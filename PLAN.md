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
4. ~~Move the network pages from the handbook here and link back.~~ Done:
   `docs/`, published at https://jurrebuunk.github.io/hgd1-network/.

## Known issues

- **Unbound answers NOERROR (no address) for names under `lan.buunk.org`
  that don't exist**, instead of NXDOMAIN (seen 2026-11-24 with
  `dig @10.10.10.1 nonexistent-xyz.lan.buunk.org`). Resolvers that stop at
  the first non-NXDOMAIN answer (musl) then fail. The k3s pods work around it
  (their own resolver file without the search domain, in
  `nix-hgd1-cluster`); the fix here is the zone's local-zone type (`static`
  answers NXDOMAIN for unknown names).

## Visibility

This repo is **public**, like the docs it holds. Secrets (the OPNsense API
key, the SNMP community, switch credentials) only ever go in encrypted, and
router and switch exports stay outside Git: they contain password hashes and
keys.

## Not yet

CI and the directory layout are intentionally not set up yet.
