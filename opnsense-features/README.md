# OPNsense Menu Guide

Every menu and sub-menu in the OPNsense web GUI (version 26.7), what it is, when you'd use it, and where you actually do the work. Examples use a small manufacturing company with an office network and a factory network. Items marked **(used)** are ones I've already used in Labs 01 to 03.

## Where Things Are Done

There are four different places you work in during these labs. The "Where" column in every table below refers to these.

| Place | What it is | In the lab | In a real company |
|---|---|---|---|
| **VirtualBox** | The program that runs the virtual machines on my laptop. | Adding network adapters, choosing NAT / Host-only / Internal Network, snapshots, starting and stopping VMs. | Doesn't exist. Replaced by the physical box, real cables and real switches. Plugging a cable into a port is the real version of "Adapter 3 = Internal Network". |
| **Web GUI** | The OPNsense website at `https://192.168.56.10`, opened from a browser on the LAN side. | Almost all configuration: interfaces, rules, dnsmasq, updates. | Exactly the same. This is where an admin spends 95% of the time. |
| **OPNsense console** | The black text menu (options 0 to 13) on the firewall itself. | Assigning em0/em1, setting the LAN IP, resetting the password, the shell (option 8). | A monitor and keyboard plugged into the box, or a serial cable. Used only when the web GUI can't be reached: first setup, a locked-out admin, or a broken LAN setting. |
| **Client terminal** | The command line on a device behind the firewall (Mint's terminal, Windows cmd/PowerShell). | `ip a`, `ping`, `nmcli`, `ipconfig`, `arp -a`. | Same commands on staff PCs, servers or a Raspberry Pi, to test whether the network works from the user's side. |

A simple rule: **VirtualBox builds the hardware, the console gets the firewall reachable, the web GUI configures everything, and the client terminal proves it works.**

---

## Lobby

The home area, the first thing you see after logging in.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Dashboard **(used)** | A page of widgets: version, uptime, CPU and memory, interfaces with their IPs, gateway status, live firewall log, and the list of running services (green = running). | Your daily health check. If the WAN gateway shows as down, the internet line is the problem. If a service is red, it has stopped. You can add or remove widgets to show what matters to you. | Web GUI |
| License | The software license text. | Almost never. | Web GUI |
| Password | Change the password of the account you're logged in with. | Right after the first login, and when company policy requires regular changes. | Web GUI |
| Logout | Ends your session. | When leaving a shared PC, so nobody else gets into the firewall. | Web GUI |

---

## Reporting

Graphs and statistics. This menu answers "what happened?" and "who is using the network?"

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Health | Long-term graphs of CPU, memory, traffic, packet loss and gateway quality, going back weeks. | A manager says "the internet was slow yesterday at 3 pm". You check whether the line was full, packets were lost, or the firewall itself was overloaded at that time. | Web GUI |
| Insight | Traffic history broken down by interface, device, port and protocol. Needs NetFlow turned on. | Finding which PC used the most bandwidth last week, or noticing that a factory machine suddenly started sending lots of data. | Web GUI |
| NetFlow | The setting that records traffic flows so Insight has data to show. | Enable it once when you want traffic history. | Web GUI |
| Traffic | A live graph of traffic right now, per interface, with the top devices. | Someone says "the network is slow right now". You open this and see immediately which device is downloading. | Web GUI |
| Unbound DNS | Statistics on DNS lookups: most requested names, blocked names, which devices ask the most. | Spotting a device that keeps looking up a strange domain, which can be a sign of malware. | Web GUI |

---

## System

Settings for the firewall itself, rather than for the network.

### Access

Who can log into the firewall and what they're allowed to do.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Users | The list of accounts that can log into the firewall. Root is one of them. | Each IT person gets their own account instead of sharing root, so the History and Audit logs show who changed what. | Web GUI |
| Groups | Collections of users that share the same permissions. | An "admins" group with full access and a "helpdesk" group for colleagues who only need to read logs. | Web GUI |
| Privileges | The exact pages and actions a user or group may access. | Allowing a helpdesk colleague to see the firewall log and the dnsmasq leases, but not change any rules. | Web GUI |
| Servers | Connections to external login systems like LDAP / Active Directory or RADIUS. | In a company with a central user directory (for example Windows Active Directory), staff log into the firewall or VPN with their normal company password. When someone leaves, disabling them in one place removes all their access. | Web GUI |
| Tester | Tries a login against one of those servers. | Checking that the Active Directory connection works before relying on it for VPN logins. | Web GUI |

### Configuration

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Backups | Download the complete configuration as one XML file, or restore one. Can also send backups automatically to cloud storage. | Before any update or big change. If the firewall hardware dies, you install OPNsense on a new box and restore this file, and everything is back. On real hardware, this is the main safety net (the equivalent of my VirtualBox snapshots). | Web GUI |
| Defaults | Resets the firewall to factory settings. | Reusing an old firewall for a new site. It erases everything, so take a backup first. | Web GUI (or console option 4) |
| History | A record of every configuration change: when, by which user, and what exactly changed. You can compare versions and roll back. | "The factory lost network access after lunch." You open History, see the rule someone changed at 12:40, and revert it. | Web GUI |
| Wizard **(used)** | The initial setup wizard (hostname, DNS, WAN, LAN, password). | First setup, or to quickly redo basic settings. | Web GUI |

### Firmware

Updating OPNsense and adding features.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Status **(used)** | Shows the installed version and has the "Check for updates" button. | Monthly update routine. | Web GUI (or console option 12) |
| Settings | Which download mirror and which release type (community, business, development) to use. | Choosing a mirror in Japan or Korea for faster downloads. | Web GUI |
| Changelog | Release notes for each version. | Reading what an update changes before installing it on a production firewall, especially whether it touches something you rely on. | Web GUI |
| Updates **(used)** | The list of packages that will be updated, and the button to run the update. | During the update, after checking the changelog and taking a backup. | Web GUI |
| Plugins | Optional add-ons you can install: for example Zenarmor (application filtering), CrowdSec (blocking known attackers), HAProxy / Nginx (reverse proxy), ACME client (free Let's Encrypt certificates), extra monitoring tools. | Adding a feature that isn't built in, e.g. ACME for a proper certificate on the web GUI. | Web GUI |
| Packages | Every software package installed on the system, with versions. You can reinstall or lock individual packages. | Advanced troubleshooting, or checking the exact version of a component like Suricata. | Web GUI |
| Reporter | Sends crash reports to the OPNsense developers. | If OPNsense reports a crash after an update, you can send it to help fix the bug. | Web GUI |
| Log File | The log of update and plugin installations. | An update failed halfway: check here what went wrong. | Web GUI |

### Gateways

A gateway is the next router OPNsense sends traffic to. My `WAN_DHCP` (10.0.2.2) is one.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Configuration | The list of gateways, with monitoring settings: OPNsense pings each gateway constantly to check whether it's alive. | A factory with two internet lines (fiber plus a mobile backup). You define both gateways and see which one is up. | Web GUI |
| Group | Combines gateways into a group for failover or load balancing. | If the fiber line fails, traffic switches automatically to the mobile backup line. Production keeps its connection without anyone touching anything. | Web GUI |
| Log File | Events when a gateway goes up or down. | "When exactly did the internet drop last night, and for how long?" | Web GUI |

### High Availability

Two OPNsense boxes working as a pair.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Settings | Links the main firewall to a backup firewall and synchronises configuration and connection states between them. | Factories that can't afford any downtime. If the main firewall's hardware dies, the backup takes over within seconds, and machines don't even notice. This is the "two routers" situation from the interview. | Web GUI |
| Status | Shows which box is currently active (master) and which is waiting (backup), and whether they're in sync. | Checking the pair is healthy, and during planned maintenance when you deliberately switch to the backup. | Web GUI |

### Routes

Manual directions for networks that OPNsense can't reach by itself.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Configuration | Static routes: "network X is reachable through router Y". | A second building has its own router, and its network 192.168.50.0/24 sits behind it. You add a route so OPNsense knows to send that traffic to that router. | Web GUI |
| Status | The full routing table currently in use. | Troubleshooting why traffic to a network goes the wrong way. | Web GUI |
| Log File | Events related to routing. | Checking when routes were added or changed. | Web GUI |

### Settings

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Administration | Web GUI settings (port, HTTPS certificate, which interfaces it listens on, session timeout), SSH access, console options. | Restricting the web GUI to the admin network so office and factory devices can't even see the login page. Enabling SSH so you can manage the firewall from a terminal. | Web GUI |
| Cron | Scheduled tasks that run automatically. | Nightly configuration backups, scheduled update checks, regular blocklist refreshes. | Web GUI |
| General | Hostname, domain, time zone, language, and the DNS servers the firewall itself uses. | Set during the wizard; change it if the company domain changes or you want different upstream DNS servers. | Web GUI |
| Logging | How long logs are kept, how big they may grow, and sending logs to an external log server. | Keeping firewall logs for months on a separate server for security audits, so an attacker can't erase them from the firewall. | Web GUI |
| Miscellaneous | Hardware options such as power saving, cryptography acceleration, thermal sensors, and some system behaviour. | Rarely, usually when setting up a specific appliance model. | Web GUI |
| Tunables | Low-level operating system (FreeBSD kernel) values. | Only when documentation or support tells you to, for example to fix a specific network card issue. | Web GUI |

### Snapshots

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Snapshots | Snapshots of the whole system, like VirtualBox snapshots but inside OPNsense. Only works when OPNsense was installed on ZFS. | On real appliances installed with ZFS: take one before an update, roll back if it fails. My lab VM uses UFS, so this is empty and I use VirtualBox snapshots instead. | Web GUI (lab: VirtualBox) |

### Trust

Certificates, which prove identity and encrypt connections.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Authorities | Certificate Authorities (CAs): the "issuers" that sign certificates. You can create your own company CA. | Creating an internal company CA to issue certificates for VPN users and internal servers. | Web GUI |
| Certificates | The certificates themselves, for the web GUI, VPN server, VPN users and other services. | Replacing the self-signed GUI certificate so browsers stop warning, or creating a certificate for each employee's VPN access. | Web GUI |
| Revocation | Lists of cancelled certificates. | An employee leaves or loses their laptop: you revoke their VPN certificate so it can't be used anymore. | Web GUI |
| Settings | General certificate options, such as how trusted authorities are stored and updated. | Rarely changed. | Web GUI |

### Log Files

Logs about the firewall system itself (not traffic, that's under Firewall).

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Audit | Security events: logins, failed logins, configuration changes. | "Who logged into the firewall at 2 am?" Repeated failed logins from one address can mean someone is trying to break in. | Web GUI |
| Backend | Messages from configd, the engine that applies settings when you click Apply. | A setting doesn't seem to take effect: look here for errors. | Web GUI |
| Boot | Everything printed while the system starts. | The firewall doesn't come up properly after a reboot, or a network card isn't detected. | Web GUI |
| General | General system messages. | First place to look when something behaves strangely. | Web GUI |
| Web GUI | Messages from the web interface itself. | The web GUI shows errors or won't load a page. | Web GUI |

### Diagnostics

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Activity | Running processes and their CPU and memory use, like Task Manager. | The firewall is slow: see which process uses the CPU, for example intrusion detection scanning heavy traffic. | Web GUI |
| Services | All services with start, stop and restart buttons. | dnsmasq stopped handing out addresses: restart it here. | Web GUI |
| Statistics | System counters: memory, disk, interrupts, network buffers. | Deeper performance troubleshooting. | Web GUI |

---

## Interfaces

Everything about the network ports.

### The interfaces I created: WAN, LAN and CLIENTS

| Interface | Port | Address | What it is in my lab | What it would be in a real company |
|---|---|---|---|---|
| WAN | em0 (VirtualBox Adapter 1, NAT) | 10.0.2.15, from DHCP | The "outside". VirtualBox's NAT plays the internet provider. Everything from here is untrusted and blocked by default. | The port with the cable from the fiber box (ONU). Faces the internet. |
| LAN | em1 (VirtualBox Adapter 2, Host-only) | 192.168.56.10/24, static | My management network. Only my laptop is on it, and I open the web GUI from here. It has the anti-lockout rule, so I can't lock myself out. | The admin/management network: the IT person's PC and the network equipment. Many companies keep management separate from normal staff. |
| CLIENTS | em2 (VirtualBox Adapter 3, Internal Network `lab-lan`) | 192.168.10.1/24, static; devices get .100 to .200 from dnsmasq | The network of ordinary devices. Mint lives here and gets its address from dnsmasq. Its rule lets CLIENTS network out. | The office network: staff PCs, printers, Wi-Fi. In Lab 04, a FACTORY interface is added the same way. |

To add another network like CLIENTS: in the lab, first add the adapter in **VirtualBox** (VM powered off), then assign it in the **web GUI**. In real life, you plug a cable into a free port on the appliance, then assign it in the web GUI.

### Other Interfaces items

| Item | What it is | When you use it | Where |
|---|---|---|---|
| [CLIENTS] / [LAN] / [WAN] **(used)** | The settings page for each assigned interface: enable, IPv4/IPv6 type, address, MTU, and on WAN the "block private / bogon networks" options. | Setting a new FACTORY interface to 192.168.20.1/24. | Web GUI (LAN IP can also be set with console option 2) |
| Assignments **(used)** | Links physical ports (em0, em1, em2...) to roles and names (WAN, LAN, CLIENTS). | The first step for every new network. Also where I fixed the swapped WAN/LAN. | Web GUI (or console option 1) |
| Devices | Creates virtual interfaces on top of physical ports: VLAN (several networks on one cable), LAGG (several cables as one), Bridge (join interfaces), Point-to-Point (PPPoE, which some Japanese fiber services use on WAN), GIF / GRE / VXLAN (tunnels), Loopback. | VLANs are the big one: with a managed switch, office, factory, cameras and guests can all run over one cable to the firewall, each as its own network. | Web GUI |
| Neighbors: Automatic Discovery | Devices OPNsense has automatically seen on its networks, with their IP and MAC addresses. | Finding what's actually connected, e.g. spotting an unknown device someone plugged in on the factory floor. | Web GUI |
| Neighbors: Static Assignment | Fixed IP-to-MAC entries. | Pinning a critical machine's MAC so another device can't impersonate its IP. | Web GUI |
| Neighbors: Discovery Log | History of discovered devices. | "When did this device first appear on the network?" | Web GUI |
| Overview | All interfaces with status (up/down), addresses, speed, and traffic and error counters. | Quick check whether a port has a cable connected and whether errors are increasing, which can mean a bad cable. | Web GUI |
| Settings | Global interface options, such as hardware offloading and IPv6 behaviour. | Troubleshooting odd performance problems with certain network cards. | Web GUI |
| Virtual IPs: Settings | Extra IP addresses on an interface, including the shared CARP address used by a High Availability pair. | With two firewalls, the office's gateway is a shared virtual address that moves to whichever firewall is active. | Web GUI |
| Virtual IPs: Status | Shows the current state of those virtual addresses (which box holds them). | Checking which firewall in a pair currently owns the gateway address. | Web GUI |
| Wireless: Devices | Turns a Wi-Fi card inside the firewall into an access point. | Very small sites where the firewall also provides Wi-Fi. Most companies use separate access points instead. | Web GUI |
| Diagnostics | Test tools run from the firewall itself: ARP Table, DNS Lookup, NDP Table, Netstat, Packet Capture, Ping, Port Probe, Traceroute. | A machine isn't reachable: ping it from the firewall, check the ARP table to see if it's even on the network, probe its port, or capture packets to see exactly what arrives. | Web GUI |

---

## Firewall

The core of OPNsense: deciding what traffic is allowed.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Aliases | Named lists of IPs, networks, ports or domain names to use in rules. | Create `FACTORY_MACHINES` with ten machine IPs, then write one rule using the alias. When an eleventh machine arrives, you add it to the alias, not to every rule. | Web GUI |
| Categories | Coloured labels for rules. | Tagging all factory rules so they're easy to find in a long rule list. | Web GUI |
| Groups | Groups of interfaces, so one rule applies to all of them. | One rule "block access to the firewall GUI" applied to office, factory and guest at once. | Web GUI |
| NAT: Port Forward | Sends traffic arriving on a WAN port to a server inside. | Making the company's WordPress server reachable from the internet on port 443 (Lab 05). | Web GUI |
| NAT: One-to-One | Maps a whole public IP to one inside device. | A company with several public IPs gives one to its web server. | Web GUI |
| NAT: Outbound / Source NAT | How inside addresses are translated when traffic leaves. Automatic by default; that's what let Mint reach the internet. | Rarely changed; used when certain traffic must leave with a specific public IP. | Web GUI |
| NAT: NPTv6 | Prefix translation for IPv6. | Advanced IPv6 setups only. | Web GUI |
| Rules **(used)** | Pass and block rules for each interface, checked top to bottom, plus floating rules that apply across several interfaces. | Everything from "office may reach the internet" to "factory may not reach the office" to "only the planning PC may reach machine port 502". | Web GUI |
| Shaper | Bandwidth limits and priorities. | Guest Wi-Fi limited so guests can't slow down the production data traffic; video calls given priority over downloads. | Web GUI |
| Settings | Global firewall behaviour: advanced options (state limits, timeouts, bogon updates), normalization (cleaning malformed packets), and schedules (time windows for rules). | Schedules: guest Wi-Fi only works during office hours. The rest usually stays default. | Web GUI |
| Log Files | Live View (real-time passed and blocked traffic), plus overview and plain views. | Testing a new rule: generate traffic from a client, watch Live View, and see which rule matched. The most useful troubleshooting page in OPNsense. | Web GUI |
| Diagnostics | States (every active connection), sessions, statistics, pfTop, pfInfo, alias contents. | "Is the planning PC connected to the machine right now?" Look it up in States. | Web GUI (pfTop also console option 9) |

---

## VPN

Encrypted tunnels over the internet.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| IPsec | The standard VPN between firewalls, supported by almost every vendor. | Connecting to a supplier or partner company that uses a different brand of firewall, or linking two plants permanently. | Web GUI |
| OpenVPN | A widely supported VPN that uses certificates, often for staff laptops. | Employees connecting from home or while travelling, each with their own certificate that can be revoked when they leave. | Web GUI |
| WireGuard | A modern, simple and fast VPN. | IT working remotely, or a fast link between the main plant and a warehouse. Usually the easiest to set up (Lab 06). | Web GUI |

---

## Services

Programs running on the firewall that serve the network.

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Captive Portal | A web page shown before a device gets internet access (login, voucher code or "accept the terms"). Sub-items: Administration, Sessions, Vouchers, Log. | Guest Wi-Fi for visitors and truck drivers: they get a voucher code valid for one day. | Web GUI |
| DHCRelay | Forwards DHCP requests from one network to a DHCP server on another network. | The company already runs DHCP on a Windows server. The firewall passes requests from the factory network to that server instead of answering itself. | Web GUI |
| Dnsmasq DNS & DHCP **(used)** | Hands out IP addresses and can answer local DNS names. Sub-items: General, Domains, Hosts (fixed addresses and names), DHCP ranges, DHCP options, DHCP boot, DHCP tags, Leases, Log File. | Every new PC getting an address automatically; fixed addresses for printers and machines under Hosts; checking Leases when a device "isn't on the network". | Web GUI |
| Intrusion Detection | Suricata: inspects traffic for known attacks, and can alert or block. Sub-items: Administration, Policy, Log File. | Detecting someone scanning the factory network, or malware trying to reach known bad servers (Lab 07). | Web GUI |
| Kea DHCP | An alternative DHCP server built for larger networks and firewall pairs. Sub-items for DHCPv4, DHCPv6, Control Agent, Leases, Log. | Networks with thousands of devices, or two firewalls sharing DHCP. Use either dnsmasq or Kea for a network, not both. | Web GUI |
| Monit | Watches the system and services, and sends email alerts. Sub-items: Settings, Status. | Getting an email when disk space runs low, memory is high, or a service stops, before users notice. | Web GUI |
| Network Time | NTP: keeps the firewall's clock correct and can serve time to the network. Sub-items: General, GPS, PPS, Status, Log. | Factory machines, cameras and servers syncing their clocks to the firewall, so logs from different devices line up when investigating an incident. | Web GUI |
| OpenDNS | Uses the OpenDNS service (from Cisco) for web filtering. | Blocking categories like gambling or adult sites through an external service. | Web GUI |
| Router Advertisements | Tells devices how to configure IPv6 addresses. | Only when you run IPv6 on inside networks. | Web GUI |
| Unbound DNS **(used)** | The DNS resolver that answers name lookups for the network. Sub-items: General, Advanced, Access Lists, Blocklist, Overrides, Query Forwarding, DNS over TLS, Statistics, Log. | Overrides for local names like `cnc-01.factory`; Blocklist to stop all devices reaching known malware and ad domains; DNS over TLS to encrypt lookups to the outside. | Web GUI |

---

## Power

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Reboot | Restarts the firewall. | After some updates. Done outside working hours, since the whole company loses network access for a minute or two. | Web GUI (or console option 6) |
| Power Off | Shuts down cleanly. | Before moving the hardware or during planned electrical work. Always better than pulling the plug. | Web GUI (or console option 5) |

## Help

| Item | What it is | When you use it | Where |
|---|---|---|---|
| Documentation / User Manual | The official docs at docs.opnsense.org. | When a menu doesn't match a video, which usually means the video is from an older version. | Browser |
| Forum / Support | The community forum and paid support. | Stuck on a problem other people may have solved. | Browser |

---

## Services on the Dashboard

The background programs at the bottom of the dashboard. Green means running.

| Service | What it does |
|---|---|
| Configd | Applies configuration changes when you click Apply. |
| Cron | Runs scheduled tasks (backups, updates, log rotation). |
| Dnsmasq DNS/DHCP | Hands out addresses on CLIENTS. |
| Hostwatch | Keeps track of devices seen on the networks (feeds Neighbors discovery). |
| Ntpd | Keeps time in sync. |
| Packet Filter | pf, the actual firewall. If it stops, rules aren't enforced. |
| Syslog-ng | Collects and stores logs. |
| System routing | Handles routes and gateways. |
| System tunables | Applies low-level system settings. |
| Unbound | DNS resolver. |
| Users and Groups | Account management. |
| Web GUI | The web interface itself. |

---

## What a New Admin Actually Uses Day to Day

Out of everything above, most real work happens in a handful of places:

- Lobby > Dashboard: daily health check
- Interfaces > Assignments and each interface page: adding networks
- Firewall > Rules and Aliases: controlling traffic
- Firewall > Log Files > Live View: testing and troubleshooting rules
- Firewall > NAT > Port Forward: publishing a server
- Services > Dnsmasq DNS & DHCP: addresses, fixed mappings, leases
- Services > Unbound DNS: local names and DNS problems
- System > Configuration > Backups and History: safety net before and after changes
- System > Firmware: updates
- Interfaces > Diagnostics: ping, ARP table and packet capture when something doesn't work
