---
icon: lucide/log-in
---

# Get access

The router and switch are on the management VLAN (`10.10.10.0/24`), reachable
from the site network or over WARP.

## 1. WARP

```sh
warp-cli connect
ping -c1 10.10.10.1
```

!!! cluster "On the cluster docs: remote access"

    How WARP reaches the site through the hosts' tunnel, and enrolling a
    device.

    [Remote access](https://jurrebuunk.github.io/nix-hgd1-cluster/concepts/remote-access/){ .md-button }

## 2. Router

```sh
ssh root@10.10.10.1          # key only
```

Web UI: <https://10.10.10.1> (`root`, password in the password manager).

## 3. Switch

The Aruba only speaks older SSH algorithms; a host entry keeps that to this
one device:

```text
# ~/.ssh/config
Host hgd1-sw01 10.10.10.2
  User manager
  IdentityFile ~/.ssh/id_rsa_hgd1_network
  HostKeyAlgorithms +ssh-rsa
  PubkeyAcceptedAlgorithms +ssh-rsa
```

```sh
ssh hgd1-sw01
show vlans
```

## Without WARP

Plug into switch port 1 (mgmt, DHCP). Everything internal is reachable from
there. If the router itself is down: its console.
