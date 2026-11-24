---
icon: lucide/router
---

# Router and switch

What runs on OPNsense and the Aruba switch, and how to reach them. Their
configuration becomes Ansible in this repo, which also takes the backups
([plan](https://github.com/jurrebuunk/hgd1-network/blob/main/PLAN.md)); until
then it's done by hand, carefully, with a backup first.

!!! info "At a glance"

    **Router** OPNsense 26.7.9_3, `hgd1-fw01` · **Switch** HP/Aruba 2530-48G,
    `hgd1-sw01`, firmware `YA.16.02.0012` · **Design** [Network](index.md), [Firewall](firewall.md)

## Access

| Device | SSH | Web |
| --- | --- | --- |
| OPNsense `hgd1-fw01` | `ssh root@10.10.10.1` (key only) | `https://10.10.10.1` |
| Aruba `hgd1-sw01` | `ssh manager@10.10.10.2` (dedicated key, legacy algorithms) | – |

Both are reachable only from the site or through WARP. Without WARP: switch
port 1 or the OPNsense console. Use direct access to look, or in an
emergency; make lasting changes in this repo's Ansible, or its next run
overwrites them.

## What's configured

| Device | Configuration |
| --- | --- |
| OPNsense | VLAN interfaces 20/40/100, DHCP on mgmt and users, outbound NAT, Unbound with DNSSEC and the host overrides, NTP, the inter-VLAN firewall policy and remote-access aliases, the Sunshine forwards, plugins `os-isc-dhcp`, `os-firewall-legacy` and `os-node_exporter`, syslog to `10.10.20.10` |
| Aruba | VLANs 10/20/30/40/100/999, port roles, management on `10.10.10.2`, SNTP from OPNsense, key-only SSH, read-only SNMP, syslog to `10.10.20.10` |

The why behind each part is in [Network](index.md) and
[Firewall](firewall.md); how they feed monitoring is on the cluster docs,
[Monitoring](https://jurrebuunk.github.io/nix-hgd1-cluster/concepts/monitoring/).

## Changes

Everything below happens in this repo:

| Task | Where |
| --- | --- |
| DNS host override (new VM, new VIP) | Unbound overrides in its variables; keep them in line with `inventory.nix` in `nix-hgd1-cluster` |
| Firewall rule or alias | Firewall variables; follow the [policy](firewall.md) |
| Switch port role | Switch variables |
| Backups | Taken before every run |
| Firmware updates | Its runbook for OPNsense and the Aruba |

## Reboot impact

- **OPNsense:** the site loses internet, inter-VLAN routing, DNS and remote
  access. Ceph (storage VLAN, layer 2) keeps working.
- **Switch:** interrupts **everything**, including Ceph traffic between the
  hosts. Plan it like a site outage; expect Ceph to report slow or blocked
  requests until it's back.

## Related

- [Network](index.md) · [Firewall](firewall.md) · [Rebuild the network](rebuild.md)
- On the cluster docs: [Repositories](https://jurrebuunk.github.io/nix-hgd1-cluster/reference/repositories/)
