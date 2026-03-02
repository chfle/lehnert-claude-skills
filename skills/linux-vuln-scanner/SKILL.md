---
name: linux-vuln-scanner
description: Use when user wants to scan a Linux server, container, or web app for vulnerabilities — checking installed packages against CVE databases, scanning open ports and services, auditing SSL/TLS configuration, running web application security scans, or performing an authorized security assessment of their own infrastructure.
version: 1.0.0
author: Lehnert
---

# Linux Vulnerability Scanner

## Overview

Generates a complete vulnerability assessment workflow for Linux servers, Docker containers, and web applications. Uses open-source tools: **Trivy** (CVE scanning), **nmap** (port/service discovery), **testssl.sh** (TLS audit), and **Nikto** (web app scanning). Writes all scan scripts to disk, never outputs raw findings in chat.

**Language:** Respond in the user's language. Commands and configs in English.

---

## When to Use

- User wants to check if their server or containers have known CVEs
- User wants to see what ports/services are exposed to the internet
- User wants to audit their SSL/TLS configuration
- User wants to run a web application security scan
- User is preparing for a security audit or compliance review
- User asks "is my server vulnerable?" or "what CVEs affect my system?"

## When NOT to Use

- User wants to harden a server → `/linux-security-hardener`
- User wants to audit config files → `/linux-config-auditor`
- User wants to scan systems they **do not own** → refuse

---

## ⚠️ Authorization Requirement

Before generating any scan commands, confirm:

> **Only run these scans on systems you own or have explicit written permission to test.**
> Unauthorized scanning is illegal in most jurisdictions regardless of intent.
> Keep all scan results confidential — they reveal attack surface to anyone who reads them.

If the user's target is not their own infrastructure, do not proceed.

---

## Step 1 — Detect Scan Type

| User says | Scan profile |
|-----------|-------------|
| "Scan for CVEs", "check my packages" | Profile 1: OS/Package CVE scan (Trivy) |
| "Check my Docker containers / images" | Profile 1: Container image scan (Trivy) |
| "What ports are open?" | Profile 2: Port & service scan (nmap) |
| "Audit my SSL/TLS", "check my certificates" | Profile 3: TLS audit (testssl.sh) |
| "Scan my web app", "check for XSS/injection" | Profile 4: Web app scan (Nikto) |
| "Scan my dependencies / npm / pip / maven" | Profile 5: Dependency CVE scan |
| "Full security assessment", "scan everything" | All profiles + full-scan.sh |

---

## Scan Profiles

### Profile 1 — OS/Package CVE Scan (Trivy)

Trivy scans the local OS or Docker images against NVD + OS advisory databases.

**Install:**
```bash
# Debian/Ubuntu
apt install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key \
  | gpg --dearmor > /etc/apt/trusted.gpg.d/trivy.gpg
echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" \
  > /etc/apt/sources.list.d/trivy.list
apt update && apt install trivy

# RHEL/Rocky
cat > /etc/yum.repos.d/trivy.repo << 'EOF'
[trivy]
name=Trivy repository
baseurl=https://aquasecurity.github.io/trivy-repo/rpm/releases/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://aquasecurity.github.io/trivy-repo/rpm/public.key
EOF
dnf install trivy
```

**Scan commands:**
```bash
# Scan local filesystem for OS package CVEs
trivy fs / \
  --scanners vuln \
  --severity HIGH,CRITICAL \
  --format table \
  --output /tmp/trivy-os-report.txt

# Scan a specific Docker image
trivy image nginx:latest \
  --severity HIGH,CRITICAL \
  --format table \
  --output /tmp/trivy-image-report.txt

# Scan all running containers
docker ps --format '{{.Image}}' | sort -u | while read img; do
  echo "=== Scanning: $img ===" >> /tmp/trivy-containers.txt
  trivy image "$img" --severity HIGH,CRITICAL --quiet >> /tmp/trivy-containers.txt 2>&1
done

# Scan for misconfigurations too
trivy fs . --scanners misconfig --format table
```

**Severity triage:**

| Severity | Action |
|----------|--------|
| CRITICAL | Patch immediately — active exploits likely exist |
| HIGH | Patch within 48 hours |
| MEDIUM | Patch within 1–2 weeks |
| LOW | Patch at next maintenance window |

---

### Profile 2 — Port & Service Discovery (nmap)

nmap maps open ports and identifies running services and versions.

**Install:** `apt install nmap` / `dnf install nmap`

```bash
# Quick scan — top 1000 ports, service detection
nmap -sV -sC --open TARGET_IP \
  -oA /tmp/nmap-quick

# Full TCP scan — all 65535 ports (slower but thorough)
nmap -p- -sV --open TARGET_IP \
  -oA /tmp/nmap-full

# UDP scan — DNS, SNMP, NTP, TFTP
nmap -sU --top-ports 50 TARGET_IP \
  -oA /tmp/nmap-udp

# Scan from inside the server (loopback + all interfaces)
nmap -sV -p- localhost

# Run vuln detection scripts
nmap --script vuln TARGET_IP \
  -oA /tmp/nmap-vulns
```

**High-risk findings:**

| Finding | Risk |
|---------|------|
| Port 23 (Telnet) open | Critical — unencrypted remote shell |
| Port 21 (FTP) open | High — unencrypted file transfer |
| Database ports (3306, 5432, 6379, 27017) exposed to internet | Critical |
| SSH on 22 exposed to internet | High — restrict to VPN/allowlist |
| Services with outdated version strings | High — cross-check CVE databases |

---

### Profile 3 — SSL/TLS Audit (testssl.sh)

testssl.sh audits protocols, ciphers, certificates, and known vulnerabilities.

**Install:**
```bash
git clone --depth 1 https://github.com/drwetter/testssl.sh.git /opt/testssl.sh
```

**Scans:**
```bash
# Full audit with HTML report
/opt/testssl.sh/testssl.sh \
  --htmlfile /tmp/testssl-report.html \
  --logfile /tmp/testssl-report.txt \
  https://yourdomain.com

# Check only known vulnerabilities (Heartbleed, POODLE, ROBOT, etc.)
/opt/testssl.sh/testssl.sh --vulnerable https://yourdomain.com

# Check certificate expiry and chain
/opt/testssl.sh/testssl.sh --certinfo https://yourdomain.com

# Non-HTTPS port (e.g. SMTP STARTTLS)
/opt/testssl.sh/testssl.sh yourdomain.com:587
```

**Issues to flag immediately:**

| Finding | Risk | Fix |
|---------|------|-----|
| SSLv2 / SSLv3 enabled | Critical | Disable in nginx/Apache config |
| TLS 1.0 / 1.1 enabled | High | PCI DSS requires TLS 1.2+ |
| Heartbleed (CVE-2014-0160) | Critical | Update OpenSSL |
| RC4 ciphers | High | Remove from cipher list |
| Certificate expired | Critical | Renew immediately |
| Self-signed certificate | Medium | Use Let's Encrypt |
| RSA key < 2048-bit | High | Regenerate with 4096-bit or EC |

---

### Profile 4 — Web Application Scan (Nikto)

Nikto performs non-destructive scanning for common web vulnerabilities.

**Install:** `apt install nikto` / `dnf install nikto`

```bash
# Basic scan with HTML output
nikto -h https://yourdomain.com \
  -o /tmp/nikto-report.html \
  -Format html

# Scan with authentication
nikto -h https://yourdomain.com \
  -id username:password \
  -o /tmp/nikto-auth.txt

# Targeted: injection tests only
nikto -h https://yourdomain.com -Tuning 9   # SQL injection
nikto -h https://yourdomain.com -Tuning 4   # XSS / HTML injection
nikto -h https://yourdomain.com -Tuning 1 2 # Interesting files + misconfigs
```

**Nikto tuning flags:**

| Flag | Tests |
|------|-------|
| `1` | Interesting files (backup files, .git, .env) |
| `2` | Misconfiguration / default credentials |
| `3` | Information disclosure (server headers, stack traces) |
| `4` | Injection (XSS, HTML injection) |
| `9` | SQL injection |

---

### Profile 5 — Dependency CVE Scan

```bash
# Node.js (built-in)
npm audit --audit-level=high
npm audit fix   # auto-fix where safe

# Python
pip install pip-audit
pip-audit -r requirements.txt

# Java (Maven)
./mvnw org.owasp:dependency-check-maven:check
# Report: target/dependency-check-report.html

# Java (Gradle)
# Add to build.gradle: id "org.owasp.dependencycheck" version "9.0.7"
./gradlew dependencyCheckAnalyze

# Trivy for any repo
trivy fs . --scanners vuln --format table
```

---

## Full-Assessment Playbook

Generate `vuln-scan/full-scan.sh` when user asks for a complete assessment:

```bash
#!/usr/bin/env bash
# full-scan.sh — runs all scan types, saves to /tmp/security-assessment/
set -euo pipefail

TARGET="${1:?Usage: $0 <TARGET_IP_OR_HOSTNAME>}"
OUTPUT_DIR="/tmp/security-assessment-$(date +%Y%m%d_%H%M%S)"
mkdir -p "$OUTPUT_DIR"

echo "[1/4] Trivy OS CVE scan..."
trivy fs / --scanners vuln --severity HIGH,CRITICAL \
  --format table --output "$OUTPUT_DIR/trivy-os.txt" 2>/dev/null || true

echo "[2/4] nmap port scan..."
nmap -sV -p- --open "$TARGET" -oA "$OUTPUT_DIR/nmap" 2>/dev/null || true

echo "[3/4] testssl.sh TLS audit..."
if [[ -x /opt/testssl.sh/testssl.sh ]]; then
  /opt/testssl.sh/testssl.sh \
    --htmlfile "$OUTPUT_DIR/testssl.html" \
    --logfile "$OUTPUT_DIR/testssl.txt" \
    "https://$TARGET" 2>/dev/null || true
else
  echo "testssl.sh not installed — skipping TLS audit" >> "$OUTPUT_DIR/testssl.txt"
fi

echo "[4/4] Nikto web scan..."
nikto -h "https://$TARGET" -o "$OUTPUT_DIR/nikto.html" -Format html 2>/dev/null || true

echo "✓ All scans complete."
echo "  Reports saved to: $OUTPUT_DIR/"
echo "  Start with CRITICAL/HIGH findings in: $OUTPUT_DIR/trivy-os.txt"
```

---

## Output Format

Write scripts to `./vuln-scan/` in the current working directory:

```
vuln-scan/
  install-tools.sh     ← installs Trivy, nmap, testssl.sh, Nikto
  full-scan.sh         ← runs all scans against a target
  trivy-scan.sh        ← CVE scan only
  nmap-scan.sh         ← port scan only
  tls-audit.sh         ← TLS audit only
  nikto-scan.sh        ← web app scan only
```

Then print ONLY:

```
✅ Vulnerability scan scripts created in ./vuln-scan/

⚠️  Run ONLY on systems you own or have written authorization to test.

▶ Install tools (run once as root):
  chmod +x vuln-scan/install-tools.sh
  sudo ./vuln-scan/install-tools.sh

▶ Run full assessment:
  chmod +x vuln-scan/full-scan.sh
  sudo ./vuln-scan/full-scan.sh <TARGET_IP_OR_HOSTNAME>

▶ Reports saved to:
  /tmp/security-assessment-YYYYMMDD_HHMMSS/

💡 Review CRITICAL and HIGH findings first.
💡 Next: /linux-security-hardener to remediate discovered issues.
💡 Next: /linux-config-auditor to audit specific service configs.
```
