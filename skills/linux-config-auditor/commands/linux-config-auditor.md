Audit a Linux config file using the linux-config-auditor skill.

If the user provided config content or a config type as an argument, use it directly.
If no argument was given, ask: "Paste the config file content you want audited, or tell me the config type (e.g. nginx, sshd, systemd unit, iptables, sudoers)."

Then run the full linux-config-auditor workflow:
1. Identify the config type from the content or user's description
2. Run the audit across three lenses: Security → Performance → Compliance/Best Practice
3. Output the findings in this exact structure:
   - Summary header: config name, count of critical / warnings / info / passed
   - Findings table with severity (🔴 Critical / 🟠 Warning / 🟡 Info / ✅ OK), the setting or line, the issue, and the fix
   - Fixed config snippet containing only the changed lines with inline comments
   - Next steps (apply fixes, link to related skills)

$ARGUMENTS
