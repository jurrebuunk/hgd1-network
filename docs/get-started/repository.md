---
icon: lucide/folder-git-2
---

# Tour of the repo

Today this repo holds the plan and these docs; the router and switch were
configured by hand and are described here. The Ansible comes next.

```text
docs/              these docs (published to GitHub Pages)
zensical.toml      the docs site
PLAN.md            what's planned: Ansible for OPNsense and the Aruba
README.md
```

## Planned layout

From [PLAN.md](https://github.com/jurrebuunk/hgd1-network/blob/main/PLAN.md):

| Device | Ansible |
| --- | --- |
| OPNsense 26.7 | `ansibleguy.opnsense` collection, through the OPNsense API |
| Aruba 2530 | `arubanetworks.aos_switch` collection, over SSH |

- **Variables** for VLANs, addresses and DNS names, in step with
  `inventory.nix` in `nix-hgd1-cluster`.
- **Secrets** (API key, SNMP community, switch login) encrypted with sops.
- **Runs from the laptop over WARP**; CI only lints.
- **Backups before every run**, outside Git.

## Conventions

- One change at a time, with a backup before it.
- Router and switch exports never in Git: they contain password hashes and
  keys.
- Deferred work becomes a GitHub issue on this repo.
