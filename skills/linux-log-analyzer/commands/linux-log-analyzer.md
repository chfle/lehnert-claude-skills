# /linux-log-analyzer

Analyze any Linux log file or pasted output — finds errors, security threats, crashes, brute-force attacks, and anomalies.

## Usage

```
/linux-log-analyzer <paste log content or file path>
```

## Examples

```
/linux-log-analyzer [paste /var/log/auth.log content]
/linux-log-analyzer [paste journalctl -u nginx --since "1 hour ago" output]
/linux-log-analyzer [paste Docker container logs from crashed container]
/linux-log-analyzer [paste /var/log/syslog]
/linux-log-analyzer [paste fail2ban.log to see what IPs were banned]
/linux-log-analyzer [paste nginx error.log showing 502 errors]
/linux-log-analyzer [paste PostgreSQL log with slow query warnings]
/linux-log-analyzer [paste kernel dmesg output after crash]
```

## Generates

```
log-analysis/
  report-<type>-<YYYY-MM-DD>.md    ← full structured analysis report
```

## Behavior

If log content or a path is provided, analyze it immediately.
If no input is given, ask: "Paste the log content you want analyzed, or give me a file path."

Runs 6 analysis passes in order:
1. **Error & warning summary** — categorized by severity, top recurring patterns
2. **Security threat detection** — brute-force attempts, port scans, exploit probes, privilege escalation
3. **Performance & stability issues** — OOM kills, timeouts, connection drops, high error rates
4. **Notable events timeline** — restarts, failures, config reloads, first/last events
5. **IP intelligence** — top offending IPs, geo hints, known bad patterns
6. **Extended analysis** — spike detection, cross-event correlation, trend analysis, DB slow queries, HTTP error patterns, kernel/ext4 deep dive (runs if data is rich enough)

Writes the full structured report to `./log-analysis/report-<type>-<date>.md`.
Prints only: report path, severity counts, top security finding, top issue, and 3 recommended actions.
Suggests: `/linux-security-hardener` if brute-force is detected, `/linux-monitoring-setup` for ongoing alerting.

$ARGUMENTS
