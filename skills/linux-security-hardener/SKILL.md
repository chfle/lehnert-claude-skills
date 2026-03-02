---
name: linux-security-hardener
description: Use when user wants to harden a Linux server, secure a fresh VPS, follow CIS benchmarks, reduce attack surface, lock down SSH, configure a firewall, set up fail2ban, disable unused services, configure auditd, or asks how to make a Linux system more secure.
version: 1.1.0
author: Lehnert
---

# Linux Security Hardener

## Overview

Produces a complete, prioritized server hardening plan tailored to the user's OS, role, and exposure level. Generates all config changes and scripts as ready-to-apply files — the user runs one script and the system is hardened. Based on CIS Benchmark principles, STIG guidance, and real-world best practices.

**Language:** Respond in the user's language. All generated scripts and configs use English.

---

## When to Use

- Fresh VPS or server that needs hardening before going live
- Existing server the user wants to audit and secure
- User asks "how do I secure my Linux server?"
- Post-install checklist for Ubuntu, Debian, RHEL, Rocky, AlmaLinux, Arch
- User wants to lock down SSH, set up a firewall, configure fail2ban, or harden the kernel

## When NOT to Use

- User wants to audit a single config file → /linux-config-auditor
- User wants to write a script for a specific task → /linux-shell-scriptor
- User wants SELinux-specific help → /linux-selinux-helper

---

## Step 1 — Gather Context

Ask the user at most **two** questions before generating. If the OS and server role are clear from the conversation, skip straight to output.

**Question 1** (if not known): "Which OS and version is this? (e.g. Ubuntu 24.04, Debian 12, Rocky Linux 9)"

**Question 2** (if not known): "What is this server's role?" — offer choices:
- Web server (nginx/Apache)
- Database server
- Docker host
- Mail server
- VPN / jump host
- General purpose / personal VPS
- Other

If the user says "just harden it" or gives a vague answer, default to **Ubuntu 24.04 LTS, general-purpose VPS** and state that assumption.

---

## Step 2 — Generate Hardening Plan

Produce a structured plan covering all sections below. For each section, generate the actual config or script — not just advice.

---

### Section 1 — System Updates

```bash
# Ensure system is fully updated
apt-get update && apt-get upgrade -y    # Debian/Ubuntu
# dnf upgrade -y                        # RHEL/Rocky/Alma
```

Enable unattended security updates:
- Ubuntu/Debian: `unattended-upgrades` package configured for security-only
- RHEL/Rocky: `dnf-automatic` with `apply_updates = yes`

---

### Section 2 — User & Authentication Hardening

**Actions to generate:**

| Action | Implementation |
|--------|---------------|
| Disable root login | `passwd -l root` |
| Create a sudo user | `useradd -m -s /bin/bash -G sudo <username>` |
| Enforce strong password policy | `/etc/security/pwquality.conf`: minlen=14, dcredit=-1, ucredit=-1, special=-1 |
| Password aging | `/etc/login.defs`: PASS_MAX_DAYS 90, PASS_MIN_DAYS 1, PASS_WARN_AGE 14 |
| Lock idle accounts | `useradd` default shell `/sbin/nologin` for system accounts |
| Sudo timeout | `/etc/sudoers.d/timeout`: `Defaults timestamp_timeout=15` |
| Disable empty passwords | Verify `/etc/shadow` has no empty password fields |

---

### Section 3 — SSH Hardening

Generate a complete hardened `/etc/ssh/sshd_config.d/99-hardening.conf` (drop-in file, doesn't replace the main config):

```ini
# SSH Hardening — drop-in config
Port 22                          # Change to non-standard port if desired
Protocol 2
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PermitEmptyPasswords no
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding no
PrintMotd no
AllowTcpForwarding no
AllowAgentForwarding no
PermitUserEnvironment no
ClientAliveInterval 300
ClientAliveCountMax 2
MaxAuthTries 3
MaxSessions 4
LoginGraceTime 30
Banner /etc/issue.net
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com
MACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com
KexAlgorithms curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group16-sha512
```

Also generate `/etc/issue.net` banner:
```
Unauthorized access to this system is prohibited.
All activity is monitored and logged.
```

---

### Section 4 — Firewall (UFW or firewalld)

**For Ubuntu/Debian — UFW:**
```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp        # SSH — change port if you changed SSH port above
ufw allow 80/tcp        # HTTP  — remove if not a web server
ufw allow 443/tcp       # HTTPS — remove if not a web server
ufw enable
ufw status verbose
```

**For RHEL/Rocky/Alma — firewalld:**
```bash
systemctl enable --now firewalld
firewall-cmd --permanent --set-default-zone=drop
firewall-cmd --permanent --add-service=ssh
firewall-cmd --permanent --add-service=http   # remove if not needed
firewall-cmd --permanent --add-service=https  # remove if not needed
firewall-cmd --reload
firewall-cmd --list-all
```

Adjust open ports based on the server role provided by the user.

---

### Section 5 — fail2ban

Generate `/etc/fail2ban/jail.d/hardening.conf`:

```ini
[DEFAULT]
bantime  = 3600
findtime = 600
maxretry = 5
banaction = iptables-multiport

[sshd]
enabled  = true
port     = ssh
logpath  = %(sshd_log)s
maxretry = 3
bantime  = 86400

[sshd-ddos]
enabled  = true
port     = ssh
logpath  = %(sshd_log)s
maxretry = 10
findtime = 60
bantime  = 86400
```

```bash
apt-get install -y fail2ban
systemctl enable --now fail2ban
```

---

### Section 6 — Kernel Hardening (sysctl)

Generate `/etc/sysctl.d/99-hardening.conf`:

```ini
# Network hardening
net.ipv4.ip_forward = 0        # EXCEPTION: set to 1 for Docker hosts, VPN servers, and routers
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
net.ipv4.icmp_echo_ignore_broadcasts = 1
net.ipv4.icmp_ignore_bogus_error_responses = 1
net.ipv4.tcp_syncookies = 1
# Disable IPv6 only if user confirmed they don't need it (ask first — default is KEEP enabled)
# net.ipv6.conf.all.disable_ipv6 = 1
# net.ipv6.conf.default.disable_ipv6 = 1

# Kernel hardening
kernel.randomize_va_space = 2
kernel.dmesg_restrict = 1
kernel.kptr_restrict = 2
kernel.yama.ptrace_scope = 1
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
fs.suid_dumpable = 0
```

Apply: `sysctl -p /etc/sysctl.d/99-hardening.conf`

---

### Section 7 — Disable Unnecessary Services

```bash
# List enabled services
systemctl list-unit-files --state=enabled

# Disable common unnecessary services (adjust per server role)
for svc in avahi-daemon cups bluetooth rpcbind nfs-server nis telnet; do
  systemctl disable --now "$svc" 2>/dev/null || true
done
```

---

### Section 8 — File System Hardening

```bash
# Remove SUID/SGID from unnecessary binaries
find / -xdev \( -perm -4000 -o -perm -2000 \) -type f 2>/dev/null

# Secure /tmp with noexec,nosuid,nodev
# Add to /etc/fstab:
# tmpfs /tmp tmpfs defaults,noexec,nosuid,nodev 0 0
```

Permissions to enforce:
```bash
chmod 700 /root
chmod 600 /etc/crontab
chmod 700 /etc/cron.d /etc/cron.daily /etc/cron.weekly /etc/cron.monthly
chmod 644 /etc/passwd /etc/group
chmod 000 /etc/shadow /etc/gshadow
chown root:root /etc/passwd /etc/shadow /etc/group /etc/gshadow
```

---

### Section 9 — Audit Logging (auditd)

```bash
apt-get install -y auditd audispd-plugins
systemctl enable --now auditd
```

Generate `/etc/audit/rules.d/99-hardening.rules`:
```
# Monitor authentication
-w /etc/passwd -p wa -k identity
-w /etc/shadow -p wa -k identity
-w /etc/sudoers -p wa -k sudo_changes
-w /etc/ssh/sshd_config -p wa -k sshd_config

# Monitor privilege escalation
-a always,exit -F arch=b64 -S execve -F euid=0 -k root_commands

# Monitor login events
-w /var/log/auth.log -p wa -k auth_log
-w /var/log/faillog -p wa -k login_failure

# Monitor cron
-w /etc/cron.allow -p wa -k cron
-w /etc/cron.deny -p wa -k cron
-w /var/spool/cron -p wa -k cron

# Immutable rules — requires reboot to change
-e 2
```

---

### Section 10 — Automatic Hardening Script

Consolidate everything into a single `harden.sh` script the user can run as root. The script must:
- Check it's running as root
- Detect the OS and version automatically
- Apply all sections above in order
- Log every action to `/var/log/hardening-$(date +%Y%m%d).log`
- Print a summary at the end of what was done

---

## Output Format

Write all files to a `hardening/` directory in the current working directory:

```
hardening/
  harden.sh                              ← master script (run this)
  configs/
    sshd_hardening.conf                  ← drop into /etc/ssh/sshd_config.d/
    sysctl_hardening.conf                ← drop into /etc/sysctl.d/
    fail2ban_hardening.conf              ← drop into /etc/fail2ban/jail.d/
    audit_hardening.rules                ← drop into /etc/audit/rules.d/
    issue.net                            ← SSH login banner
```

Then print ONLY this to chat:

```
✅ Hardening plan created in ./hardening/

▶ To apply everything at once (run as root):
  chmod +x hardening/harden.sh
  sudo ./hardening/harden.sh

▶ To apply manually section by section:
  sudo cp hardening/configs/sshd_hardening.conf /etc/ssh/sshd_config.d/
  sudo sshd -t && sudo systemctl restart sshd
  sudo cp hardening/configs/sysctl_hardening.conf /etc/sysctl.d/
  sudo sysctl -p /etc/sysctl.d/sysctl_hardening.conf
  sudo cp hardening/configs/fail2ban_hardening.conf /etc/fail2ban/jail.d/
  sudo systemctl restart fail2ban
  sudo cp hardening/configs/audit_hardening.rules /etc/audit/rules.d/
  sudo augenrules --load

⚠️  Test SSH in a second session before closing your current one!
    If SSH breaks: sudo sshd -t  to check the config.

💡 Next: Run /linux-log-analyzer on auth.log after hardening to verify fail2ban is working.
💡 Then: /linux-monitoring-setup to get alerts for future intrusion attempts.
```

---

## Safety Rules

- **Always warn** to keep an active SSH session open while testing changes
- **Always run `sshd -t`** to validate SSH config before restarting
- **Never disable IPv6** without asking if the user needs it
- **Document every change** in harden.sh with a comment explaining why
- If the user's role is "Docker host" — do NOT disable `net.ipv4.ip_forward`, Docker needs it
