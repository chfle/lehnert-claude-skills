# /linux-performance-tuner

Profile a Linux system, find the bottleneck, and generate tuned sysctl, CPU governor, I/O scheduler, and workload-specific configs.

## Usage

```
/linux-performance-tuner [workload type, description, or diagnostic output]
```

## Examples

```
/linux-performance-tuner my server has high load — here's vmstat: [paste output]
/linux-performance-tuner tune for nginx web server, 32 cores, 64GB RAM
/linux-performance-tuner postgresql database server on NVMe SSD
/linux-performance-tuner high-throughput load balancer, 100k concurrent connections
/linux-performance-tuner HPC compute node — I want max single-thread performance
/linux-performance-tuner general VPS — mixed workload, low latency
/linux-performance-tuner why is my server slow? I'll paste the stats
```

## Generates

```
performance/
  sysctl.conf        ← drop into /etc/sysctl.d/99-tuning.conf
  limits.conf        ← drop into /etc/security/limits.d/99-tuning.conf
  io-scheduler.sh    ← sets I/O scheduler + udev rule for persistence
  cpu-governor.sh    ← sets CPU governor + systemd service to persist across reboots
  apply-all.sh       ← applies everything at once (sysctl, limits, I/O, governor)
```

## Behavior

If no diagnostic data is provided, output the **60-second profiling snapshot** commands and ask for the results before tuning.
If workload type is provided without diagnostic data, ask: "Do you have vmstat/iostat output, or shall I generate the profile directly?"

1. Run or receive the diagnostic snapshot (vmstat, iostat, mpstat, free, ss, dmesg)
2. Identify the bottleneck: CPU-bound / memory pressure / I/O wait / network saturation / misconfigured limits
3. Ask for workload type if not clear:
   - **Web / App server** — nginx, Apache, Node.js, Python
   - **Database** — PostgreSQL, MySQL, Redis, MongoDB
   - **High-throughput networking** — load balancer, proxy, 10k+ connections
   - **General purpose / VPS** — mixed workload
   - **Compute / HPC** — CPU-intensive batch jobs
   - **Storage / I/O heavy** — file server, NFS, large file processing
4. Generate all 5 tuning files for the detected profile
5. Write everything silently to `./performance/`
6. Print: review command, `apply-all.sh` command, before/after benchmark commands
7. Suggest: use `/linux-monitoring-setup` to track metrics after applying the tuning

$ARGUMENTS
