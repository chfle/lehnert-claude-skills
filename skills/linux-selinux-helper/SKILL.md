---
name: linux-selinux-helper
description: Use when user has an SELinux denial, AVC message, permission error on RHEL/Rocky/Alma/Fedora/CentOS, wants to write a custom SELinux policy module, needs to fix file contexts, label ports, manage SELinux booleans, troubleshoot why an application is blocked, or wants to understand SELinux modes, contexts, and policies.
version: 1.0.0
author: Lehnert
---

# Linux SELinux Helper

## Overview

Diagnoses SELinux denials and generates the minimal correct fix — whether that's a boolean toggle, a file context relabel, a port label, or a custom policy module. Never suggests disabling SELinux. Always explains what the denial means and why the fix is correct.

**Language:** Respond in the user's language. Policy files and commands in English.

---

## When to Use

- User pastes an AVC denial or `sealert` output
- User says "SELinux is blocking X", "permission denied after enabling SELinux"
- User wants to write or load a custom `.te` policy module
- User needs to fix file contexts after moving files
- User wants to open a port in SELinux, label a custom port
- User wants to enable/disable an SELinux boolean
- User asks what SELinux mode their system is in
- User asks "should I disable SELinux?" (answer: no — fix it properly)

## When NOT to Use

- User wants to audit DAC permissions (chmod/chown) → general help or `linux-config-auditor`
- User wants AppArmor (Ubuntu) → this skill covers SELinux (RHEL family) only
- User wants to harden the whole server → `linux-security-hardener`

---

## NEVER Suggest This

```bash
# WRONG — never recommend this
setenforce 0
SELINUX=disabled   # in /etc/selinux/config
```

Disabling SELinux removes a critical security layer. Always fix the policy instead. If the user asks to disable it, explain why that's wrong and show the correct fix.

---

## Step 1 — Understand the Problem

Three input types:

| Input | Action |
|-------|--------|
| Raw AVC denial (from `/var/log/audit/audit.log`) | Parse and diagnose |
| `sealert -a /var/log/audit/audit.log` output | Extract the denial and suggestion |
| Description ("nginx can't write to /data/logs") | Generate the likely denial and fix |

If the user describes a problem without providing the AVC message, generate the diagnostic commands first:

```bash
# Find recent SELinux denials
ausearch -m avc -ts recent | audit2why

# Get a human-readable report
sealert -a /var/log/audit/audit.log | tail -100

# List denials since last boot
ausearch -m avc,user_avc -ts boot | audit2allow

# Check SELinux status
sestatus
getenforce
```

---

## Step 2 — Parse the AVC Denial

Given an AVC message, extract these fields:

```
type=AVC msg=audit(1234567890.123:456): avc:  denied  { write } for  pid=1234
  comm="nginx" path="/data/logs/app.log"
  scontext=system_u:system_r:httpd_t:s0
  tcontext=unconfined_u:object_r:default_t:s0
  tclass=file permissive=0
```

| Field | Meaning |
|-------|---------|
| `denied { write }` | The **action** that was blocked (read, write, execute, getattr, etc.) |
| `comm="nginx"` | The **process** that was blocked |
| `path="/data/logs/app.log"` | The **object** being accessed |
| `scontext=...httpd_t...` | The **source context** — the process's SELinux label |
| `tcontext=...default_t...` | The **target context** — the file/resource's SELinux label |
| `tclass=file` | The **object class** (file, dir, tcp_socket, process, etc.) |

The fix strategy depends on the mismatch:
- Wrong **file context** → `semanage fcontext` + `restorecon`
- Wrong **port label** → `semanage port`
- Missing **boolean** → `setsebool`
- Legitimate new access pattern → custom `.te` policy module

---

## Step 3 — Choose the Fix

### Fix 1 — File Context (most common)

Use when: file/directory has the wrong SELinux label (often after moving files or creating dirs in non-standard locations).

```bash
# Check current context
ls -Z /data/logs/

# See what context should be applied
semanage fcontext --list | grep /var/log/nginx

# Apply the correct context persistently
semanage fcontext -a -t httpd_log_t "/data/logs(/.*)?"
restorecon -Rv /data/logs/

# Verify
ls -Z /data/logs/
```

**Common file context types:**

| Path pattern | Correct type | Used by |
|-------------|-------------|---------|
| Web content | `httpd_sys_content_t` | nginx, Apache |
| Web writable dirs | `httpd_sys_rw_content_t` | PHP apps, uploads |
| Web log files | `httpd_log_t` | nginx, Apache |
| Web exec scripts | `httpd_sys_script_exec_t` | CGI scripts |
| PostgreSQL data | `postgresql_db_t` | PostgreSQL |
| MySQL data | `mysqld_db_t` | MySQL/MariaDB |
| SSH keys | `ssh_home_t` | sshd |
| System binaries | `bin_t` | Executables |
| App data (custom) | `var_t` or custom type | Custom apps |

---

### Fix 2 — SELinux Boolean

Use when: a known feature is disabled by policy and can be toggled.

```bash
# List all booleans related to a topic
getsebool -a | grep httpd

# Enable a boolean (current session only)
setsebool httpd_can_network_connect on

# Enable a boolean persistently (survives reboot)
setsebool -P httpd_can_network_connect on

# Check value
getsebool httpd_can_network_connect
```

**Common booleans:**

| Boolean | Effect | Use case |
|---------|--------|----------|
| `httpd_can_network_connect` | Allow httpd to make outbound TCP connections | PHP app calling external API |
| `httpd_can_network_connect_db` | Allow httpd to connect to databases | Web app → database |
| `httpd_enable_cgi` | Allow CGI execution | CGI scripts |
| `httpd_use_nfs` | Allow httpd to access NFS | Web content on NFS mount |
| `httpd_read_user_content` | Allow serving from ~/public_html | User web dirs |
| `allow_user_exec_content` | Allow users to execute content in home dirs | Dev environments |
| `ftpd_anon_write` | Allow anonymous FTP writes | Public FTP upload |
| `samba_export_all_rw` | Allow Samba full read/write | Samba file sharing |
| `ssh_sysadm_login` | Allow sysadm_r login via SSH | Admin SSH access |
| `httpd_can_sendmail` | Allow httpd to send email | Web app sending email |

---

### Fix 3 — Port Label

Use when: a service is listening on a non-standard port.

```bash
# Check what ports are already labeled for a type
semanage port --list | grep http_port_t

# Label a custom port (e.g. nginx on port 8443)
semanage port -a -t http_port_t -p tcp 8443

# Modify an existing label
semanage port -m -t http_port_t -p tcp 8443

# Verify
semanage port --list | grep 8443
```

**Common port types:**

| Port type | Default ports | Service |
|-----------|-------------|---------|
| `http_port_t` | 80, 443, 8080, 8443 | nginx, Apache |
| `ssh_port_t` | 22 | sshd |
| `postgresql_port_t` | 5432 | PostgreSQL |
| `mysqld_port_t` | 3306 | MySQL/MariaDB |
| `redis_port_t` | 6379 | Redis |
| `smtp_port_t` | 25, 465, 587 | Mail servers |
| `dns_port_t` | 53 | named, unbound |

---

### Fix 4 — Custom Policy Module

Use when: none of the above apply — the application legitimately needs access that no boolean covers.

**Full workflow:**

```bash
# Step 1: Put SELinux in permissive mode for the specific type only
# (DO NOT put the whole system in permissive)
semanage permissive -a httpd_t

# Step 2: Reproduce the issue — let all denials be logged
systemctl restart nginx
# ... trigger the failing operations ...

# Step 3: Generate policy from audit log
ausearch -m avc -ts recent -c nginx | audit2allow -M nginx-custom

# This creates two files:
#   nginx-custom.te   ← human-readable policy source
#   nginx-custom.pp   ← compiled policy package

# Step 4: Review the .te file — understand what you're allowing
cat nginx-custom.te

# Step 5: Load the policy module
semodule -i nginx-custom.pp

# Step 6: Remove permissive exception
semanage permissive -d httpd_t

# Step 7: Verify the fix works
systemctl restart nginx
ausearch -m avc -ts recent   # should be empty
```

**Custom .te file template:**

```
module nginx-custom 1.0;

require {
    type httpd_t;
    type var_log_t;
    class file { read write create open getattr };
    class dir { write add_name };
}

# Allow nginx to write to /data/logs (labeled var_log_t)
allow httpd_t var_log_t:file { read write create open getattr };
allow httpd_t var_log_t:dir { write add_name };
```

Compile manually:
```bash
checkmodule -M -m -o nginx-custom.mod nginx-custom.te
semodule_package -o nginx-custom.pp -m nginx-custom.mod
semodule -i nginx-custom.pp
```

---

## SELinux Modes Reference

```bash
# Check current mode
getenforce          # Enforcing / Permissive / Disabled
sestatus            # Full status including policy name

# Temporarily switch to permissive (reverts on reboot)
setenforce 0        # permissive — logs but doesn't block
setenforce 1        # enforcing — logs and blocks

# Permanently change mode in /etc/selinux/config
# (requires reboot)
SELINUX=enforcing   # recommended
SELINUX=permissive  # for troubleshooting only
SELINUX=disabled    # NEVER — removes security layer

# Relabel entire filesystem after mode change from disabled
# (run, then reboot)
touch /.autorelabel
```

---

## Quick Diagnostic Commands

Always provide these when diagnosing an unknown SELinux issue:

```bash
# Show all recent denials, human-readable
ausearch -m avc -ts recent | audit2why

# Show denials for a specific process
ausearch -m avc -c nginx | audit2why

# Full report with suggested fixes
sealert -a /var/log/audit/audit.log 2>/dev/null | grep -A 20 "SELinux is preventing"

# Show context of a file
ls -Z /path/to/file

# Show context of a running process
ps -eZ | grep nginx

# Show all booleans with current value
getsebool -a | grep -i http

# Check what context a path should have
matchpathcon /path/to/check
```

---

## Output Format

For **file context / boolean / port fixes** — print the exact commands directly in chat (no file writing needed — these are one-liners).

For **custom policy modules** — write to `./selinux/`:
```
selinux/
  nginx-custom.te    ← policy source (review before loading)
  build-and-load.sh  ← compile + load script
```

Then print:
```
✅ Custom SELinux policy written to ./selinux/

▶ Review the policy before loading:
  cat selinux/nginx-custom.te

▶ Build and load:
  chmod +x selinux/build-and-load.sh
  sudo ./selinux/build-and-load.sh

▶ Verify no new denials:
  ausearch -m avc -ts recent | audit2why

💡 Next: re-run sealert to confirm the denial is resolved.
```
