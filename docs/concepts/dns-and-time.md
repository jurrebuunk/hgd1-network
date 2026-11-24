---
icon: lucide/clock
---

# DNS and time

OPNsense is the site's resolver and clock: Unbound for `lan.buunk.org` and
the internet, NTP for every VLAN. Everything else depends on both, Ceph
especially.

!!! info "At a glance"

    **Status** <span class="status">Done</span> · **Resolver**
    `10.10.<vlan>.1` (Unbound, DNSSEC) · **Zone** `lan.buunk.org` · **NTP**
    OPNsense · **How-to** [Add a DNS override](../how-to/add-dns-override.md)

## Overview

| Service | Where | For |
| --- | --- | --- |
| DNS (Unbound) | The router's address on every VLAN (`.1`) | `lan.buunk.org` host overrides; recursive with DNSSEC for everything else |
| NTP | The router's address on every VLAN | Hosts (chrony prefers it), VMs, the switch (SNTP) |

The storage VLAN has no router address, so nothing there uses it; hosts and
VMs use their other NIC.

## How it works

### Host overrides

Every fixed address has a name in `lan.buunk.org`, as an Unbound host
override: hosts (`hgd1-hv01.lan.buunk.org`), VIPs (`s3`, `k8s`, `mon`,
`api`) and the VMs. They follow `inventory.nix` in `nix-hgd1-cluster`;
a new address there needs an override here
([Add a DNS override](../how-to/add-dns-override.md)).

`lab.buunk.org` is internal for now (the web UI).

### When the router is down

Hosts keep each other in `/etc/hosts`, so they resolve each other (and
Ceph keeps its monitors) without Unbound. Names of VIPs and VMs don't
resolve until it's back.

### Known issue: NOERROR for unknown names

Unbound answers NOERROR with no address, instead of NXDOMAIN, for names
under `lan.buunk.org` that don't exist. Resolvers that stop at the first
non-NXDOMAIN answer (musl, so Alpine containers) then fail on search-domain
lookups. The k3s pods work around it; the fix here is the zone's local-zone
type ([#1](https://github.com/jurrebuunk/hgd1-network/issues/1)).

### Time

Ceph refuses to run with clocks more than a few hundredths of a second
apart, so hosts run chrony and prefer OPNsense; OPNsense syncs to public
pools.

## Configuration

| What | Where |
| --- | --- |
| Unbound, overrides, DNSSEC | OPNsense → Services → Unbound DNS (Ansible here, planned) |
| NTP | OPNsense → Services → Network Time |
| Host side (resolvers, `/etc/hosts`, chrony) | `modules/network.nix` in `nix-hgd1-cluster` |

## Operating

```sh
dig @10.10.20.1 s3.lan.buunk.org +short             # an override
dig @10.10.20.1 example.com +dnssec | grep -c RRSIG # DNSSEC answers
labctl metrics query 'node_timex_offset_seconds'    # clock offset per host and VM
```

## Troubleshooting

See [Troubleshooting → Names don't resolve](../operate/troubleshooting.md#names-dont-resolve).

## Related

- [Network design](design.md) · [Addresses](../reference/addresses.md)
