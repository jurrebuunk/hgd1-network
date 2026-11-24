---
icon: lucide/git-commit-horizontal
---

# Your first change

A guided first change: back up the router, add a harmless DNS override,
check it, and remove it again. It shows the loop for every change here:
**back up, change one thing, check, keep the backup**.

**Before you start:** [access](access.md) works.

## 1. Back up

OPNsense: System → Configuration → Backups → **Download configuration**.
Save it to `~/.local/state/nix-cluster/network-rollout/` (mode 600): it
contains password hashes and keys, so never in Git.

## 2. Make the change

Services → Unbound DNS → Overrides → Host overrides → **+**:

| Field | Value |
| --- | --- |
| Host | `first-change` |
| Domain | `lan.buunk.org` |
| IP | `10.10.20.11` |

Save, then **Apply**.

## 3. Check

```sh
dig @10.10.20.1 first-change.lan.buunk.org +short    # 10.10.20.11
```

## 4. Undo it

Delete the override, Apply, and check that the name no longer resolves.

When the Ansible exists, the same loop becomes: change a variable, `--check
--diff`, run, check. See [the plan](../reference/roadmap.md).
