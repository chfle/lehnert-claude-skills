# /linux-security-hardener

Generate a complete, ready-to-apply server hardening plan with scripts and drop-in config files.

## Usage

```
/linux-security-hardener <OS version and server role>
```

## Examples

```
/linux-security-hardener Ubuntu 24.04 LTS web server running nginx
/linux-security-hardener fresh Debian 12 VPS — nothing installed yet
/linux-security-hardener Rocky Linux 9 Docker host
/linux-security-hardener CentOS 7 PostgreSQL database server
/linux-security-hardener harden my server — I'll answer your questions
/linux-security-hardener Raspberry Pi 4 running Ubuntu 22.04 as a home server
```

## Generates

```
hardening/
  harden.sh              ← master script — run as root to apply everything
  configs/
    sshd_hardened.conf   ← SSH drop-in (PasswordAuth off, keys only, etc.)
    sysctl-hardened.conf ← kernel hardening parameters
    ufw-rules.sh         ← firewall rules (or firewalld for RHEL-based)
    fail2ban-jail.local  ← fail2ban configuration
    auditd.rules         ← audit logging rules
```

## Behavior

If OS and server role are provided, start generating immediately.
If missing, ask at most two questions: OS version, and primary server role.

Covers 10 hardening areas:
1. System updates and unattended-upgrades
2. User and authentication hardening (disable root login, enforce key-based SSH)
3. SSH lockdown (drop-in config, port, AllowUsers)
4. Firewall (UFW for Debian/Ubuntu, firewalld for RHEL/Rocky)
5. fail2ban (SSH, nginx, custom jails if applicable)
6. Kernel sysctl hardening (network, memory, randomization)
7. Disable unused services and protocols
8. Filesystem permissions and sticky bits
9. auditd logging rules
10. `harden.sh` — master script applying all of the above

Writes everything silently to `./hardening/`.
Prints: apply commands, **⚠️ keep a second SSH session open** warning, and rollback hint.
Suggests: run `/linux-log-analyzer` on `auth.log` after hardening to verify fail2ban is working.

$ARGUMENTS
