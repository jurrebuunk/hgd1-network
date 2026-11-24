---
icon: lucide/heart-pulse
---

# Operate

Running the network day to day. Commands run on the laptop with WARP
connected.

## Health check

```sh
labctl metrics query 'up{job=~"router-node|switch-snmp"}'           # router and switch answer
labctl metrics query 'probe_success{job=~"probe-.*"} == 0'          # failing pings, DNS, internet
labctl logs --host hgd1-fw01 --since 1h | tail                      # router log arriving
dig @10.10.20.1 s3.lan.buunk.org +short                             # DNS answers
```

**Healthy means:**

- [x] router and switch targets `up`, syslog from both arriving;
- [x] no failing probes (ping, DNS, internet from every host);
- [x] DNS answers for the overrides; clocks in sync (no Ceph clock skew);
- [x] no switch port errors growing (`labctl metrics query 'rate(ifInErrors[1h]) > 0'`).

!!! cluster "On the cluster docs: health check"

    `labctl health`: hosts, VMs, nodes, Ceph, VIPs, disks, S3 and the
    tunnel.

    [Cluster health check](https://jurrebuunk.github.io/nix-hgd1-cluster/operate/){ .md-button }

## Ground rules

1. **Back up before every change**, outside Git (`~/.local/state/nix-cluster/network-rollout/`).
2. **One change at a time**, checked before the next.
3. **Never lock yourself out:** keep switch port 1 (mgmt) and the router
   console as the way back.
4. **A switch reboot stops everything**, Ceph included; plan it like a site
   outage.

## Runbooks

<div class="grid cards" markdown>

- :lucide-router: [**Router and switch**](router-and-switch.md): what's configured, access, reboot impact
- :lucide-life-buoy: [**Troubleshooting**](troubleshooting.md): find the fix by symptom
- :lucide-cable: [**Rebuild the network**](rebuild.md): bring it up from scratch

</div>

## Schedule

| How often | What |
| --- | --- |
| Weekly | The health check above |
| Monthly | Download a router backup and a switch `running-config` |
| Every few months | OPNsense updates (System → Firmware), after a backup; check the forwards and remote access after |
| Yearly | Aruba firmware if there's a security release; review the firewall rules against the [policy](../reference/firewall.md) |

## State outside Git

| What | Where |
| --- | --- |
| Router and switch backups | `~/.local/state/nix-cluster/network-rollout/` (mode 600) |
| OPNsense `root`, Aruba `manager` passwords | Password manager |
