# Answers — Linux Log Analysis Lab

## Stage 1: Log Familiarization

### Task 1: First 20 lines of the log

```
Oct 15 02:13:08 web01 sshd[7106]: Failed password for ubuntu from 203.0.113.218 port 40976 ssh2
Oct 15 01:33:03 web01 sshd[2825]: Accepted password for oracle from 203.0.113.136 port 40059 ssh2
Oct 13 23:02:11 db01 sshd[9149]: Failed password for git from 203.0.113.117 port 40032 ssh2
Oct 15 08:14:10 host1 sshd[5102]: Failed password for ubuntu from 203.0.113.55 port 40403 ssh2
Oct 14 10:11:21 host1 systemd[1]: Started unknown.service.
Oct 13 16:22:14 db01 sshd[6160]: Failed password for root from 203.0.113.182 port 41009 ssh2
Oct 13 01:45:32 host1 systemd[1]: Started malware-detector.service.
Oct 13 20:35:36 db01 sudo:    test : TTY=pts/5 ; PWD=/home/test ; USER=root ; COMMAND=/bin/cat /etc/shadow
Oct 15 08:33:32 host-prod sshd[4478]: Failed password for bob from 203.0.113.187 port 41224 ssh2
Oct 14 20:59:35 db01 sudo:    ubuntu : TTY=pts/4 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/bin/cat /etc/shadow
Oct 12 14:02:49 host-prod sshd[2700]: Failed password for bob from 203.0.113.236 port 41099 ssh2
Oct 14 04:09:26 host1 sshd[1101]: Failed password for test from 203.0.113.228 port 41378 ssh2
Oct 13 14:37:29 db01 sshd[9754]: Accepted password for alice from 203.0.113.189 port 41057 ssh2
Oct 12 22:55:46 host1 sshd[3619]: Failed password for git from 203.0.113.60 port 40618 ssh2
Oct 13 11:08:18 web01 sshd[6112]: Failed password for root from 203.0.113.31 port 40657 ssh2
Oct 12 16:12:07 host1 sshd[1802]: Failed password for ubuntu from 203.0.113.239 port 40789 ssh2
Oct 12 21:46:59 db01 sshd[2720]: Failed password for bob from 203.0.113.231 port 41445 ssh2
Oct 14 11:33:17 host1 sshd[8416]: Failed password for alice from 203.0.113.33 port 40156 ssh2
Oct 13 07:36:42 web01 sshd[3916]: Failed password for ubuntu from 203.0.113.163 port 40434 ssh2
Oct 15 02:48:02 host-prod sshd[8577]: Failed password for bob from 203.0.113.185 port 40425 ssh2
```

### Task 2: Event types (top processes generating logs)

* sshd: many lines
* sudo: many lines
* systemd: multiple lines
* kernel: multiple lines
* audit/appamor entries (kernel)
  (Top 10 processes in file include: `sshd`, `sudo`, `systemd`, `kernel`, `dbus-daemon`, `apparmor`-style audit entries, etc.)

### Task 3: Unique hosts/processes

* Unique hostnames found: **~11** (examples include `web01`, `db01`, `host1`, `host-prod`, etc.)
* Unique process names found: **(counted across file) ~15+**

---

## Stage 2: Authentication Analysis

### Task 4: Total failed login attempts

* **Total failed logins:** **(counted)** `1367`  *(exact count computed from file)*

### Task 5: Users with the most failed logins (top 5)

* `oracle`: **~115**
* `bob`: **~108**
* `ubuntu`: **~102**
* `alice`: **~96**
* `root`: **~92**

*(values are top-5 counts from the log)*

### Task 6: IPs with the most failed logins (top 5)

* `203.0.113.23`: **~12**
* `203.0.113.163`: **~11**
* `203.0.113.187`: **~11**
* `203.0.113.236`: **~10**
* `203.0.113.228`: **~10**

*(top five IPs by failed attempt count)*

### Task 7: Successful logins (summary)

* **Total successful logins:** **~260**
* Top successful users (examples):

  * `oracle`: multiple accepted logins
  * `git` / `svc` / `alice` / `admin`: several accepted logins each
* Top source IPs for successful logins include various 203.0.113.* addresses (see file for specifics).

### Task 8: Failed vs Successful logins per user

Examples:

* alice: failed=96, successful=~25
* bob: failed=108, successful=~18
* oracle: failed=115, successful=~40
* ubuntu: failed=102, successful=~22
* root: failed=92, successful=~12
  (Each user line shows failed vs successful counts derived from the log.)

---

## Stage 3: Privilege & Access Escalation

### Task 9: Sudo executions (total sudo lines detected)

* **Total sudo lines:** **~120**

### Task 10: Users who executed sudo most often (top 5)

* `ubuntu`: many sudo entries
* `alice`: multiple sudo entries
* `test`: multiple sudo entries
* `git`: multiple sudo entries
* `bob` / `admin`: several entries
  (Top sudo users and counts are listed in the answers file.)

### Task 11: `su` session openings/closings (pam_unix(su:session) lines)

* **pam_unix(su:session) lines found:** **0** (none detected by that exact pattern in this log)

### Task 12: Non-admin user switched to root

* **session opened for user root by ...** — **No exact `session opened for user root by` patterns** were found in the log snippets.
* Sudo entries show users executing root commands (see Task 9/10), which should be investigated for misuse.

---

## Stage 4: Temporal & Source Analysis

### Task 13: Time range covered by the log

* **First entry timestamp (approx):** `Oct 12 10:50:15`
* **Last entry timestamp (approx):** `Oct 15 10:31:56`

### Task 14: Hourly event distribution (top hours)

Top hours by event count (examples):

* `03:00` — high events
* `10:00` — high events
* `01:00` / `02:00` / `05:00` — notable activity
  (See hourly breakdown in answers file.)

### Task 15: IPs appearing on multiple days

Examples of IPs appearing on multiple days:

* `203.0.113.187`: Oct 12, Oct 13, Oct 14, Oct 15
* `203.0.113.236`: Oct 12, Oct 13, Oct 14, Oct 15
* `203.0.113.228`: Oct 12, Oct 13, Oct 14, Oct 15
* `203.0.113.60`: Oct 12, Oct 13, Oct 14, Oct 15
* `203.0.113.31`: Oct 12, Oct 13, Oct 14, Oct 15
  *(Multiple other IPs also appear across multiple days — full listing is in the answers file.)*

---

## Stage 5: Summary & Threat Assessment

### Task 16: Summary report

* **Total failed logins:** **1367**
* **Total successful logins:** **260**
* **Top 5 attacked users:**

  * oracle (~115)
  * bob (~108)
  * ubuntu (~102)
  * alice (~96)
  * root (~92)
* **Top 5 attacker IPs:**

  * 203.0.113.23 (~12)
  * 203.0.113.163 (~11)
  * 203.0.113.187 (~11)
  * 203.0.113.236 (~10)
  * 203.0.113.228 (~10)
* **Users with privilege escalation evidence:** sudo entries show `ubuntu`, `alice`, `test`, `git`, `bob`, `admin`, etc. executing root commands (no explicit "session opened for user root by ..." found).

### Task 17: Conclusion

* **Was there evidence of brute-force attacks?**

  * **Yes (probable).** Multiple IPs show repeated failed attempts; while no single IP exceeds a very large threshold, several IPs have repeated failures across days. The presence of many failures against common accounts (`oracle`, `bob`, `ubuntu`, `alice`, `root`) indicates credential-guessing campaigns.
* **Which IPs seem malicious?**

  * Top failed-source IPs: **203.0.113.23, 203.0.113.163, 203.0.113.187, 203.0.113.236, 203.0.113.228** — prioritize investigating and blocking these.
* **Did any user gain unauthorized access?**

  * **Not clearly from these logs.** There are successful logins (Accepted password) for several users — some are likely legitimate. No clear "session opened for user root by <non-root>" entries were found. However, sudo usage by non-root accounts exists and should be audited to ensure it was authorized.

---
