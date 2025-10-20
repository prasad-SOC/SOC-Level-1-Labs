# Linux File Analysis Lab — Instructor Answers

> These are model answers for the lab artifacts created by the provided lab setup. Answers assume the sample files were created exactly as in the lab README. Binary compilation may change the binary hash depending on the environment; hashes below are for the source/text files as provided.

---

## Stage 1 — Environment Recon & File Inventory

**Observed files (example)**

* suspicious.sh — shell script (size ~407 bytes)
* dropper (compiled from dropper.c) — ELF binary (size varies by build)
* dropper.c — C source file
* masquerade.txt — plain text file
* README_TRIGGER — plain text file

**Notes:** files are located in `~/labs/file_analysis` and some artifacts (simulated markers) are created in `/tmp` when executed.

---

## Stage 2 — File Identification

**File types (one-liners)**

* suspicious.sh: POSIX/Bash shell script (ASCII text, executable)
* dropper: ELF executable (architecture depends on build environment)
* dropper.c: C source code (ASCII text)
* masquerade.txt: plain text (contains an IP-looking string)
* README_TRIGGER: plain text

---

## Stage 3 — Hashing & Reputation

**Hashes for the lab-provided textual files (calculated on the exact content in the lab):**

* suspicious.sh

  * SHA256: `a570fee4e7ceb74459734824539abdcf6ebc3cda20cade17724854a2c7f48955`
  * MD5:   `06a9b25d02bf85d3d150493b17e96006`

* dropper.c

  * SHA256: `c4d2fc932df9033e6c9dfe80adabb91e43ea65cca55f0795cbf68b80068bf69f`
  * MD5:    `7cd46ff7aac62e307819ecce058db934`

* masquerade.txt

  * SHA256: `64c208dd7daabbaa5bd86402237dc582c5593ab931e5c45fc8d7a773f0d1026a`
  * MD5:    `c45a6ee49df790007887735568f2b026`

* README_TRIGGER

  * SHA256: `656b5bb15edc18497864d4d8f9c620151396e91154b856f5f811f1f3a88747f1`
  * MD5:    `5c56ca02152637628293b189fa69bcdd`

**Important notes:**

* The compiled `dropper` binary's hash will vary by compiler, flags, and environment. If you compile using `gcc -o dropper dropper.c` on your lab VM, compute the binary's hashes separately and record them.
* Hashes are used to uniquely identify artifacts, to search threat intelligence feeds, and to track the same file across multiple hosts.

---

## Stage 4 — Static Analysis (observations)

**suspicious.sh — notable findings**

* Contains a `base64`-encoded string assigned to variable `b64` and then decoded: indicates attempted obfuscation of a payload or command.
* Writes a marker to `/tmp/payload_marker.txt` to signal execution.
* Writes a cron-like stub into `/tmp/sim_cron` containing `# @reboot /tmp/suspicious.sh` (simulated persistence artifact).

**dropper.c / dropper — notable findings**

* Source writes `/tmp/hello_marker.txt` containing `dropper ran` and prints `dropper: harmless sample` to stdout.
* Binaries examined with `strings dropper` should reveal `dropper ran` and `dropper: harmless sample` among other strings.

**masquerade.txt — notable findings**

* Contains `dest: 192.0.2.123:8080` — looks like an exfil destination but 192.0.2.0/24 is RFC 5737 TEST-NET-1 (safe example range). Still treat as an IOC in exercises and note it is an example IP.

---

## Stage 5 — Persistence & System Artifacts

**Findings**

* The lab sample simulates persistence by writing a cron-like line to `/tmp/sim_cron`, but this is only a stub (not actually installed into system cron). Real persistence checks should include:

  * `crontab -l` for user crontabs and files in `/etc/cron.*`
  * systemd unit files under `/etc/systemd/system` and `~/.config/systemd/user`
  * shell startup files (`~/.bashrc`, `~/.profile`) and `/etc/rc.local` where present

**Interpretation**

* Presence of a cron-like string in `/tmp` is an indicator to investigate further; determine whether the job is actually installed or just a leftover artifact.

---

## Stage 6 — Network & Exfil Indicators

**Findings**

* `masquerade.txt` contains an IP-like destination: `192.0.2.123:8080` — **example/test IP range**.
* No actual `wget`, `curl`, `nc`, or C2 domains exist in the benign samples. The `base64` decoding could hide commands that contact external hosts in a real malicious sample.

**Actionable notes**

* Flag non-testnet addresses for further analysis. Use passive DNS and TI sources to validate domains/IPs when present.

---

## Stage 7 — Behavioral Observation (safe execution)

**Observed behavior when run in the provided sandbox**

* Running `./suspicious.sh` will create:

  * `/tmp/payload_marker.txt` containing: `[+] simulated payload running`
  * `/tmp/dropper_output.txt` containing: `echo 'Sample dropper executed'` (this is the decoded base64 string from the sample)
  * `/tmp/sim_cron` containing the cron-like stub `# @reboot /tmp/suspicious.sh`

* Compiling and running `./dropper` will create:

  * `/tmp/hello_marker.txt` containing `dropper ran`
  * stdout prints `dropper: harmless sample`

**Evidence collected**

* These marker files serve as evidence of execution for the lab and demonstrate how a real dropper might leave artifacts.

---

## Stage 8 — Reporting & Remediation (model answers)

**Which file appears suspicious and why?**

* `suspicious.sh` is the primary suspicious artifact: it contains obfuscated data (base64), writes persistence-like stubs, and writes output markers indicating execution. `dropper` is also suspicious in a real case because it is an executable that writes a marker file, but in this lab it is intentionally benign.

**Actions performed by the files**

* `suspicious.sh`: writes markers to `/tmp`, decodes a base64 payload into `/tmp/dropper_output.txt`, and writes a cron-like stub to `/tmp/sim_cron`.
* `dropper`: writes `/tmp/hello_marker.txt` and prints a message to stdout.

**Recommended remediation steps (short list)**

1. Isolate the affected host from the network to prevent further potential exfiltration or C2 communication.
2. Collect forensic evidence: copies of the suspicious files, relevant logs (`/var/log/auth.log`, `/var/log/syslog`), and memory image if possible.
3. Remove or quarantine the malicious artifacts (after collecting them) and disable any confirmed persistence mechanisms.
4. Rotate any potentially compromised credentials and audit user accounts for suspicious access.
5. Perform a broader hunt across the environment using IoCs (file hashes, filenames, IPs) and consider reimaging if persistence or unknown binaries are found.

---
