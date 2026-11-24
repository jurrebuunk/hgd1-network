---
icon: lucide/map-pin
---

# Addresses

The network's own addresses: VLANs, the router and switch, switch ports. DNS
names are in `lan.buunk.org`.

!!! cluster "On the cluster docs: hosts, VMs and service addresses"

    Every host, VM, floating IP (S3, k3s API, monitoring, Platform API) and their MACs, from `inventory.nix`.

    [Cluster addresses](https://jurrebuunk.github.io/nix-hgd1-cluster/reference/addresses/){ .md-button }

## VLANs

| VLAN | Name | Subnet | Gateway | DHCP |
| --- | --- | --- | --- | --- |
| 10 | mgmt | `10.10.10.0/24` | `10.10.10.1` | `.100–.199` |
| 20 | infra | `10.10.20.0/24` | `10.10.20.1` | no |
| 30 | storage | `10.10.30.0/24` | none (not routed) | no |
| 40 | k8s | `10.10.40.0/24` | `10.10.40.1` | no |
| 100 | users | `10.10.100.0/24` | `10.10.100.1` | `.100–.249` |
| – | home / WAN | `192.168.2.0/24` | home router | home router |

Kept free: `10.42.0.0/16` (k3s pods) and `10.43.0.0/16` (k3s services).

## Router and switch

| Name | mgmt (10) | infra (20) | k8s (40) | users (100) | WAN |
| --- | --- | --- | --- | --- | --- |
| `hgd1-fw01` (OPNsense) | `10.10.10.1` | `10.10.20.1` | `10.10.40.1` | `10.10.100.1` | `192.168.2.12` |
| `hgd1-sw01` (Aruba) | `10.10.10.2` | – | – | – | – |

| Device | MAC |
| --- | --- |
| OPNsense WAN (`re0`) | `90:1b:0e:fa:70:dd` (reserved on the home router) |

## Reserved ranges

| Range | For |
| --- | --- |
| `10.10.10.100–199` | DHCP on mgmt |
| `10.10.40.200–249` | Kubernetes load balancers (later, LAN-only services) |
| `10.10.100.100–249` | DHCP on users |
| `10.10.100.50` | `win11-gaming` on `hgd1-gpu1` (Sunshine forwards) |

## Switch ports

| Port | Connected to |
| --- | --- |
| 1 | Management recovery (untagged 10) |
| 2, 4, 48 | Wired users (untagged 100) |
| 5 / 7 / 9 | `hgd1-hv01` / `hgd1-hv02` / `hgd1-hv03` (untagged 20, tagged 30, 40) |
| 14 | OPNsense trunk `igb0` (untagged 10, tagged 20, 40, 100) |
| 16, 18 | Spare router links, disabled |
| 19 | `hgd1-gpu1` (untagged 20, tagged 100) |

