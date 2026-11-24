---
icon: lucide/shield
---

# Firewall

What may talk to what between the VLANs and from the home network.
OPNsense enforces it; the ports the hosts and VMs open themselves are on the
cluster docs.

!!! cluster "On the cluster docs: host and VM ports"

    The NixOS firewalls on the hosts and k3s VMs, port by port, and the Cloudflare Gateway rules for remote access.

    [Host firewall](https://jurrebuunk.github.io/nix-hgd1-cluster/reference/firewall/){ .md-button }


## VLAN policy

Everything between VLANs is denied unless listed.

| From | Allowed | Blocked |
| --- | --- | --- |
| mgmt (10) | Everything internal, internet | – |
| infra (20) | Internet, DNS and NTP on OPNsense | Other internal |
| infra: `10.10.20.11–13` only | mgmt, k8s, users (remote access) | storage (not routed) |
| k8s (40) | Internet, DNS and NTP on OPNsense | mgmt, infra, users |
| users (100) | Internet; TCP 80/443 to k8s load balancers `.200–.249` | mgmt, infra, k8s nodes |
| WAN (home) | Sunshine forwards to `10.10.100.50` (below) | Everything else |

The remote-access exception uses the OPNsense aliases `HGD1_TUNNEL_HOSTS`
(`10.10.20.11–13`) and `HGD1_TUNNEL_DESTS` (`10.10.10.0/24`,
`10.10.40.0/24`, `10.10.100.0/24`), in a rule placed above "INFRA deny other
private destinations".

## WAN forwards

| Proto | Ports | To |
| --- | --- | --- |
| TCP | 47984, 47989, 48010 | `10.10.100.50` (Sunshine) |
| UDP | 47998–48000, 48002, 48010 | `10.10.100.50` |

Source: the whole home network `192.168.2.0/24`, destination the WAN address
`192.168.2.12`. Not forwarded: 47990 (Sunshine admin), RDP, discovery.
"Block private networks" stays **off** on WAN.

## Devices

| Device | Open for monitoring |
| --- | --- |
| OPNsense | `10.10.10.1:9100` node_exporter (reached by the hosts through the remote-access rule) |
| Aruba | SNMP v2c read-only (`operator restricted`) |

Rules for k3s workloads (for example K8S → `10.10.20.20:443` for the web UI)
are added here too. The switch's SNMP community is
`secrets/snmp-community.age` in `nix-hgd1-cluster`, where the scraper runs.
