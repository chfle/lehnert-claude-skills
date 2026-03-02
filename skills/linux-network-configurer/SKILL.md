---
name: linux-network-configurer
description: Use when user wants to configure Linux networking — static IP, network interfaces, routing, iptables, nftables, firewalld, VLANs, bonding, WireGuard VPN, OpenVPN, DNS, bridge interfaces, network namespaces, or asks how to set up, troubleshoot, or change any network setting on a Linux system.
version: 1.2.0
author: Lehnert
---

# Linux Network Configurer

## Overview

Generates ready-to-apply network configuration files and commands for any Linux networking task. Detects the user's network stack (NetworkManager, systemd-networkd, or legacy ifupdown) and produces the correct config format. Writes all files to disk and prints only the apply commands.

**Language:** Respond in the user's language. Config files and comments in English.

---

## When to Use

- User wants to set a static IP address
- User wants to configure routing, VLANs, bonding, or bridges
- User wants to set up WireGuard or OpenVPN
- User wants to configure iptables, nftables, or firewalld rules
- User asks "why is my network not working?" — include troubleshooting
- User wants to configure DNS resolvers or /etc/hosts

## When NOT to Use

- User wants to audit firewall rules → `linux-config-auditor`
- User wants to harden the full server → `linux-security-hardener`
- User wants to write a network monitoring script → `linux-shell-scriptor`

---

## Step 1 — Detect Task Type

Map the user's request to a task category. If the request covers multiple areas, handle them all.

| User says | Task |
|-----------|------|
| "set static IP", "change my IP address" | Static IP configuration |
| "add a route", "routing table", "default gateway" | Routing |
| "VLAN", "tagged interface", "802.1q" | VLAN configuration |
| "bond", "team", "link aggregation", "LACP" | Interface bonding |
| "bridge", "br0", "VM networking", "LXC bridge" | Bridge interface |
| "WireGuard", "wg0", "VPN tunnel" | WireGuard VPN |
| "OpenVPN", "vpn server", "ovpn" | OpenVPN |
| "iptables", "nftables", "firewall rules" | Firewall rules |
| "firewalld", "zone", "rich rule" | firewalld |
| "DNS", "resolv.conf", "nameserver" | DNS configuration |
| "network not working", "can't connect", "ping fails" | Troubleshooting |
| "namespace", "network isolation" | Network namespaces |

---

## Step 2 — Detect the Network Stack

Ask at most **one** question if the network stack is unknown:
> "Which network manager does your system use?"
> 1. **NetworkManager** — most desktop and RHEL/Rocky/Alma systems
> 2. **systemd-networkd** — most servers, Ubuntu Server, Arch Linux
> 3. **ifupdown** (`/etc/network/interfaces`) — older Debian/Ubuntu
> 4. **Not sure** — I'll generate all three formats

If the user specifies their distro, auto-detect:

| Distro | Default stack |
|--------|--------------|
| Ubuntu Desktop 20.04+ | NetworkManager (with Netplan frontend) |
| Ubuntu Server 20.04+ | systemd-networkd (with Netplan frontend) |
| Debian 11+ | NetworkManager or ifupdown |
| RHEL / Rocky / Alma / CentOS | NetworkManager |
| Arch Linux | systemd-networkd or NetworkManager |
| Alpine Linux | ifupdown (`/etc/network/interfaces`) |

**Netplan** (Ubuntu 18.04+): Netplan is a YAML frontend that writes to either NetworkManager or systemd-networkd. Always prefer Netplan on Ubuntu — it survives upgrades better than editing lower-level configs directly. Use `netplan try` instead of `netplan apply` — auto-reverts after 120s if no confirmation.

---

## Configuration Patterns

### Static IP

**systemd-networkd** (`/etc/systemd/network/10-static.network`):
```ini
[Match]
Name=eth0

[Network]
Address=192.168.1.100/24
Gateway=192.168.1.1
DNS=1.1.1.1
DNS=8.8.8.8

[Route]
Gateway=192.168.1.1
```

Apply:
```bash
systemctl restart systemd-networkd
networkctl status
```

**Netplan** (`/etc/netplan/01-static.yaml`) — Ubuntu Server:
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
      dhcp4: false
```

Apply:
```bash
netplan apply
```

**NetworkManager** (`/etc/NetworkManager/system-connections/static.nmconnection`):
```ini
[connection]
id=static-eth0
type=ethernet
interface-name=eth0

[ipv4]
method=manual
addresses=192.168.1.100/24
gateway=192.168.1.1
dns=1.1.1.1;8.8.8.8;

[ipv6]
method=disabled
```

Apply:
```bash
chmod 600 /etc/NetworkManager/system-connections/static.nmconnection
nmcli connection reload
nmcli connection up static-eth0
```

**ifupdown** (`/etc/network/interfaces`):
```
auto eth0
iface eth0 inet static
    address 192.168.1.100/24
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 8.8.8.8
```

Apply:
```bash
ifdown eth0 && ifup eth0
```

---

### VLAN Configuration

**systemd-networkd** — two files needed:

`/etc/systemd/network/10-vlan100.netdev`:
```ini
[NetDev]
Name=eth0.100
Kind=vlan

[VLAN]
Id=100
```

`/etc/systemd/network/10-vlan100.network`:
```ini
[Match]
Name=eth0.100

[Network]
Address=10.100.0.1/24
```

Apply: `systemctl restart systemd-networkd`

**ip commands (immediate, not persistent)**:
```bash
ip link add link eth0 name eth0.100 type vlan id 100
ip addr add 10.100.0.1/24 dev eth0.100
ip link set eth0.100 up
```

---

### Interface Bonding (LACP / Active-Backup)

**systemd-networkd** — three files:

`/etc/systemd/network/10-bond0.netdev`:
```ini
[NetDev]
Name=bond0
Kind=bond

[Bond]
Mode=802.3ad          # LACP — use active-backup for failover only
TransmitHashPolicy=layer3+4
LACPTransmitRate=fast
MIIMonitorSec=100ms
```

`/etc/systemd/network/10-bond-slave.network` (apply to each slave interface):
```ini
[Match]
Name=eth0

[Network]
Bond=bond0
```

`/etc/systemd/network/10-bond0.network`:
```ini
[Match]
Name=bond0

[Network]
Address=192.168.1.100/24
Gateway=192.168.1.1
```

---

### Bridge Interface (for VMs / LXC containers)

**systemd-networkd**:

`/etc/systemd/network/10-bridge.netdev`:
```ini
[NetDev]
Name=br0
Kind=bridge
```

`/etc/systemd/network/10-bridge-bind.network`:
```ini
[Match]
Name=eth0

[Network]
Bridge=br0
```

`/etc/systemd/network/10-bridge.network`:
```ini
[Match]
Name=br0

[Network]
Address=192.168.1.100/24
Gateway=192.168.1.1
```

---

### WireGuard VPN

Generate a complete WireGuard server + client config pair.

**Server** (`/etc/wireguard/wg0.conf`):
```ini
[Interface]
Address = 10.8.0.1/24
ListenPort = 51820
PrivateKey = <SERVER_PRIVATE_KEY>

# Enable IP forwarding and NAT for client internet access
# iptables version (most systems):
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
# nftables alternative (Debian 12+, RHEL 9+):
# PostUp = nft add rule inet filter forward iifname wg0 accept; nft add rule inet nat postrouting oifname eth0 masquerade
# PostDown = nft flush ruleset

[Peer]
# Client 1
PublicKey = <CLIENT1_PUBLIC_KEY>
AllowedIPs = 10.8.0.2/32
```

**Client** (`wg0-client.conf`):
```ini
[Interface]
Address = 10.8.0.2/24
PrivateKey = <CLIENT_PRIVATE_KEY>
DNS = 1.1.1.1

[Peer]
PublicKey = <SERVER_PUBLIC_KEY>
Endpoint = YOUR_SERVER_IP:51820
AllowedIPs = 0.0.0.0/0    # Route all traffic through VPN
PersistentKeepalive = 25
# MTU = 1280              # Uncomment if experiencing fragmentation issues
```

Key generation commands:
```bash
# Generate server keys
wg genkey | tee /etc/wireguard/server_private.key | wg pubkey > /etc/wireguard/server_public.key
chmod 600 /etc/wireguard/server_private.key

# Generate client keys
wg genkey | tee client1_private.key | wg pubkey > client1_public.key

# Enable and start
sysctl -w net.ipv4.ip_forward=1
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.d/99-wireguard.conf
systemctl enable --now wg-quick@wg0
wg show
```

---

### iptables Ruleset

Generate a complete stateful iptables ruleset as a script:

```bash
#!/usr/bin/env bash
# Flush existing rules
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X

# Default policies
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Allow loopback
iptables -A INPUT -i lo -j ACCEPT

# Allow established/related
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Allow SSH
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow HTTP/HTTPS (remove if not a web server)
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Allow ICMP ping
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT

# Log and drop everything else
iptables -A INPUT -j LOG --log-prefix "IPT-DROP: " --log-level 4
iptables -A INPUT -j DROP

# Save rules
iptables-save > /etc/iptables/rules.v4
```

---

### nftables Ruleset

```
#!/usr/sbin/nft -f
flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;

        iif lo accept
        ct state established,related accept
        ct state invalid drop

        tcp dport 22 accept
        tcp dport { 80, 443 } accept
        icmp type echo-request accept
        icmpv6 type { echo-request, nd-neighbor-solicit, nd-router-advert } accept

        log prefix "nft-drop: " drop
    }

    chain forward {
        type filter hook forward priority 0; policy drop;
    }

    chain output {
        type filter hook output priority 0; policy accept;
    }
}
```

Apply: `nft -f /etc/nftables.conf && systemctl enable nftables`

---

### Tailscale VPN (easier alternative to WireGuard)

Use when the user wants a zero-config mesh VPN without managing a server.

```bash
# Install (Ubuntu/Debian)
curl -fsSL https://tailscale.com/install.sh | sh

# Authenticate and connect
tailscale up

# Advertise this machine as a subnet router (optional — exposes LAN)
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.d/99-tailscale.conf
sysctl -p /etc/sysctl.d/99-tailscale.conf
tailscale up --advertise-routes=192.168.1.0/24

# Enable exit node (route all client traffic through this server)
tailscale up --advertise-exit-node

# Check status
tailscale status
tailscale ip          # your Tailscale IP (100.x.x.x range)
```

**Key differences from WireGuard:**
- No server to configure — Tailscale manages the coordination
- Devices connect directly peer-to-peer (DERP relay as fallback)
- Access control defined in Tailscale admin console, not config files
- Requires Tailscale account (free tier: 100 devices)

---

### DNS Configuration

**systemd-resolved** (modern systems):
```bash
# /etc/systemd/resolved.conf
[Resolve]
DNS=1.1.1.1 8.8.8.8
FallbackDNS=9.9.9.9
DNSSEC=yes
DNSOverTLS=yes

# Apply
systemctl restart systemd-resolved
resolvectl status
```

**Direct /etc/resolv.conf** (when not using systemd-resolved):
```
nameserver 1.1.1.1
nameserver 8.8.8.8
options ndots:5
```

**Prevent DHCP from overwriting DNS**:
```bash
# NetworkManager
echo "[main]
dns=none" > /etc/NetworkManager/conf.d/no-dns-update.conf
systemctl restart NetworkManager
```

---

### Network Troubleshooting Checklist

When the user reports network issues, run through this and generate the diagnostic commands:

```bash
# 1. Check interface state
ip link show
ip addr show

# 2. Check routing table
ip route show
ip route get 8.8.8.8

# 3. Test layer 3 connectivity
ping -c 3 192.168.1.1      # gateway
ping -c 3 8.8.8.8          # internet IP (bypasses DNS)
ping -c 3 google.com       # tests DNS + internet

# 4. DNS resolution
resolvectl query google.com
# or: nslookup google.com
# or: dig google.com @1.1.1.1

# 5. Check firewall
iptables -L -n -v
nft list ruleset

# 6. Check listening services
ss -tlnp
# or: netstat -tlnp

# 7. Trace the route
traceroute 8.8.8.8
# or: mtr 8.8.8.8

# 8. Check logs for network errors
journalctl -u NetworkManager --since "1 hour ago"
journalctl -u systemd-networkd --since "1 hour ago"
dmesg | grep -i "eth\|net\|link"
```

---

## Output Format

Write all config files silently to `./network-config/` in the current working directory.

Then print ONLY:

```
✅ Network config written to ./network-config/

▶ Files created:
  [list each file with its destination path on the system]

▶ To apply:
  [exact commands to apply the config — in order]

▶ To verify:
  ip addr show
  ip route show
  ping -c 3 8.8.8.8

⚠️  Test in a second SSH session before disconnecting — wrong network config can lock you out.
```

---

## Safety Rules

- **Always warn** about applying network changes over SSH — wrong config = lockout
- **Always include rollback** — show how to revert if something breaks
- **Validate before apply** — include syntax check commands (`netplan try`, `wg showconf`)
- **`netplan try`** instead of `netplan apply` when possible — auto-reverts after 120s if no confirmation
- **Never disable the primary interface** without confirming there's console/IPMI access

### Safe Remote Apply Sequence

Always include this workflow when the user is applying config over SSH:

```
1. Open a SECOND SSH session to the server — keep it open as a safety net
2. Apply config in the FIRST session
3. From the SECOND session: verify connectivity (ping, ssh test)
4. Only close the first session once verified
5. If something breaks: use the second session to revert
```

For Netplan specifically:
```bash
# netplan try auto-reverts after 120s if you don't confirm
netplan try
# If you can still SSH in:
netplan apply   # or press Enter if prompted
# If locked out — wait 120s, netplan will revert automatically
```

> ⚠️ **Netplan try timer details:**
> - The 120-second countdown starts immediately and **cannot be paused or extended**
> - The revert will fire even if your SSH session is still active — you'll lose the connection
> - **Always test in a SECOND SSH session** before the timer expires
> - If the SECOND session can't connect: wait 120s for automatic revert, do NOT close the first session
