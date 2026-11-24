---
icon: lucide/life-buoy
---

# Troubleshooting

Find the symptom, follow the steps. For everything on the machines
themselves (hosts, Ceph, S3, k3s, the API), see the cluster docs.

!!! cluster "On the cluster docs: troubleshooting"

    Host down, Ceph not healthy, S3 not answering, k3s nodes, the Platform
    API and monitoring.

    [Cluster troubleshooting](https://jurrebuunk.github.io/nix-hgd1-cluster/operate/troubleshooting/){ .md-button }

## No access

1. **WARP:** `warp-cli status`. Reconnect; on the users VLAN at home,
   disconnect it and use the site network directly.
2. **Tunnel:** Zero Trust dashboard → Networks → Tunnels → `hgd1-admin`. Zero
   healthy connectors means all three hosts are down or offline
   ([Remote access](https://jurrebuunk.github.io/nix-hgd1-cluster/concepts/remote-access/)).
3. **At the site without WARP:** switch port 1 (mgmt, DHCP). Everything
   internal is reachable from there, including `ssh root@10.10.10.1`.
4. **Router dead:** the OPNsense console. Restore the latest backup
   (`~/.local/state/nix-cluster/network-rollout/`) or, once it exists,
   re-apply this repo's playbooks.

## Names don't resolve

| You see | Likely cause and fix |
| --- | --- |
| A `*.lan.buunk.org` name gives no address | Missing Unbound host override; add it, in step with `inventory.nix` |
| A made-up `*.lan.buunk.org` name gives NOERROR instead of NXDOMAIN | Known: the zone's local-zone type; see [PLAN.md](../reference/roadmap.md#known-issues). k3s pods work around it |
| Hosts still resolve each other while the router is down | Expected: they keep each other in `/etc/hosts` |

## Traffic between VLANs blocked

Everything between VLANs is denied unless the [policy](../reference/firewall.md) allows
it. Check the rule in OPNsense (Firewall → Log Files → Live View, filter on
the source address) before changing anything; a new path is a new rule here,
following the policy.

| You see | Likely cause |
| --- | --- |
| Web UI loads but shows no data | The pod can't reach `10.10.20.20:443`: the K8S → API VIP rule |
| A host can't reach the router's node_exporter or the switch | The remote-access rule (`HGD1_TUNNEL_HOSTS`) is missing or below the INFRA deny |
| Moonlight from home can't connect | The Sunshine forwards, or "Block private networks" was turned on for WAN |

## Router or switch logs missing

Both send syslog to `10.10.20.10:514` (the monitoring VIP). Check the
syslog settings on OPNsense and `show logging` on the switch; then
`labctl logs -f --host hgd1-fw01` (see [Monitoring](https://jurrebuunk.github.io/nix-hgd1-cluster/concepts/monitoring/)).

## After a reboot

- **OPNsense:** the site loses internet, routing, DNS and remote access until
  it's back; Ceph keeps working.
- **Switch:** everything stops, Ceph included. Expect slow or blocked Ceph
  requests until it's back ([Router and switch](router-and-switch.md#reboot-impact)).
