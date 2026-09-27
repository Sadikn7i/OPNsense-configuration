# Stage 2: Firmware Update, Snapshots and Firewall Rule Basics

**Lab:** Network configuration with OPNsense
**Stage:** 2 (maintenance and understanding the default rule set)
**Status:** ✅ Completed
**OPNsense version:** 26.7 → 26.7.4_1 (amd64)

---

## 1. Objective

Bring the fresh installation up to date, create rollback points before and after the change, and understand the default firewall behaviour before writing any custom rules.

---

## 2. Snapshots (rollback points)

VirtualBox snapshots freeze the full VM state so it can be restored in seconds if a change breaks something. A snapshot was taken before the update and another after it succeeded.

| Snapshot | Taken |
|---|---|
| Stage 1 – fresh install working | Before the firmware update |
| Stage 2 – updated to 26.7.4_1 | After the update was verified |

This mirrors standard change management on production firewalls: always have a known-good state to return to before touching the system.

---

## 3. Firmware Update

Performed from *System → Firmware → Status → Check for updates → Update*.

| Item | Value |
|---|---|
| From | OPNsense 26.7 |
| To | OPNsense 26.7.4_1 (the `_1` suffix is a hotfix on top of 26.7.4) |
| Packages affected | 82 (1 new, 81 upgraded) |
| Download size | 172 MiB |
| Integrity check | Passed, 0 conflicts |

Several upgraded packages are directly security-relevant: `openssl` and `openssh-portable` (encryption and remote access), `ca_root_nss` (trusted certificate authorities), `unbound` (DNS resolver), `curl`, `strongswan` and `openvpn` (VPN), and `suricata` (intrusion detection, used in a later stage). This is why firewalls must be kept current: most security fixes arrive in exactly these libraries.

The successful 172 MiB download also confirmed that the WAN side has working internet access through VirtualBox NAT.

---

## 4. Dashboard Reading

The dashboard (*Lobby → Dashboard*) provided live evidence that the Stage 1 setup works.

| Widget | Observation | Meaning |
|---|---|---|
| System Information | Timezone JST, FreeBSD 15.1 underneath | Wizard settings applied |
| Interface Statistics | 0 errors, 0 collisions on WAN and LAN | Healthy links |
| Firewall Live Log | `192.168.56.1 → 192.168.56.10:443` on LAN | Host browser reaching the web GUI over HTTPS |
| Firewall Live Log | `10.0.2.15 → 216.23.123.85:123` on WAN | Firewall syncing time via NTP (port 123) |
| Events | anti-lockout rule, "let out anything from firewall host itself" | Built-in rules matching real traffic, so pf is active |
| Gateways | WAN_DHCP → `10.0.2.2` (active) | Next hop to the internet is VirtualBox's NAT router |
| Services | Packet Filter, Unbound, Dnsmasq, Ntpd, Syslog-ng, Web GUI | Core services running |

---

## 5. Default Firewall Rules

### 5.1 LAN rules (*Firewall → Rules → LAN*)

| Interface | Version | Protocol | Source | Destination | Port | Description |
|---|---|---|---|---|---|---|
| LAN | IPv4 | any | LAN network | any | any | Default allow LAN to any rule |
| LAN | IPv6 | any | LAN network | any | any | Default allow LAN IPv6 to any rule |

Any device inside the LAN subnet may initiate any traffic to any destination. This is the typical trust model of a small office or home router.

### 5.2 Automatically generated rules

These are created by OPNsense itself and evaluated before interface rules. The ones observed in use were the **anti-lockout rule** (always permits web GUI access from LAN, so a bad rule cannot lock the admin out) and **let out anything from firewall host itself** (permits the firewall's own outbound connections such as NTP and firmware downloads). Bogon blocking on WAN, configured in the Stage 1 wizard, also lives here.

### 5.3 WAN rules

The WAN interface has no pass rules. Every connection initiated from outside is therefore dropped.

---

## 6. Key Concepts

**Default deny.** Traffic not explicitly allowed by a rule is blocked. The empty WAN rule list is what protects the network, without any configuration.

**First match wins.** Rules are evaluated top to bottom and the first matching rule decides the outcome (the "Quick" option). A block rule placed below a broad allow rule never triggers, so specific blocks must sit above general allows.

**Stateful filtering.** When an inside device opens a connection, the firewall records it in a state table and automatically permits the matching replies. This is how the firmware download returned through a WAN interface that blocks all new inbound connections.

---

## 7. Follow-up on Stage 1, Issue 5

Stage 1 recorded that the host initially could not ping the LAN address, with the root cause unconfirmed. The default LAN rule shown above permits all traffic from the LAN network, so firewall rules were not the cause. Combined with the missing ARP entry on the host, the most likely explanation is that the interface or ARP resolution was not yet ready after the interface reassignment, and it resolved once the firewall itself sent traffic to the host.

---

## 8. Next Step

Stage 3: add a separate client network behind the firewall, with dnsmasq providing DHCP.
