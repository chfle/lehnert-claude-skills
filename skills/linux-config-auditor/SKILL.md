---
name: linux-config-auditor
description: Use when user wants to audit, review, analyze, or improve a Linux config file — nginx, Apache, sshd_config, systemd service/timer/socket units, iptables, nftables, firewalld, fail2ban, sudoers, /etc/security/limits.conf, sysctl.conf, or any server config — for security issues, misconfigurations, performance problems, or compliance gaps.
version: 1.3.0
author: Lehnert
---

# Linux Config Auditor

## Overview

Audits any Linux configuration file against security best practices, performance recommendations, and common compliance baselines (CIS, STIG-inspired). Outputs a structured findings report with severity ratings, explanations, and ready-to-apply fix snippets.

**Language:** Respond in the user's language. Config snippets always use the native config syntax.

---

## When to Use

- User pastes or references a config file and wants it reviewed
- User asks "is my nginx config secure?", "check my sshd_config", "audit my firewall rules"
- User wants hardening recommendations for a specific service
- User wants to know why a service behaves unexpectedly (misconfiguration hunt)
- User asks for CIS benchmark or security best-practice review

## When NOT to Use

- User wants a script to automate the fix → use /linux-shell-scriptor
- User wants to generate a config from scratch → generate directly without this skill
- User wants to harden the whole OS (not just one config) → use /linux-security-hardener

---

## Supported Config Types

| Config File / Type | What Gets Checked |
|-------------------|-------------------|
| `nginx.conf` / vhost | TLS versions, cipher suites, headers, rate limiting, open redirects, directory listing, worker tuning |
| `apache2.conf` / `.htaccess` | ServerTokens, TLS, headers, directory access, mod_security hints |
| `sshd_config` | PermitRootLogin, PasswordAuth, key algorithms, ciphers, idle timeout, AllowUsers/Groups |
| `systemd` unit (`.service`, `.timer`, `.socket`) | Sandboxing, privilege escalation, restart policy, resource limits |
| `iptables` / `nftables` rules | Default policies, open ports vs. need, stateful rules, loopback, anti-spoofing |
| `firewalld` config | Active zones, overly permissive services, rich rules |
| `fail2ban` jail config | Enabled jails, ban time, max retry, action severity |
| `sudoers` / `visudo` | NOPASSWD, wildcards, command scope, user/group grants |
| `sysctl.conf` | Network hardening params, kernel pointer restriction, core dump settings |
| `/etc/security/limits.conf` | Open file limits, process limits for services |
| `pg_hba.conf` | Peer/md5/scram auth methods, host access rules, trust entries |
| `mysql/my.cnf` | bind-address, skip-networking, max_connections, log_error, SSL settings |
| `redis.conf` | requirepass, bind, protected-mode, ACL, TLS, dangerous commands |
| Generic / unknown | Best-effort structural review, flag obvious anti-patterns |

---

## Audit Framework

Run findings through three lenses in order:

### 1. Security
Look for settings that increase attack surface, weaken encryption, allow privilege escalation, or violate least-privilege.

### 2. Performance
Look for settings that limit throughput, cause unnecessary latency, misuse resources, or have known bottleneck defaults.

### 3. Compliance / Best Practice
Check against CIS Benchmark guidance (where applicable), vendor hardening docs, and widely accepted defaults.

---

## Output Format

Structure every audit as follows:

### Summary Header
```
Config: <filename or type>
Findings: X critical · Y warnings · Z info · N passed
```

### Findings Table

| Severity | Setting / Line | Issue | Fix |
|----------|---------------|-------|-----|
| 🔴 Critical | `PermitRootLogin yes` | Root login allowed over SSH | Set to `no` or `prohibit-password` |
| 🟠 Warning | `ssl_protocols TLSv1 TLSv1.1 TLSv1.2` | TLS 1.0 and 1.1 are deprecated and vulnerable | Remove TLS 1.0 and 1.1 |
| 🟡 Info | `worker_processes 1` | Single worker — consider matching CPU count | Set `worker_processes auto;` |
| ✅ OK | `PasswordAuthentication no` | Password auth disabled — good | — |

Severity scale:
- 🔴 **Critical** — actively exploitable or violates fundamental security principle
- 🟠 **Warning** — weakens security posture or common attack vector
- 🟡 **Info** — performance gap, minor misconfiguration, or style deviation
- ✅ **OK** — explicitly confirmed as correct, shown for reassurance

**What to include in the table:**
- 🔴 Critical: always include every finding
- 🟠 Warning: always include every finding
- 🟡 Info: include only if 3 or more items; otherwise merge into summary
- ✅ OK: include 1–2 highlights only (the most important security settings that are correctly configured)

### Fixed Config Snippet

After the table, output a corrected config snippet containing **only the changed lines** with brief inline comments:

```nginx
# Fixed settings — replace in your config
ssl_protocols TLSv1.2 TLSv1.3;          # Remove deprecated TLS 1.0/1.1
ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;
worker_processes auto;
```

### Next Steps

Always close with:
> **Apply fixes:** Review each change before applying — test in staging first
> **Full script:** Use `/linux-shell-scriptor` to automate applying these changes
> **OS hardening:** Use `/linux-security-hardener` for a full system-level review
> **Monitor:** Use `/linux-monitoring-setup` to detect anomalies after reconfiguring

---

## File Output

Write findings to `./audit/` in the current working directory:

```
audit/
  report-<type>-<YYYY-MM-DD>.md    ← full findings report
  fixed-<filename>                 ← corrected config (changed lines only)
```

Print the findings table and corrected snippet **directly in chat** — they are concise and immediately useful. The full written report is for archiving and diffing over time.

---

## Nginx-Specific Rules

Check all of the following when auditing any nginx config:

| Check | Secure Value |
|-------|-------------|
| TLS protocols | `TLSv1.2 TLSv1.3` only |
| TLS ciphers | ECDHE + AES-GCM / CHACHA20 only; no RC4, DES, MD5, EXPORT |
| HSTS header | `Strict-Transport-Security` with `max-age` ≥ 15768000 |
| `server_tokens` | `off` |
| `X-Frame-Options` | `SAMEORIGIN` or `DENY` |
| `X-Content-Type-Options` | `nosniff` |
| `Content-Security-Policy` | Present (warn if missing) |
| `client_max_body_size` | Set explicitly (not unlimited) |
| `autoindex` | `off` |
| `worker_processes` | `auto` or matches CPU count |
| `keepalive_timeout` | 65 or lower |
| Rate limiting | `limit_req_zone` defined for public endpoints |
| Default server | Catch-all returning 444 for unknown hostnames |

---

## SSH-Specific Rules

| Check | Secure Value |
|-------|-------------|
| `PermitRootLogin` | `no` or `prohibit-password` |
| `PasswordAuthentication` | `no` |
| `PermitEmptyPasswords` | `no` |
| `PubkeyAuthentication` | `yes` |
| `X11Forwarding` | `no` |
| `AllowTcpForwarding` | `no` (unless needed) |
| `ClientAliveInterval` | 300 or less |
| `ClientAliveCountMax` | 2 or less |
| `MaxAuthTries` | 4 or less |
| `LoginGraceTime` | 30 or less |
| `Ciphers` | Only modern ciphers (no arcfour, blowfish, 3des) |
| `MACs` | Only SHA-2 MACs (no md5, sha1) |
| `AllowUsers` / `AllowGroups` | Defined to restrict access |
| `Port` | Non-default (advisory only) |

---

## Systemd Unit Rules

| Check | Recommendation |
|-------|---------------|
| `NoNewPrivileges=yes` | Always set for non-root services |
| `PrivateTmp=yes` | Isolate /tmp access |
| `ProtectSystem=strict` | Prevent writes to system dirs |
| `ProtectHome=yes` | Block home directory access |
| `ReadOnlyPaths=` | Restrict filesystem to minimum needed |
| `CapabilityBoundingSet=` | Drop all capabilities not needed |
| `Restart=` | Set to `on-failure` for critical services |
| `RestartSec=` | Use with `Restart=` to avoid rapid respawn |
| `User=` / `Group=` | Never run as root unless absolutely required |
| `LimitNOFILE=` | Set for services that open many connections |
| `TimeoutStopSec=` | Set to avoid zombie services on shutdown |

---

## Iptables / Nftables Rules

| Check | Expected |
|-------|---------|
| Default INPUT policy | `DROP` or `REJECT` |
| Default FORWARD policy | `DROP` (unless routing) |
| Default OUTPUT policy | `ACCEPT` (or `DROP` with explicit rules) |
| Loopback accepted | `ACCEPT` for `lo` interface |
| Established/related | `ACCEPT` for `ESTABLISHED,RELATED` |
| ICMP | Limited — accept echo-request, drop rest |
| Anti-spoofing | Drop packets from localhost arriving on external interface |
| Open ports | Flag any port open that isn't clearly needed |
| Logging | At least one LOG rule before the final DROP |

---

## Security Checklist Reminders

Flag these patterns in any config type as 🔴 Critical:

- Hardcoded passwords, API keys, or tokens in config values
- World-readable permissions on config files with secrets (`chmod 644` on `/etc/ssl/private/`)
- `NOPASSWD: ALL` in sudoers
- Wildcard (`*`) in AllowUsers or command grants
- Empty or commented-out authentication requirements
