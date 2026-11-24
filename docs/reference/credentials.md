---
icon: lucide/lock-keyhole
---

# Credentials

Where every credential of the router and switch lives. This page never holds
a value, only its location.

!!! cluster "On the cluster docs: credentials"

    Everything else: agenix secrets on the hosts, the kubeconfig, API
    tokens and the laptop's keys.

    [Credentials](https://jurrebuunk.github.io/nix-hgd1-cluster/reference/credentials/){ .md-button }

| Credential | Where | Used for |
| --- | --- | --- |
| OPNsense `root` password | Password manager | Web UI and console (SSH uses the key) |
| OPNsense SSH | Admin key `~/.ssh/id_ed25519` on `root` | `ssh root@10.10.10.1` |
| Aruba `manager` password | Password manager | Serial console |
| Aruba SSH key | `~/.ssh/id_rsa_hgd1_network` | `ssh manager@10.10.10.2` |
| SNMP community (read-only) | `secrets/snmp-community.age` in `nix-hgd1-cluster` | snmp_exporter on the hosts |
| Router and switch exports | `~/.local/state/nix-cluster/network-rollout/` (mode 600) | Backups; contain password hashes and keys |

**Planned** (with the Ansible): the OPNsense API key, the SNMP community and
the switch login as sops-encrypted variables here.
