---
icon: lucide/cable
---

# Change a switch port

Give a port on the Aruba a role: which VLAN is untagged, which are tagged.
Background: [switch ports](../concepts/design.md#switch-ports).

**Before you start:** confirm what's plugged in by MAC, not by port number
(`show mac-address <port>`). Changing a host's port cuts it off; plan it like
[host maintenance](https://jurrebuunk.github.io/nix-hgd1-cluster/operate/host-maintenance/).

1. **Back up:**

    ```sh
    ssh hgd1-sw01 'show running-config' > ~/.local/state/nix-cluster/network-rollout/sw01-$(date +%F).txt
    chmod 600 ~/.local/state/nix-cluster/network-rollout/sw01-*.txt
    ```

2. Set the role. For a wired user port (untagged 100):

    ```text
    ssh hgd1-sw01
    configure
    vlan 999 no untagged 22
    vlan 100 untagged 22
    interface 22 enable
    write memory
    ```

    Tagged VLANs: `vlan 40 tagged 22`. Unused ports go back to `vlan 999
    untagged <port>` and `interface <port> disable`.

3. Check: `show vlans ports 22 detail`, and the device gets an address on
   the right VLAN.
4. Update the port table in [Addresses](../reference/addresses.md#switch-ports).
