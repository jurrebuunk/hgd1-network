# Plan

Where this repo is going, so I can pick it up from here. Nothing is built yet.

## Goal

Router and switch configuration as code, instead of SSH commands and
hand-edited config. Same rules as the rest: reviewed in Git, applied
deliberately, backed up first.

## Current state (to take over, not to change)

Built by hand in steps N0–N7 and documented in the handbook:
[Network](https://jurrebuunk.github.io/nix-hgd1-cluster/concepts/network/),
[Firewall](https://jurrebuunk.github.io/nix-hgd1-cluster/reference/firewall/),
[Router and switch](https://jurrebuunk.github.io/nix-hgd1-cluster/operate/router-and-switch/).
Covers VLANs 10/20/30/40/100, DHCP, Unbound overrides, firewall rules and
aliases, the Sunshine forwards, the node_exporter plugin, syslog, and on the
switch the port roles, SNMP and logging.

## Tooling

| Device | Ansible |
| --- | --- |
| OPNsense 26.7 | `ansibleguy.opnsense` collection (uses the OPNsense API; needs an API key) |
| Aruba 2530 (AOS-S, `YA.16.02.0012`) | `arubanetworks.aos_switch` collection, SSH with the existing key |

- **Inventory and variables:** VLANs, addresses and DNS names. Keep them in
  step with `inventory.nix` in the cluster repo (or generate from it).
- **Secrets:** sops with my age key (or ansible-vault). The OPNsense API key,
  SNMP community and switch credentials never in plain text.
- **Runs from the laptop over WARP.** CI only lints; it never applies.

## Safe takeover

1. Back up both devices (as today, to `~/.local/state/nix-cluster/network-rollout/`).
2. Describe the current config in Ansible and run `--check --diff` until it
   reports **no changes**.
3. Only then make changes through Ansible, one at a time, with a backup before
   every run.
4. Move the network pages from the handbook here and link back.

## Visibility

This repo is **private**: it describes the firewall in more detail than the
public cluster repo.

## Not yet

CI and the directory layout are intentionally not set up yet.
