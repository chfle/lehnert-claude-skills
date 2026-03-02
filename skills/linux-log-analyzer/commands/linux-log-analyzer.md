# /linux-log-analyzer

Analyze any Linux log file or pasted log content — finds errors, security threats, crashes, and anomalies.

## Usage

```
/linux-log-analyzer <paste log content or give a file path>
```

## Examples

```
/linux-log-analyzer [paste auth.log content]
/linux-log-analyzer /var/log/nginx/error.log
/linux-log-analyzer [paste journalctl -u nginx output]
/linux-log-analyzer [paste Docker container logs]
```

## Behavior

If log content or a path is provided, analyze it immediately.
If no input is given, ask: "Paste the log content you want analyzed, or give me a file path."

Then run the full linux-log-analyzer workflow:
1. Auto-detect the log type from content patterns
2. Run 5 analysis passes: error/warning summary, security threat detection, performance/stability issues, notable events timeline, IP intelligence
3. Write the full structured report to `./log-analysis/report-[type]-[date].md`
4. Print only: report path, severity counts, top security finding, top issue, and 3 recommended actions

$ARGUMENTS
