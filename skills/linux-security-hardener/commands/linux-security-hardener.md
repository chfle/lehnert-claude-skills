# /linux-security-hardener

Generate a complete server hardening plan with ready-to-apply scripts and configs.

## Usage

```
/linux-security-hardener <OS and server role>
```

## Examples

```
/linux-security-hardener Ubuntu 24.04 web server
/linux-security-hardener fresh Debian 12 VPS
/linux-security-hardener Rocky Linux 9 Docker host
/linux-security-hardener harden my server
```

## Behavior

If OS and server role are provided as an argument, use them directly.
If missing, ask at most two questions (OS version, server role) then generate.

Covers: system updates, user/auth hardening, SSH hardening, firewall (UFW or firewalld), fail2ban, kernel sysctl hardening, disabling unused services, filesystem permissions, and auditd logging.

Writes all output silently to `./hardening/`:
- `harden.sh` — master script, run as root to apply everything at once
- `configs/` — individual drop-in config files per service

Prints only the confirmation, apply commands, and a warning to keep a second SSH session open.

$ARGUMENTS
