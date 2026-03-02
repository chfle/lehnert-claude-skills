# /linux-config-auditor

Audit a Linux config file for security issues, misconfigurations, and performance gaps.

## Usage

```
/linux-config-auditor <config type or paste config content>
```

## Examples

```
/linux-config-auditor sshd_config
/linux-config-auditor [paste nginx vhost content]
/linux-config-auditor [paste systemd service unit]
/linux-config-auditor iptables rules
```

## Supported Configs

`nginx`, `apache`, `sshd_config`, `systemd` service/timer/socket, `iptables`, `nftables`, `firewalld`, `fail2ban`, `sudoers`, `sysctl.conf`, `/etc/security/limits.conf`

## Behavior

If config content or a config type is provided as an argument, use it directly.
If no argument is given, ask: "Paste the config file content you want audited, or describe the config type."

Then run the full linux-config-auditor workflow:
1. Identify the config type from content or description
2. Audit across three lenses: Security → Performance → Compliance/Best Practice
3. Output the findings summary (critical / warnings / info / passed count)
4. Print the findings table: severity (🔴/🟠/🟡/✅), setting, issue, fix
5. Print a corrected snippet with only the changed lines and inline comments
6. Suggest next steps

$ARGUMENTS
