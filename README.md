# OPNsense Network Configuration Labs

A running log of hands-on network configuration with the OPNsense firewall, documented as I go, mostly for my own reference, but public in case it helps someone else.

Each folder is a self-contained stage with its own README.md covering the objective, configuration steps, verification tests, problems encountered, and concepts learned.

## Labs

| # | Lab | Topics | Status |
|---|---|---|---|
| 01 | [Installation Lab](01-installation-lab/) | VirtualBox VM design, OPNsense install, WAN/LAN interface assignment, static LAN IP, setup wizard | ✅ Done |
| 02 | [Update and Firewall Basics Lab](02-update-and-firewall-basics-lab/) | Snapshots, firmware update, dashboard, default rules, default deny, stateful filtering | ✅ Done |
| 03 | [Client Network and DHCP Lab](03-client-network-dhcp-lab/) | New interface, dnsmasq DHCP, Unbound DNS, firewall rules, NAT, end-to-end testing | ✅ Done |
| 04 | [Office / Factory Segmentation Lab](04-office-factory-segmentation-lab/) | IT/OT separation, aliases, static DHCP mapping, port-specific rules, firewall logging | ✅ Done |
| 05 | [WordPress on LAMP Lab](05-wordpress-lamp-lab/) | Ubuntu Server, Apache, MariaDB, PHP, WordPress, SQL practice, database backup | 🚧 In progress |
| 06 | VPN Lab | WireGuard remote access | 🔲 Planned |
| 07 | Intrusion Detection Lab | Suricata IDS/IPS, attack detection | 🔲 Planned |

## Reference

- [OPNsense Features](opnsense-features/) - every menu in OPNsense, what it does and when to use it
- [Glossary](glossary/) - every term used in these labs, with real-world examples
- [Practical Examples](practical-examples/) - how this setup looks in a real office and a manufacturing company
- [Lab 05 Commands Reference](05-wordpress-lamp-lab/commands-reference.md) - every command used in the WordPress/LAMP lab

## Environment

- Host: Windows
- Firewall: OPNsense 26.7.4_1 on Oracle VirtualBox
- Client: Linux Mint (live session) on Oracle VirtualBox
- Web server: Ubuntu Server with Apache, MariaDB, and PHP (Lab 05)

## Notes

- Labs are numbered in the order I did them.
- Everything here runs in a local, isolated virtual environment.
