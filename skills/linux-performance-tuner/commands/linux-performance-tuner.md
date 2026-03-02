# /linux-performance-tuner

Profile a Linux system and generate tuned sysctl, CPU governor, I/O scheduler, and workload-specific optimization configs.

## Usage

```
/linux-performance-tuner [workload type or description]
```

## Examples

```
/linux-performance-tuner
/linux-performance-tuner web server nginx
/linux-performance-tuner postgresql database 64GB RAM
/linux-performance-tuner high-throughput load balancer
/linux-performance-tuner why is my server slow
```

## Behavior

1. If no diagnostic data provided, output the 60-second profiling snapshot commands and ask the user to share the results
2. Ask for workload type if not clear (web, database, networking, general, HPC, storage)
3. Generate tuning files tailored to the workload and bottleneck found:
   - `performance/sysctl.conf` — kernel parameters
   - `performance/limits.conf` — ulimits
   - `performance/io-scheduler.sh` — I/O scheduler + udev persistence
   - `performance/cpu-governor.sh` — CPU governor + systemd service
   - `performance/apply-all.sh` — apply everything at once
4. Print confirmation with review and apply commands

$ARGUMENTS
