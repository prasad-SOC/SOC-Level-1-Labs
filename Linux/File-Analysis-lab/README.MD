# 🧠 Linux File Analysis Lab

## 🎯 Objective

Create a realistic, **safe** simulated threat environment and perform a structured file analysis to determine intent, indicators, and persistence mechanisms.

---

## ⚙️ Lab Setup — create the simulated (benign) threat files

> The files created by the commands below are intentionally harmless but are structured to *look* and *behave* like common suspicious artifacts. Run these commands inside a disposable VM or lab environment.

```bash
mkdir -p ~/labs/file_analysis && cd ~/labs/file_analysis

# 1) suspicious.sh — simulates an obfuscated downloader + persistence indicator
cat > suspicious.sh <<'EOS'
#!/usr/bin/env bash
# harmless simulated payload
echo "[+] simulated payload running" > /tmp/payload_marker.txt
# base64-encoded harmless message to mimic obfuscation
b64="ZWNobyAnU2FtcGxlIGRyb3BwZXIgZXhlY3V0ZWQn"
echo "$b64" | base64 -d >> /tmp/dropper_output.txt
# write a cron-like stub to /tmp to simulate persistence artifacts
echo "# @reboot /tmp/suspicious.sh" > /tmp/sim_cron
chmod +x suspicious.sh
EOS

# 2) dropper.c — benign compiled program that writes a marker
cat > dropper.c <<'EOS'
#include <stdio.h>
#include <stdlib.h>
int main(){
    FILE *f = fopen("/tmp/hello_marker.txt","w");
    if(f){ fprintf(f,"dropper ran\n"); fclose(f); }
    printf("dropper: harmless sample\n");
    return 0;
}
EOS

gcc -o dropper dropper.c

# 3) masquerade.txt — looks like an exfil log with an IP-like string (not real)
cat > masquerade.txt <<'EOS'
user: backup
file: /var/log/syslog
dest: 192.0.2.123:8080
# NOTE: 192.0.2.0/24 is TEST-NET-1 and safe for examples
EOS

# 4) README_TRIGGER — an innocuous-looking README that references 'update.sh'
cat > README_TRIGGER <<'EOS'
Run update.sh to apply latest patches.
EOS

# (optional) protect files with limited permissions to mimic real artifacts
chmod 644 suspicious.sh masquerade.txt README_TRIGGER
chmod 755 dropper
```

---

## 🧩 Stages, Tasks and Hints

> The lab follows stages similar to the Log Analysis lab. Each stage lists tasks and a short hint — **no answers**. Use your VM tools to complete the tasks.

### Stage 1 — Environment Recon & File Inventory

**Tasks**

* List files in the target directory and note file sizes and permissions.
* Identify files that look out of place based on names or locations.

**Hints**

* Use `ls -lha` and `stat` to collect metadata. Look for files owned by unexpected users or with unusual timestamps.

---

### Stage 2 — File Identification

**Tasks**

* Determine the type of each suspicious file (script, ELF binary, plain text).
* Note architecture (32-bit vs 64-bit) for binaries.

**Hints**

* `file` is your friend. For binaries, `readelf -h` or `objdump -f` can show architecture.

---

### Stage 3 — Hashing & Reputation

**Tasks**

* Calculate MD5 and SHA256 hashes for each suspicious file.
* Record the hashes for tracking and future IOC matching.

**Hints**

* Use `sha256sum` and `md5sum`. Explain why both fast (MD5) and strong (SHA256) hashes are useful.

---

### Stage 4 — Static Analysis

**Tasks**

* Inspect scripts and text files for suspicious commands, encoded strings, or URLs.
* Extract readable strings from binaries and look for human-readable indicators.

**Hints**

* For scripts, `less` and `grep` work well. For binaries, use `strings`, `hexdump -C`, and `xxd`.
* Look for `base64`, `wget`, `curl`, `nc`, `@reboot`, or hardcoded IP-like patterns.

---

### Stage 5 — Persistence & System Artifacts

**Tasks**

* Determine whether files attempt to create persistence (cron, systemd, startup scripts).
* Search common persistence locations for related entries.

**Hints**

* Check `/etc/cron.*`, `crontab -l` (per-user crontabs), `/etc/systemd/system`, and `~/.bashrc` for references.
* In this lab some persistence artifacts may be in `/tmp` (simulated stubs) — note the difference between a stub and an installed job.

---

### Stage 6 — Network & Exfil Indicators

**Tasks**

* Identify any IPs, domain names, or port hints that indicate external communication.
* Distinguish between realistic and example/test IP ranges.

**Hints**

* RFC 5737 ranges (192.0.2.0/24, 198.51.100.0/24, 203.0.113.0/24) are safe for examples. Flag any non-testnet addresses for further review.
* Use pattern matching (regex) to find IPv4/IPv6 strings in files.

---

### Stage 7 — Safe Execution in an Isolated Sandbox (Optional)

**Tasks**

* If you choose to execute samples, run them in a disposable VM or container and observe file/system changes.
* Capture outputs and newly created files as evidence.

**Hints**

* Use a snapshot or non-production VM. Inspect `/tmp` and process lists after execution. Avoid executing samples on production systems.

---

### Stage 8 — Reporting & Remediation

**Tasks**

* Create a concise report summarizing indicators, evidence, and recommended remediation steps.
* Produce a short IOC list (hashes, filenames, IPs) to use in further hunts.

**Hints**

* Prioritize containment and evidence preservation. Suggested remediation steps: isolate host, collect logs, remove/quarantine artifacts, rotate credentials, and perform a broader hunt.

---

## 🧾 Instructor Notes

* The lab uses **safe, benign artifacts** designed to mimic suspicious behavior. Tinker with the sample files to increase difficulty (e.g., add additional encoded strings or a fake systemd unit file) — but never include live malicious code.

---
