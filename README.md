# hgd1-network

Ansible for the hgd1 site network: the OPNsense router `hgd1-fw01` and the
Aruba 2530 switch `hgd1-sw01`. The hosts and everything on them live in
[nix-hgd1-cluster](https://github.com/jurrebuunk/nix-hgd1-cluster).

**Status:** planning. Start with [PLAN.md](PLAN.md).

**Docs:** [`docs/`](docs/index.md), the network part of the hgd1 docs (the
other two: [cluster](https://jurrebuunk.github.io/nix-hgd1-cluster/),
[Kubernetes](https://jurrebuunk.github.io/hgd1-k8s/)). Not hosted yet; read
them here or preview with `nix shell nixpkgs#zensical -c zensical serve`.
