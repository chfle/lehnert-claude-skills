---
name: linux-performance-tuner
description: Use when user wants to tune Linux performance — kernel parameters, CPU governor, I/O scheduler, memory and swap settings, network stack tuning, database tuning (PostgreSQL, MySQL, Redis), high-throughput or low-latency optimization, profiling slow systems, or asks why their server is slow, has high load, or underperforms.
version: 1.0.0
author: Lehnert
---

# Linux Performance Tuner

## Overview

Generates a tuned sysctl configuration, I/O scheduler settings, CPU governor settings, and workload-specific optimizations based on the user's hardware and use case. Always measures before tuning — includes profiling commands to find the actual bottleneck before applying any change.

**Rule: Profile first, tune second.** Never apply tuning blind. Always identify the bottleneck first.

**Language:** Respond in the user's language. Config files and scripts in English.

---

## When to Use

- User reports high load, slow response times, or poor throughput
- User wants to optimize for a specific workload (web, database, HPC, containers)
- User wants to tune kernel parameters for their server
- User asks about CPU governor, I/O scheduler, huge pages, NUMA
- User wants to profile where their system is spending time
- User asks "why is my server slow?"

## When NOT to Use

- User wants to harden the server → `linux-security-hardener`
- User wants to monitor ongoing performance → `linux-monitoring-setup`
- User wants to tune Docker/container resource limits → `docker-compose-writer`

---

## Step 1 — Find the Bottleneck First

Before generating any tuning, output these diagnostic commands and ask the user to share results if they haven't already:

```bash
# ── 60-second performance snapshot ──────────────────────────
# CPU, memory, I/O, network — one command each

# 1. Overall system health
uptime                          # load averages (1, 5, 15 min)
vmstat 1 10                     # CPU/memory/swap/io per second (10 samples)

# 2. CPU breakdown
mpstat -P ALL 1 5               # per-core CPU usage (5 samples)
top -b -n1 | head -20           # top processes by CPU

# 3. Memory
free -h                         # RAM + swap usage
cat /proc/meminfo | grep -E "MemTotal|MemFree|MemAvailable|Buffers|Cached|SwapUsed"

# 4. I/O
iostat -xz 1 5                  # per-device I/O stats (5 samples)
iotop -bo -n5 2>/dev/null       # top I/O processes

# 5. Network
sar -n DEV 1 5 2>/dev/null      # per-interface throughput
ss -s                           # socket statistics summary
netstat -s | grep -E "failed|error|overflow|retran" 2>/dev/null

# 6. Identify biggest CPU consumers
ps aux --sort=-%cpu | head -10

# 7. Check for kernel errors
dmesg -T | tail -30 | grep -iE "error|warn|oom|throttl"
```

---

## Step 2 — Identify Workload Type

If the bottleneck or workload type isn't clear, present options:

> What is this server's primary workload?
>
> **1. Web / Application server** (nginx, Apache, Node.js, Python, Ruby)
> **2. Database server** (PostgreSQL, MySQL, Redis, MongoDB)
> **3. High-throughput networking** (load balancer, proxy, high-connection count)
> **4. General purpose / mixed** (VPS, dev server, multiple services)
> **5. Compute / HPC** (CPU-intensive batch jobs, scientific computing)
> **6. Storage / I/O heavy** (file server, NFS, large file processing)

---

## Tuning Profiles

### Profile 1 — Web / Application Server

**CPU:**
```bash
# Set performance governor (max CPU frequency)
cpupower frequency-set -g performance
# Or with direct kernel interface:
for cpu in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor; do
  echo performance > "$cpu"
done

# Persist across reboots (systemd)
cat > /etc/systemd/system/cpu-performance.service << 'EOF'
[Unit]
Description=Set CPU governor to performance
After=multi-user.target

[Service]
Type=oneshot
ExecStart=/bin/sh -c 'for f in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor; do echo performance > $f; done'
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF
systemctl enable --now cpu-performance.service
```

**Kernel (`/etc/sysctl.d/99-webserver.conf`):**
```ini
# File descriptor limits
fs.file-max = 2097152

# Network — connection handling
net.core.somaxconn = 65535
net.core.netdev_max_backlog = 65535
net.ipv4.tcp_max_syn_backlog = 65535

# TCP performance
net.ipv4.tcp_fin_timeout = 10
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_keepalive_time = 60
net.ipv4.tcp_keepalive_intvl = 10
net.ipv4.tcp_keepalive_probes = 6
net.ipv4.tcp_slow_start_after_idle = 0

# TCP buffers
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216

# Congestion control (BBR — better on high-bandwidth/latency links)
net.ipv4.tcp_congestion_control = bbr
net.core.default_qdisc = fq

# Memory
vm.swappiness = 10
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5
```

**ulimits (`/etc/security/limits.d/99-webserver.conf`):**
```
*    soft nofile 65536
*    hard nofile 262144
*    soft nproc  65536
*    hard nproc  65536
```

---

### Profile 2 — Database Server

**Kernel (`/etc/sysctl.d/99-database.conf`):**
```ini
# Shared memory for PostgreSQL / MySQL
kernel.shmmax = 68719476736     # 64 GB — set to 75% of RAM
kernel.shmall = 4294967296

# Huge pages (PostgreSQL benefit significantly)
vm.nr_hugepages = 512           # calculate: shared_buffers / 2MB
# Check current: grep HugePages /proc/meminfo

# Reduce swap use — databases need RAM, not swap
vm.swappiness = 1
vm.dirty_ratio = 10
vm.dirty_background_ratio = 3
vm.overcommit_memory = 2        # prevent OOM kills of DB processes
vm.overcommit_ratio = 80

# Network for DB connections
net.core.somaxconn = 4096
net.ipv4.tcp_keepalive_time = 300
```

**I/O scheduler** (critical for database performance):
```bash
# For SSDs / NVMe — use none (noop) or mq-deadline
for disk in /sys/block/sd* /sys/block/nvme*; do
  if [[ -f "${disk}/queue/scheduler" ]]; then
    echo mq-deadline > "${disk}/queue/scheduler"   # SSD
    # echo none > "${disk}/queue/scheduler"         # NVMe
    echo 0 > "${disk}/queue/rotational" 2>/dev/null || true
    echo 256 > "${disk}/queue/nr_requests" 2>/dev/null || true
  fi
done

# For spinning HDDs — use bfq (best fairness) or mq-deadline
# echo bfq > /sys/block/sda/queue/scheduler
```

**PostgreSQL-specific tuning** (`postgresql.conf` guidance):

| Parameter | Formula / Value | Notes |
|-----------|----------------|-------|
| `shared_buffers` | 25% of RAM | e.g. 4GB on 16GB server |
| `effective_cache_size` | 75% of RAM | Planner hint only |
| `work_mem` | RAM / (max_connections × 2) | Per-sort memory |
| `maintenance_work_mem` | 512MB–2GB | VACUUM, CREATE INDEX |
| `wal_buffers` | 64MB | WAL write buffer |
| `checkpoint_completion_target` | 0.9 | Spread checkpoint I/O |
| `random_page_cost` | 1.1 (SSD) / 4.0 (HDD) | Query planner cost |
| `effective_io_concurrency` | 200 (SSD) / 2 (HDD) | Parallel I/O |
| `max_worker_processes` | CPU cores | Background workers |
| `max_parallel_workers_per_gather` | CPU cores / 2 | Query parallelism |

---

### Profile 3 — High-Throughput Networking

**Kernel (`/etc/sysctl.d/99-network.conf`):**
```ini
# Maximize socket buffers
net.core.rmem_max = 134217728
net.core.wmem_max = 134217728
net.core.rmem_default = 262144
net.core.wmem_default = 262144
net.ipv4.tcp_rmem = 4096 262144 134217728
net.ipv4.tcp_wmem = 4096 262144 134217728

# Connection queue
net.core.somaxconn = 65535
net.core.netdev_max_backlog = 300000
net.ipv4.tcp_max_syn_backlog = 65535

# Connection reuse
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 5
net.ipv4.ip_local_port_range = 1024 65535

# BBR congestion control
net.ipv4.tcp_congestion_control = bbr
net.core.default_qdisc = fq

# Interrupt coalescing — reduce CPU overhead per packet
# (set via ethtool, driver-dependent)
# ethtool -C eth0 rx-usecs 50 tx-usecs 50
```

**NIC offloading:**
```bash
# Enable hardware offloading (reduces CPU load for checksums)
ethtool -K eth0 gso on gro on tso on tx on rx on

# For high packet rates — tune interrupt affinity
# Spread NIC interrupts across CPU cores
cat /proc/interrupts | grep eth0
# Then pin each queue to a different CPU
```

---

### Profile 4 — General Purpose VPS

**Kernel (`/etc/sysctl.d/99-general.conf`):**
```ini
# Balanced settings for mixed workloads
vm.swappiness = 10
vm.dirty_ratio = 20
vm.dirty_background_ratio = 5

net.core.somaxconn = 8192
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 15
net.core.rmem_max = 8388608
net.core.wmem_max = 8388608

# Kernel hardening that doesn't hurt performance
kernel.randomize_va_space = 2
fs.file-max = 1048576
```

---

### Profile 5 — Compute / HPC

```bash
# Disable NUMA balancing if workload is NUMA-aware
echo 0 > /proc/sys/kernel/numa_balancing

# Disable transparent huge pages for consistent latency
echo never > /sys/kernel/mm/transparent_hugepage/enabled
echo never > /sys/kernel/mm/transparent_hugepage/defrag

# Isolate CPUs for latency-sensitive processes
# Add to kernel cmdline: isolcpus=2,3,4,5 nohz_full=2-5 rcu_nocbs=2-5

# Set process CPU affinity
taskset -cp 2-5 <pid>

# Set real-time priority
chrt -f 50 /path/to/realtime-process
```

---

## Profiling Tools Reference

Use these to identify the bottleneck before tuning:

| Tool | What it shows | Install |
|------|-------------|---------|
| `perf top` | Live CPU hotspot — which functions consume CPU | `perf` package |
| `perf stat -p <pid>` | CPU counters for a process | `perf` package |
| `flamegraph` | Visual CPU call stack — find hot paths | `FlameGraph` + perf |
| `iostat -xz 1` | Per-disk I/O utilization and latency | `sysstat` |
| `iotop` | Per-process I/O rates | `iotop` |
| `vmstat 1` | CPU/memory/swap/IO overview | Built-in |
| `sar -n DEV 1` | Per-NIC throughput and errors | `sysstat` |
| `ss -s` | Socket state summary | Built-in |
| `atop` | Full resource history with process detail | `atop` |
| `dstat` | Combined CPU/disk/net/mem in one view | `dstat` |
| `strace -p <pid>` | System calls a process is making | Built-in |
| `lsof -p <pid>` | Open files and connections | `lsof` |
| `numactl --hardware` | NUMA topology | `numactl` |
| `turbostat` | CPU frequency and power states | `linux-tools` |

**Quick flamegraph:**
```bash
# Record 30 seconds of CPU stack traces
perf record -F 99 -a -g -- sleep 30
perf script | /path/to/FlameGraph/stackcollapse-perf.pl | \
  /path/to/FlameGraph/flamegraph.pl > flame.svg
# Open flame.svg in browser
```

---

## Output Format

Write tuning files to `./performance/` in the current working directory:

```
performance/
  sysctl.conf         ← drop into /etc/sysctl.d/99-tuning.conf
  limits.conf         ← drop into /etc/security/limits.d/99-tuning.conf
  io-scheduler.sh     ← set I/O scheduler + persist via udev rule
  cpu-governor.sh     ← set CPU governor + systemd service
  apply-all.sh        ← apply everything at once
```

Then print ONLY:

```
✅ Performance tuning written to ./performance/

▶ Review before applying — understand each change:
  cat performance/sysctl.conf

▶ Apply all at once:
  chmod +x performance/apply-all.sh
  sudo ./performance/apply-all.sh

▶ Or apply individually:
  sudo cp performance/sysctl.conf /etc/sysctl.d/99-tuning.conf
  sudo sysctl -p /etc/sysctl.d/99-tuning.conf
  sudo cp performance/limits.conf /etc/security/limits.d/99-tuning.conf

▶ Measure after applying (compare to before):
  vmstat 1 10
  iostat -xz 1 5

💡 Always benchmark before and after — tuning without measurement is guessing.
💡 Next: /linux-monitoring-setup to track metrics continuously after tuning.
```
