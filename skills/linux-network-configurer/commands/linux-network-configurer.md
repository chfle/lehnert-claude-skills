# /linux-network-configurer

Configure any Linux networking task — static IP, routing, VLANs, bonding, WireGuard, iptables, nftables, DNS, bridges.

## Usage

```
/linux-network-configurer <task description>
```

## Examples

```
/linux-network-configurer set static IP 192.168.1.100 on Ubuntu Server
/linux-network-configurer set up WireGuard VPN server on Debian 12
/linux-network-configurer configure VLAN 100 on eth0
/linux-network-configurer iptables ruleset for a web server
/linux-network-configurer bond eth0 and eth1 with LACP
/linux-network-configurer my network stopped working
```

## Behavior

If the task and OS/network stack are clear, generate immediately.
If the network stack is unknown, ask one question (NetworkManager / systemd-networkd / ifupdown / not sure).

Generates the correct config format for the detected stack. Writes all files to `./network-config/` silently.
Prints only: created file list with system destination paths, apply commands in order, verify commands, and a lockout warning.

$ARGUMENTS
