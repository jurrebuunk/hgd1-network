---
icon: lucide/rocket
---

# Get started

Three short steps take you from nothing to a safe change on the router or
switch. Do them in order the first time.

<div class="grid cards" markdown>

-   :lucide-log-in:{ .lg .middle } **1. Get access**

    ---

    Reach the router and the switch over WARP, by SSH and the web UI.

    [:octicons-arrow-right-24: Get access](access.md)

-   :lucide-git-commit-horizontal:{ .lg .middle } **2. Your first change**

    ---

    Back up, make one small change, check it, and know how to undo it.

    [:octicons-arrow-right-24: First change](first-change.md)

-   :lucide-folder-git-2:{ .lg .middle } **3. Tour of the repo**

    ---

    What's here today and what the Ansible will look like.

    [:octicons-arrow-right-24: Repository](repository.md)

</div>

## What you need

- WARP connected (team `buunk`), or a cable in switch port 1 (mgmt).
- Your SSH key on OPNsense's `root` user, and the switch key
  `~/.ssh/id_rsa_hgd1_network`.
- The `nix-hgd1-cluster` dev shell for `labctl` (logs and metrics of the
  router and switch).

## Then read

- [Network design](../concepts/design.md): the VLANs, once.
- [Health check](../operate/index.md): the check you'll run most.
