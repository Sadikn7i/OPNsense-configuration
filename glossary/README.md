# Glossary

Terms I came across while doing these labs, what they mean, and where you'd see them in a real network.

## Network Basics

| Term | Definition | In real life |
|---|---|---|
| $\color{dodgerblue}\textbf{IP address}$ | The address of a device on a network, like a postal address for computers. | *Every PC, phone, printer and machine in a company has one, e.g. `192.168.10.167`.* |
| $\color{dodgerblue}\textbf{IPv4}$ | The classic address format: four numbers from 0 to 255, e.g. `192.168.56.10`. | *Still what most office and factory networks use day to day.* |
| $\color{dodgerblue}\textbf{IPv6}$ | The newer, much longer address format, e.g. `2001:db8::1`, created because IPv4 addresses ran out. | *Used more and more by internet providers; many internal networks still run mainly on IPv4.* |
| $\color{dodgerblue}\textbf{Subnet}$ | A group of addresses that belong to the same network and can talk to each other directly. | *The office is one subnet, the factory another, guests a third.* |
| $\color{dodgerblue}\textbf{Subnet mask / /24}$ | Says which part of the address is the network and which part is the device. `/24` = `255.255.255.0`: first three numbers are the network, last number is the device (254 usable). | *Most small networks are `/24`. Office PCs share `192.168.10.x` and only the last number differs.* |
| $\color{dodgerblue}\textbf{Private IP range}$ | Address ranges reserved for internal networks, never used on the internet: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16` (RFC1918). | *Every home router and company network uses these inside.* |
| $\color{dodgerblue}\textbf{Public IP}$ | The address the internet sees, assigned by the internet provider. | *A company usually has one or a few, shared by all inside devices through NAT.* |
| $\color{dodgerblue}\textbf{MAC address}$ | A hardware address burned into each network card, e.g. `08:00:27:51:59:3c`. | *Used to identify a device on the local network, and to give it a fixed IP in DHCP.* |
| $\color{dodgerblue}\textbf{Gateway (default gateway)}$ | The device a computer sends traffic to when the destination is outside its own subnet. Usually the router. | *In an office, the gateway is the firewall's inside address, e.g. `192.168.10.1`.* |
| $\color{dodgerblue}\textbf{Router}$ | A device that connects different networks and forwards traffic between them. | *The OPNsense box connecting the office, the factory and the internet.* |
| $\color{dodgerblue}\textbf{Switch}$ | A box with many network ports that connects devices on the same network. It doesn't make decisions about traffic. | *The 24-port box in the cabinet with cables running to every desk.* |
| $\color{dodgerblue}\textbf{Network interface / NIC}$ | A network port on a device, physical or virtual. | *Each port on the firewall appliance is one interface.* |
| $\color{dodgerblue}\textbf{Port (physical)}$ | The socket you plug a network cable into. | *The WAN port and LAN ports on the front of the firewall.* |
| $\color{dodgerblue}\textbf{Port (number)}$ | A number that identifies a service on a device: 443 = HTTPS, 80 = HTTP, 53 = DNS, 123 = NTP, 22 = SSH. | *Firewall rules often allow one specific port, e.g. office PCs may reach a machine only on its data port.* |
| $\color{dodgerblue}\textbf{Broadcast}$ | A message sent to every device on the subnet at once. | *A new device asking "is there a DHCP server?" does it by broadcast.* |
| $\color{dodgerblue}\textbf{ARP}$ | How a device finds the MAC address of an IP on its own subnet ("who has 192.168.10.1?"). | *"Destination Host Unreachable" usually means nobody answered the ARP question.* |
| $\color{dodgerblue}\textbf{ICMP / ping}$ | A simple "are you there?" message used to test connectivity. | *First tool for checking whether a device or the internet is reachable. Some companies block it.* |
| $\color{dodgerblue}\textbf{TTL}$ | Time to live: a counter in each packet that drops by one at every router. | *Seeing `ttl=62` instead of 64 means the reply passed two routers.* |
| $\color{dodgerblue}\textbf{TCP / UDP}$ | The two main ways data is sent. TCP checks delivery (web, email), UDP is faster with no checks (DNS, video). | *Firewall rules can match one or the other, or "any".* |
| $\color{dodgerblue}\textbf{HTTPS}$ | Encrypted web traffic, normally on port 443. | *Websites, and the OPNsense web GUI itself.* |
| $\color{dodgerblue}\textbf{NTP}$ | Protocol for keeping clocks in sync, port 123. | *Firewalls and servers sync time so logs show the correct time.* |

## Network Types and Zones

| Term | Definition | In real life |
|---|---|---|
| $\color{dodgerblue}\textbf{LAN}$ | Local Area Network: the trusted inside network. | *The office network with staff PCs and printers.* |
| $\color{dodgerblue}\textbf{WAN}$ | Wide Area Network: the outside, facing the internet. | *The firewall port connected to the internet provider's fiber box.* |
| $\color{dodgerblue}\textbf{OPT / extra interface}$ | Any additional inside network beyond LAN (named OPT1, OPT2 until you rename it). | *Guest Wi-Fi, a server network, or a factory network.* |
| $\color{dodgerblue}\textbf{Segmentation}$ | Splitting a network into separate parts with rules between them. | *Office and factory kept apart so a hacked machine can't reach office PCs.* |
| $\color{dodgerblue}\textbf{IT network}$ | The office side: PCs, email, printers, websites. | *Staff computers and the company website.* |
| $\color{dodgerblue}\textbf{OT network}$ | Operational technology: the factory side, with machines, controllers and sensors. | *CNC machines, PLCs, cameras on the production line.* |
| $\color{dodgerblue}\textbf{DMZ}$ | A separate network for servers that the internet is allowed to reach. | *The web server sits in the DMZ so outside visitors never touch the office network.* |
| $\color{dodgerblue}\textbf{VLAN}$ | A way to split one physical switch into several separate networks. | *One switch carrying office, guest and camera networks separately.* |
| $\color{dodgerblue}\textbf{LAGG}$ | Link aggregation: combining several cables into one faster or more reliable link. | *Two cables between the firewall and the main switch, so one can fail without an outage.* |
| $\color{dodgerblue}\textbf{Bogon}$ | Address ranges that should never appear on the internet (unassigned or reserved). | *Firewalls block them on WAN because such traffic is always suspicious.* |

## Services

| Term | Definition | In real life |
|---|---|---|
| $\color{dodgerblue}\textbf{DHCP}$ | Automatically gives devices an IP address, subnet mask, gateway and DNS server. | *A new laptop plugged in at a desk works immediately without manual setup.* |
| $\color{dodgerblue}\textbf{DHCP lease}$ | The time a device may keep its address before renewing it. | *Set to 1 day in my lab (86400 seconds).* |
| $\color{dodgerblue}\textbf{DHCP range / pool}$ | The set of addresses DHCP is allowed to hand out, e.g. `.100` to `.200`. | *Kept separate from fixed addresses used by servers and printers.* |
| $\color{dodgerblue}\textbf{Static mapping}$ | A fixed address always given to a specific device, based on its MAC address. | *Printers, servers and factory machines, which must never change address.* |
| $\color{dodgerblue}\textbf{DNS}$ | Translates names like `google.com` into IP addresses. | *Staff type names, not numbers. If DNS fails, "the internet is down" even when it isn't.* |
| $\color{dodgerblue}\textbf{DNS resolver}$ | The server that looks up names on behalf of devices and caches the answers. | *The firewall answers DNS for the whole office.* |
| $\color{dodgerblue}\textbf{Unbound}$ | The DNS resolver built into OPNsense. | *Handles DNS in my lab.* |
| $\color{dodgerblue}\textbf{dnsmasq}$ | A small service that does DHCP and can also do DNS. | *Hands out addresses on my CLIENTS network. Common on small business firewalls, home routers and Raspberry Pis.* |
| $\color{dodgerblue}\textbf{Kea DHCP}$ | Another DHCP server available in OPNsense, aimed at larger setups. | *An alternative to dnsmasq; you pick one per network.* |
| $\color{dodgerblue}\textbf{SSH}$ | Encrypted remote login to a device's command line, port 22. | *How an admin manages a headless server or Raspberry Pi.* |
| $\color{dodgerblue}\textbf{VPN}$ | An encrypted tunnel connecting a device or site to a network over the internet. | *Connecting a warehouse to the main plant, or an admin working from home.* |
| $\color{dodgerblue}\textbf{WireGuard}$ | A modern, fast VPN built into OPNsense. | *Remote access for staff, or site-to-site links.* |

## Firewall

| Term | Definition | In real life |
|---|---|---|
| $\color{dodgerblue}\textbf{Firewall}$ | A device or software that decides which traffic is allowed and which is blocked. | *The OPNsense box between the internet and the company.* |
| $\color{dodgerblue}\textbf{Firewall rule}$ | One instruction: which traffic (from where, to where, which port) to pass or block. | *"Office PCs may reach the machine data server on port 502."* |
| $\color{dodgerblue}\textbf{Pass / Block}$ | The two basic actions a rule can take. | *Most rules pass specific traffic; everything else is blocked by default.* |
| $\color{dodgerblue}\textbf{Default deny}$ | Anything not explicitly allowed is blocked. | *The WAN has no pass rules, so nothing from outside can start a connection in.* |
| $\color{dodgerblue}\textbf{First match wins (Quick)}$ | Rules are checked top to bottom, and the first matching rule decides. | *A block rule must sit above a broad allow rule, otherwise it never triggers.* |
| $\color{dodgerblue}\textbf{Stateful filtering}$ | The firewall remembers connections started from inside and lets their replies back in. | *Why web pages load even though WAN blocks all new incoming traffic.* |
| $\color{dodgerblue}\textbf{State table}$ | The firewall's list of active connections. | *Used when troubleshooting "why was this allowed or blocked?"* |
| $\color{dodgerblue}\textbf{Anti-lockout rule}$ | A built-in rule that always allows the web GUI from the LAN. | *Stops an admin locking themselves out with a bad rule.* |
| $\color{dodgerblue}\textbf{Automatically generated rules}$ | Rules OPNsense creates itself, checked before your own. | *Allowing DHCP, the firewall's own traffic, bogon blocking.* |
| $\color{dodgerblue}\textbf{Alias}$ | A named group of addresses or ports used in rules. | *"FACTORY_MACHINES" instead of typing ten IP addresses into every rule.* |
| $\color{dodgerblue}\textbf{NAT (outbound)}$ | Replacing private inside addresses with the public address when traffic leaves. | *How 50 office PCs share one internet connection.* |
| $\color{dodgerblue}\textbf{Port forwarding (inbound NAT)}$ | Sending traffic that arrives on a public port to a specific inside server. | *Making the company website on the inside web server reachable from the internet.* |
| $\color{dodgerblue}\textbf{pf (packet filter)}$ | The firewall engine inside OPNsense. `pfctl -d` disables it, `pfctl -e` enables it. | *Only turned off briefly for testing, never left off.* |
| $\color{dodgerblue}\textbf{Live log}$ | Real-time view of traffic the firewall passes or blocks. | *First place to look when something is unexpectedly blocked.* |
| $\color{dodgerblue}\textbf{IDS / IPS}$ | Intrusion detection / prevention: watches traffic for attacks and alerts or blocks them. | *Suricata in OPNsense, detecting scans or known attack patterns.* |
| $\color{dodgerblue}\textbf{High availability / CARP}$ | Two firewalls working as a pair; if one fails, the other takes over. | *Factories that can't afford to lose their network.* |

## OPNsense

| Term | Definition | In real life |
|---|---|---|
| $\color{dodgerblue}\textbf{OPNsense}$ | Open-source firewall and router operating system, based on FreeBSD. | *Runs on a small appliance in the company's network cabinet.* |
| $\color{dodgerblue}\textbf{Appliance}$ | A small dedicated computer built to run the firewall, with several network ports. | *The fanless box on the shelf next to the fiber box.* |
| $\color{dodgerblue}\textbf{Web GUI}$ | The browser-based control panel of OPNsense. | *Opened from an inside PC at an address like `https://192.168.1.1`.* |
| $\color{dodgerblue}\textbf{Console menu}$ | The text menu on the firewall itself (options 0 to 13). | *Used when the web GUI isn't reachable, e.g. to fix interfaces or reset the password.* |
| $\color{dodgerblue}\textbf{em0 / em1 / em2}$ | Names FreeBSD gives network cards: "em" = the Intel driver, the number = the order found. | *On real hardware you'd see names like `igc0` or `re0`.* |
| $\color{dodgerblue}\textbf{Interface assignment}$ | Deciding which physical port has which role (WAN, LAN, OPT). | *First thing checked on a new appliance; swapped ports mean nothing works.* |
| $\color{dodgerblue}\textbf{Firmware update / hotfix}$ | Updates to OPNsense and its components. A `_1` suffix is a small fix on top of a release. | *Done regularly, ideally after a snapshot or backup, because security fixes arrive this way.* |
| $\color{dodgerblue}\textbf{Self-signed certificate}$ | An HTTPS certificate the firewall made for itself, not signed by a trusted authority. | *Why the browser warns when opening the web GUI. Normal for internal devices.* |
| $\color{dodgerblue}\textbf{Fingerprint (SHA-256)}$ | A unique code identifying a certificate. | *Comparing the browser's fingerprint with the console's proves you're talking to the real firewall.* |

## Virtualization and Lab Setup

| Term | Definition | In real life |
|---|---|---|
| $\color{dodgerblue}\textbf{Virtual machine (VM)}$ | A computer simulated in software, running inside a real one. | *Lets you build and test networks without buying hardware.* |
| $\color{dodgerblue}\textbf{Host / guest}$ | The host is the real computer; the guest is the VM running on it. | *My laptop is the host; OPNsense and Mint are guests.* |
| $\color{dodgerblue}\textbf{Hypervisor}$ | The software that runs VMs. Type 2 runs on top of an OS (VirtualBox); type 1 is the OS itself (Proxmox, VMware ESXi). | *Companies run servers on type 1 hypervisors; type 2 is for learning and desktops.* |
| $\color{dodgerblue}\textbf{VirtualBox}$ | A free type 2 hypervisor for Windows, Mac and Linux. | *What this lab runs on.* |
| $\color{dodgerblue}\textbf{Proxmox}$ | A free type 1 hypervisor managed from a browser. | *Popular for home labs and small companies; often runs OPNsense as a VM.* |
| $\color{dodgerblue}\textbf{ISO}$ | A file containing an installation disk image. | *Downloaded to install OPNsense or Linux.* |
| $\color{dodgerblue}\textbf{Snapshot}$ | A saved state of a VM that can be restored in seconds. | *Taken before updates or big changes, as a rollback point.* |
| $\color{dodgerblue}\textbf{NAT adapter}$ | VirtualBox network mode that gives a VM internet access through the host. | *Plays the role of the internet provider in this lab.* |
| $\color{dodgerblue}\textbf{Host-only adapter}$ | VirtualBox network shared only between the host and VMs. | *Plays the role of the admin's management network.* |
| $\color{dodgerblue}\textbf{Internal network}$ | VirtualBox network shared only between VMs, like a switch. | *Plays the role of the office switch (`lab-lan`).* |
| $\color{dodgerblue}\textbf{Live session}$ | Running an OS straight from the ISO without installing it. | *Quick way to get a test client.* |
| $\color{dodgerblue}\textbf{Host key}$ | The key that releases the mouse and keyboard from a VM (Right Ctrl by default). | *Set to Left Alt on my JIS keyboard, which has no Right Ctrl.* |
| $\color{dodgerblue}\textbf{UFS / ZFS}$ | Two file systems for FreeBSD. UFS is simple and light; ZFS adds snapshots and redundancy but needs more RAM. | *UFS for a small VM; ZFS on real appliances with more memory.* |

## Hardware and Real-World Devices

| Term | Definition | In real life |
|---|---|---|
| $\color{dodgerblue}\textbf{ONU (fiber box)}$ | The box the internet provider installs where the fiber enters the building. | *The first box in the chain, connected to the firewall's WAN port.* |
| $\color{dodgerblue}\textbf{Access point (AP)}$ | A device that provides Wi-Fi, connected to the switch by cable. | *Mounted on office ceilings for phones and laptops.* |
| $\color{dodgerblue}\textbf{Raspberry Pi}$ | A credit-card-sized, low-cost Linux computer, booting from a microSD card. | *Sensor monitoring, floor dashboards, barcode stations, test devices.* |
| $\color{dodgerblue}\textbf{ARM}$ | The processor type in Raspberry Pis and phones: cheap and low power. | *Why a Pi can run all day on a phone charger.* |
| $\color{dodgerblue}\textbf{PLC}$ | Programmable logic controller: the small computer controlling a factory machine. | *Sits on the factory network, usually with a fixed IP and strict rules.* |
| $\color{dodgerblue}\textbf{Headless}$ | Running a device without a screen, managed over the network. | *Most servers and Raspberry Pis in a company.* |

## Commands Used

| Command | Where | What it shows |
|---|---|---|
| `ipconfig` | Windows | *The PC's IP address, mask and gateway for each adapter.* |
| `ip a` | Linux | *Each network interface and its IP address.* |
| `ip route` | Linux | *The routing table, including the default gateway.* |
| `ping <address>` | Windows / Linux | *Whether a device replies, and how fast.* |
| `arp -a` | Windows / FreeBSD | *Which IPs on the local network have been matched to MAC addresses.* |
| `nmcli device connect <if>` | Linux (NetworkManager) | *Brings an interface up and requests an address via DHCP.* |
| `sudo ip addr add <ip>/24 dev <if>` | Linux | *Adds an IP address manually, for testing.* |
| `sudo ip addr flush dev <if>` | Linux | *Removes all IP addresses from an interface.* |
| `ifconfig <if>` | FreeBSD (OPNsense shell) | *Interface status and addresses.* |
| `pfctl -d` / `pfctl -e` | FreeBSD (OPNsense shell) | *Disables / enables the firewall engine.* |
| `Get-FileHash <file> -Algorithm SHA256` | Windows PowerShell | *The checksum of a download, to verify it.* |
