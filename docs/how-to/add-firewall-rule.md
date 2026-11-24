---
icon: lucide/shield-plus
---

# Add a firewall rule

Allow a new path between VLANs. Background: the
[firewall policy](../reference/firewall.md): everything between VLANs is
denied unless a rule allows it.

**Before you start:** know the exact source, destination, protocol and port,
and why. Prefer the narrowest rule (one address and port, not a subnet).

1. **Back up** OPNsense.
2. Firewall → Rules → the **source** VLAN's interface (rules apply where
   traffic enters) → **+**:

    | Field | Value |
    | --- | --- |
    | Action | Pass |
    | Protocol | TCP or UDP |
    | Source | The source address or alias |
    | Destination | The destination address or alias, and the port |
    | Description | What and why (for example "K8S → API VIP for lab-console") |

3. Move it **above** that interface's deny rules (for infra: above "INFRA
   deny other private destinations").
4. Save, **Apply**.
5. Check from the source: `nc -zv <destination> <port>` (from a pod:
   `kubectl run -it --rm t --image=busybox:1.37 -- nc -zv <dst> <port>`), and
   check that something that should stay blocked still is.
6. Add the rule to the [Firewall](../reference/firewall.md) page.

The hosts and VMs have their own firewalls too: a port they don't open stays
closed whatever the router allows
([host firewall](https://jurrebuunk.github.io/nix-hgd1-cluster/reference/firewall/)).
