---
icon: lucide/book-a
---

# Glossary

| Term | Meaning |
| --- | --- |
| **VLAN** | A separate network on shared cables, identified by a tag number |
| **Trunk** | A link carrying several VLANs, tagged |
| **Tagged / untagged** | Whether a port's frames carry the VLAN tag; a port has one untagged VLAN and any number of tagged ones |
| **VLAN 999** | The parking VLAN for unused, disabled ports |
| **WAN** | The router's outside: here the home network `192.168.2.0/24` |
| **Port forward (NAT)** | Sends traffic for the WAN address on a port to an inside address |
| **Unbound** | OPNsense's DNS resolver |
| **Host override** | A fixed name → address answer in Unbound |
| **DNSSEC** | Signed DNS answers, checked by Unbound |
| **NXDOMAIN / NOERROR** | "No such name" versus "name exists" (possibly without an address) |
| **NTP / SNTP** | Time sync; chrony on the hosts, SNTP on the switch |
| **Alias (OPNsense)** | A named list of addresses used in rules |
| **SNMP** | Reads counters from the switch (read-only community) |
| **WARP** | Cloudflare's client that routes the laptop into the site |

Cluster terms (VIP, OSD, RGW, …): the
[cluster glossary](https://jurrebuunk.github.io/nix-hgd1-cluster/reference/glossary/).
