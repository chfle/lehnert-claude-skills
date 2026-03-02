# /linux-config-auditor

Audit any Linux config file for security vulnerabilities, misconfigurations, and performance gaps.

## Usage

```
/linux-config-auditor <config type or paste config content>
```

## Examples

```
/linux-config-auditor [paste /etc/ssh/sshd_config]
/linux-config-auditor [paste nginx server block with SSL]
/linux-config-auditor [paste systemd service unit]
/linux-config-auditor [paste /etc/sysctl.conf]
/linux-config-auditor my iptables rules — here: [paste iptables-save output]
/linux-config-auditor [paste fail2ban jail.local]
/linux-config-auditor audit my sudoers file: [paste visudo output]
/linux-config-auditor review my PostgreSQL pg_hba.conf: [paste content]
```

## Supported Config Types

`nginx` · `apache/httpd` · `sshd_config` · `systemd` service/timer/socket · `iptables` · `nftables` · `firewalld` · `fail2ban` · `sudoers` · `sysctl.conf` · `limits.conf` · `pg_hba.conf` · `mysql/my.cnf`

## Generates

```
audit/
  report-<type>-<date>.md    ← full findings report
  fixed-<filename>           ← corrected config file (only changed lines)
```

## Behavior

If config content or a config type is provided, use it directly.
If no argument is given, ask: "Paste the config file you want audited, or tell me the config type."

1. Auto-detect the config type from content syntax or the argument
2. Audit across three lenses: **Security** → **Performance** → **Compliance/Best Practice**
3. Output findings summary: `🔴 Critical: N | 🟠 Warnings: N | 🟡 Info: N | ✅ Passed: N`
4. Print findings table: severity · setting · issue · recommended fix
5. Print the corrected snippet with only the changed lines and inline `# comments`
6. Write the full report and corrected config to `./audit/`
7. Suggest next step: apply the fixes, then use `/linux-security-hardener` for a full server hardening plan

$ARGUMENTS
