# /linux-network-configurer

Configure any Linux networking task — static IP, VLANs, bonding, WireGuard VPN, iptables, nftables, DNS, and more.

## Usage

```
/linux-network-configurer <task description>
```

## Examples

```
/linux-network-configurer set static IP 192.168.1.100/24, gateway 192.168.1.1, DNS 1.1.1.1 on Ubuntu Server
/linux-network-configurer set up WireGuard VPN server on Debian 12 with 10.0.0.0/24 tunnel
/linux-network-configurer add WireGuard peer for my laptop to an existing server
/linux-network-configurer configure VLAN 100 tagged on eth0, untagged on br0
/linux-network-configurer iptables rules for a web server: allow 80, 443, 22 — drop everything else
/linux-network-configurer bond eth0 and eth1 with LACP (802.3ad) active-active
/linux-network-configurer my network stopped working after a reboot — debug it
/linux-network-configurer configure systemd-resolved with custom DNS and search domain
```

## Generates

```
network-config/
  <stack>/
    <interface>.network       ← systemd-networkd config
    — or —
    <interface>.nmconnection  ← NetworkManager config
    — or —
    interfaces                ← ifupdown /etc/network/interfaces
  wg0.conf                   ← WireGuard interface config (if applicable)
  apply.sh                   ← ordered apply commands with rollback hint
```

## Behavior

If the task and OS/network stack are clear, generate immediately.
If the network stack is unknown, ask one question: NetworkManager / systemd-networkd / Netplan / ifupdown / not sure.

1. Detect or confirm the network stack
2. Generate correct config files for that stack
3. For WireGuard: generate server config + peer config template
4. For iptables/nftables: generate complete ruleset with comments and `iptables-save` persistence
5. Write all files silently to `./network-config/`
6. Print: file list with system destination paths, apply commands in order, verify commands
7. **⚠️ Print lockout warning** — always warn to test from a local session before remote apply

$ARGUMENTS
