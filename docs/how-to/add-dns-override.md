---
icon: lucide/file-search
---

# Add a DNS override

Give a fixed address a name in `lan.buunk.org`. Background:
[DNS and time](../concepts/dns-and-time.md).

**Before you start:** the address is in `inventory.nix` in
`nix-hgd1-cluster` (hosts, VMs, VIPs), or in [Addresses](../reference/addresses.md)
for the network's own devices. The name follows it.

1. **Back up** OPNsense (System → Configuration → Backups → Download).
2. Services → Unbound DNS → Overrides → Host overrides → **+**: host, domain
   `lan.buunk.org`, the address, a description (what it is).
3. Save, **Apply**.
4. Check:

    ```sh
    dig @10.10.20.1 <name>.lan.buunk.org +short
    ```

5. Add it to [Addresses](../reference/addresses.md) if it's a network
   address; otherwise the cluster docs list it already.
