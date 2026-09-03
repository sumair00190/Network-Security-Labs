# 🌐 Network Security & Enumeration: Nmap Guide

This repository serves as a comprehensive lab guide, cheat sheet, and enumeration workflow for **Nmap (Network Mapper)**, compiled during hands-on network security labs and TryHackMe modules.

---

## 📖 Overview
Nmap is an essential open-source security tool used for network discovery, host detection, port scanning, and vulnerability auditing. Understanding Nmap scans is crucial for both offensive reconnaissance and defensive network monitoring.

---

## 📌 Core Scan Types & Commands

| Scan Type | Command | Purpose & Operational Use Case |
| :--- | :--- | :--- |
| **SYN Stealth Scan** | `nmap -sS <target>` | Default/Fast scan. Sends SYN packet; tears down without completing 3-way handshake. Reduces log footprints. |
| **TCP Connect Scan** | `nmap -sT <target>` | Used when raw socket permissions (`sudo`) are unavailable. Completes full 3-way handshake. |
| **UDP Scan** | `nmap -sU <target>` | Audits open UDP services (e.g., DNS, DHCP, SNMP). Slower due to ICMP port unreachable responses. |
| **Service Detection** | `nmap -sV <target>` | Probes open ports to extract service banners and exact protocol versions. |
| **OS Detection** | `nmap -O <target>` | Uses TCP/IP stack fingerprinting to determine target Operating System. |
| **Aggressive Scan** | `nmap -A <target>` | Enables OS detection, service versioning, NSE scripts, and traceroute in a single flag. |
| **Full Port Scan** | `nmap -p- <target>` | Scans all 65,535 TCP ports instead of default top 1,000 ports. |

---

## 📜 Nmap Script Engine (NSE)
The Nmap Script Engine allows users to automate network tasks, vulnerability detection, and advanced enumeration.

* **Default Script Scan:** Runs standard safe enumeration scripts against target services.
  * Command: `nmap -sC <target_ip>`
* **Specific Vulnerability Checking:** Scans target services against known vulnerability databases (CVEs).
  * Command: `nmap --script vuln <target_ip>`
* **Targeted HTTP Enumeration:** Extracts web server directories, methods, and configurations on port 80.
  * Command: `nmap --script http-enum -p 80 <target_ip>`

---

## 🛠️ Practical Scanning Workflow

1. **Host Discovery & Fast Reconnaissance:** Identifies live targets and quickly maps top active ports.
   * `nmap -sn <subnet/IP>` (Host discovery without port scan)
   * `nmap -sS -F <target_ip>` (Fast scan of top 100 ports)

2. **Comprehensive Enumeration & Output Logging:** Performs deep scan on active services and saves results to file.
   * `nmap -sV -sC -O -oN nmap_report.txt <target_ip>`

3. **In-Depth All-Port Audit (No Ping):** Probes every single TCP port without dropping hosts that block ICMP pings.
   * `nmap -p- -Pn -sV --min-rate 5000 <target_ip>`

---

## 🔒 Key Defensive Takeaways

* **Firewall Hardening:** Filter unauthorized ICMP requests and strictly restrict public access to administrative ports (e.g., SSH 22, RDP 3389) using `ufw` / `iptables`.
* **Banner Suppression:** Disable or randomize service version banners across web servers and SSH to obscure infrastructure details from unauthorized reconnaissance.
* **Network Intrusion Detection (IDS/IPS):** Monitor for high-frequency TCP SYN bursts and anomalous UDP sweeps to catch reconnaissance activities early.
