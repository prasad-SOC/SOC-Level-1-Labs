# Linux Host Hardening Lab — Instructor Answers

> These are model answers and observations based on a standard Ubuntu/Debian lab environment. Actual results may vary depending on VM setup and pre-installed packages.

---

## Stage 1 — Baseline Discovery & Inventory

**Observed Findings:**

* Open ports: 22 (SSH), 80/443 if web server installed, others depend on VM.
* Running services: sshd, systemd-journald, possibly cron, rsyslog.
* Users: default user(s), root, test users.
* SSH config: `PermitRootLogin yes` (vulnerable), `PasswordAuthentication yes` (vulnerable).

**Notes:** Identify all services and defaults that could be hardened.

---

## Stage 2 — Authentication Hardening

**Applied Changes:**

* Disabled SSH password authentication (`PasswordAuthentication no`) and root login (`PermitRootLogin no`).
* Enforced password complexity (`/etc/pam.d/common-password`) and expiration (`chage`).
* Locked unused accounts (`usermod -L`) and cleaned sudoers.
* Configured test user with key-based login and verified access.

---

## Stage 3 — Service & Package Hardening

**Changes Implemented:**

* Disabled/removal of unneeded services (e.g., cups, avahi-daemon).
* Applied system updates: `apt update && apt upgrade`.
* Ensured running services use least privilege.
* Removed unnecessary packages to reduce attack surface.

---

## Stage 4 — Filesystem & Permissions Hardening

**Findings and Actions:**

* Located world-writable files outside `/tmp` or expected directories and fixed permissions (`chmod 644` or `chmod 600`).
* Verified `/etc/ssh/*`, `/etc/sudoers*`, `/etc/passwd` ownership and permissions corrected.
* Reviewed `/etc/fstab` for mount options, applied restrictive mounts where safe (optional in lab).

---

## Stage 5 — Logging, Monitoring & Auditing

**Configured:**

* Confirmed `rsyslog` and `journalctl` functioning.
* Enabled `auditd` for critical files and commands.
* Configured log rotation (`/etc/logrotate.d/`) to prevent log overflow.
* Created simple alert scripts (e.g., detect new sudoers entries).

---

## Stage 6 — Network Hardening & Firewall

**Implemented Rules:**

* Default deny incoming, allow SSH: `ufw default deny incoming`, `ufw allow ssh`.
* Rate-limiting applied using `ufw limit ssh` or `fail2ban`.
* Verified rules persist across reboots (`ufw status` and systemctl check).

---

## Stage 7 — Secure Bootstrapping & Patch Management

**Actions:**

* Patch plan: update weekly, snapshot before major updates.
* Enabled unattended upgrades for security patches (`/etc/apt/apt.conf.d/50unattended-upgrades`).
* Created baseline snapshot or script to restore hardening settings.

---

## Stage 8 — Least Privilege & Application Sandboxing

**Implemented:**

* Services run under dedicated, unprivileged users (checked `systemd` unit `User=`).
* Applied AppArmor profiles for key services; SELinux optional depending on distro.
* Containerized or chrooted risky applications in lab scenarios (optional).

---

## Stage 9 — Validation & Hardening Verification

**Verification Results:**

* Re-scanned ports and services: only required ports/services open.
* Verified file permissions and that unauthorized SSH/password logins fail.
* Captured terminal logs/screenshots demonstrating applied changes.
* Evidence shows baseline hardening successfully applied.

---

## Summary & Recommendations

* Initial risks: weak SSH config, world-writable files, unneeded services, missing updates.
* Changes applied: SSH hardening, package updates, service minimization, permissions fixed, firewall rules applied, auditing enabled.
* Next steps: periodic verification, maintain patching, update AppArmor profiles, monitor logs for anomalies.

---

## Instructor Notes / Caveats

* Actual services, users, and open ports may differ by lab VM.
* Ensure rollback steps for SSH/firewall in case students lock themselves out.

*End of Linux Hardening Lab Answers*
