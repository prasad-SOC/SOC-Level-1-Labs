# 🧠 Linux Log Analysis Lab

## 🎯 Objective
In this lab, you’ll act as a **SOC Level 1 Analyst** investigating suspicious activity on a Linux server.  
You’ll use real log analysis techniques to identify failed login attempts, brute-force patterns, and privilege escalation indicators.

---

## 🧰 Requirements
- Basic Linux command-line skills  
- Familiarity with `grep`, `awk`, `sort`, and `uniq`  
- The sample log file: **fake_auth_30000.log**

---

## ⚙️ Setup
1. Download the log file (if not already present).
2. Move it into your working directory:
   - `mv fake_auth_30000.log ~/logs/`
   - `cd ~/logs/`
3. Verify that the file exists:
   - `ls -lh fake_auth_30000.log`
4. You’re ready to start the investigation.

---

## 🚀 Lab Overview
This lab is divided into **five stages**, each covering a key SOC log analysis skill:
1. Log Familiarization  
2. Authentication Analysis  
3. Privilege Escalation Detection  
4. Temporal & Source Analysis  
5. Summary & Threat Assessment  

---

## 🧩 Stage 1: Log Familiarization
Get familiar with the log structure before analyzing attacks.

### 🧭 Task 1: View the first 20 lines of the log
💡 *Hint:* Use a command that shows the top part of a file.

---

### 🧭 Task 2: Identify different event types
Which services are generating logs? (e.g., `sshd`, `sudo`, `su`, `CRON`)  
💡 *Hint:* Search for these keywords in the log.

---

### 🧭 Task 3: Count how many unique hosts or processes generated logs
💡 *Hint:* Extract the hostname or process column and count unique occurrences.

---

## 🔐 Stage 2: Authentication Analysis
Focus on login attempts and authentication anomalies.

### 🔎 Task 4: Count all failed login attempts
💡 *Hint:* Look for `"Failed password"` in the log.

---

### 🔎 Task 5: Find users with the most failed logins
💡 *Hint:* Extract usernames from failed password lines and count them.

---

### 🔎 Task 6: Identify IPs with the most failed logins
💡 *Hint:* Extract IPs after the word `from`.

---

### 🔎 Task 7: Check for successful logins
💡 *Hint:* Search for `"Accepted password"` and list users and IPs.

---

### 🔎 Task 8: Compare failed vs successful logins per user
💡 *Hint:* Combine multiple searches and compare counts.

---

## 🧰 Stage 3: Privilege & Access Escalation
Detect potential privilege misuse.

### ⚡ Task 9: Detect all `sudo` executions
💡 *Hint:* Look for lines containing `sudo:` in the log.

---

### ⚡ Task 10: Identify which users executed `sudo` most often
💡 *Hint:* Extract usernames and count frequencies.

---

### ⚡ Task 11: Check for `su:` session openings and closings
💡 *Hint:* Search for `pam_unix(su:session)` lines.

---

### ⚡ Task 12: Detect if any non-admin user switched to root
💡 *Hint:* Look for “session opened for user root by ...” patterns.

---

## ⏰ Stage 4: Temporal & Source Analysis
Find when and where attacks happened.

### 🕒 Task 13: Identify the time range covered by the log
💡 *Hint:* Extract the first and last timestamp entries.

---

### 🕒 Task 14: Detect hourly login spikes
💡 *Hint:* Extract the hour field from timestamps and count occurrences.

---

### 🕒 Task 15: Identify IPs appearing on multiple days
💡 *Hint:* Combine date fields and IPs, then count them.

---

## 📊 Stage 5: Summary & Threat Assessment
Wrap up with a report like a SOC analyst.

### 📋 Task 16: Generate a summary report
Your report should include:
- Total failed logins  
- Total successful logins  
- Top 5 attacked users  
- Top 5 attacker IPs  
- Any users with privilege escalation  

💡 *Hint:* Combine the previous commands logically.

---

### 🧠 Task 17: Write your conclusion
Answer these:
- Was there evidence of brute-force attacks?  
- Which IPs seem malicious?  
- Did any user gain unauthorized access?  

---

## ✅ Learning Outcomes
By completing this lab, you’ll be able to:
- Understand and analyze Linux authentication logs  
- Detect brute-force and unauthorized access attempts  
- Identify privilege escalations  
- Perform correlation and timeline analysis  
- Create a structured SOC investigation report  

---

## 📂 Next Step
Compare your answers with the provided **answers.md** file to verify your analysis.
