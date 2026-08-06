# Nmap Cheat Sheet

## Content

- Nmap Introduction
- Scan Types
- Host Discovery
- Port Scanning
- Service Enumeration
- Version Detection
- OS Detection
- NSE Scripts
- Firewall Evasion
- Output Formats
- Common Pentest Scans
- HTB Workflow
- References

---

# 1. Nmap Introduction

Nmap is a network scanner used for:

- Host discovery
- Port scanning
- Service enumeration
- Vulnerability detection
- Network mapping

Basic syntax:

```bash
nmap [options] <target>
```

Example:

```bash
nmap 10.10.10.10
```

---

# 2. Host Discovery

## Ping Scan

Discover active hosts:

```bash
nmap -sn 10.10.10.0/24
```

Disable ping:

```bash
nmap -Pn <IP>
```

Useful when:

- ICMP is blocked
- Firewall filtering exists

---

# 3. Port Scanning

## Default Scan

Scans common ports:

```bash
nmap <IP>
```

---

## All Ports

Scan all TCP ports:

```bash
nmap -p- <IP>
```

Recommended for HTB:

```bash
nmap -p- --min-rate 5000 <IP>
```

---

## Specific Ports

```bash
nmap -p 22,80,443 <IP>
```

Range:

```bash
nmap -p 1-1000 <IP>
```

---

# 4. Scan Types

## TCP SYN Scan

Default stealth scan:

```bash
nmap -sS <IP>
```

---

## TCP Connect Scan

Full connection:

```bash
nmap -sT <IP>
```

---

## UDP Scan

Scan UDP ports:

```bash
nmap -sU <IP>
```

Example:

```bash
nmap -sU -p 53,161 <IP>
```

---

# 5. Service Enumeration

Detect services:

```bash
nmap -sV <IP>
```

Aggressive detection:

```bash
nmap -A <IP>
```

Includes:

- Version detection
- OS detection
- Scripts
- Traceroute

---

# 6. OS Detection

Detect operating system:

```bash
nmap -O <IP>
```

Aggressive:

```bash
nmap -A <IP>
```

Information:

- OS version
- Device type
- Network distance

---

# 7. NSE Scripts

Nmap Scripting Engine allows automation.


List scripts:

```bash
ls /usr/share/nmap/scripts/
```

---

## Default Scripts

```bash
nmap -sC <IP>
```

---

## Vulnerability Scan

```bash
nmap --script vuln <IP>
```

---

## HTTP Enumeration

```bash
nmap --script http-enum <IP>
```

---

## SMB Enumeration

```bash
nmap --script smb-enum-shares <IP>
```

---

## FTP Anonymous Login

```bash
nmap --script ftp-anon <IP>
```

---

## DNS Enumeration

```bash
nmap --script dns-brute <IP>
```

---

# 8. Firewall Evasion

## Fragment Packets

```bash
nmap -f <IP>
```

---

## Decoy Scan

```bash
nmap -D RND:5 <IP>
```

---

## Spoof Source Port

```bash
nmap --source-port 53 <IP>
```

---

## Slow Scan

```bash
nmap -T1 <IP>
```

---

# 9. Timing Templates

Control scan speed:

```
-T0
-T1
-T2
-T3
-T4
-T5
```

Common:

Fast:

```bash
-T4
```

HTB:

```bash
-T4 --min-rate 5000
```

Stealth:

```bash
-T1
```

---

# 10. Output Formats

## Normal Output

```bash
-oN scan.txt
```

---

## XML Output

```bash
-oX scan.xml
```

---

## Grepable Output

```bash
-oG scan.gnmap
```

---

## All Formats

```bash
-oA scan_name
```

Creates:

```
scan_name.nmap
scan_name.xml
scan_name.gnmap
```

---

# 11. Common Pentest Scans


## Initial Recon

```bash
nmap -sC -sV <IP>
```

---

## Full TCP Scan

```bash
nmap -p- -T4 <IP>
```

---

## Full Enumeration

```bash
nmap \
-p- \
-sC \
-sV \
-O \
-A \
<IP>
```

---

## Vulnerability Scan

```bash
nmap \
--script vuln \
<IP>
```

---

# 12. Service Specific Enumeration


## HTTP

```bash
nmap -p80,443 --script http-* <IP>
```

Look for:

- Web server
- Directories
- Vulnerabilities


---

## SMB

```bash
nmap -p445 --script smb-* <IP>
```

Look for:

- Shares
- SMB versions
- Vulnerabilities


---

## FTP

```bash
nmap -p21 --script ftp-* <IP>
```

Look for:

- Anonymous login
- Version information


---

## SSH

```bash
nmap -p22 --script ssh-* <IP>
```

Look for:

- Algorithms
- Versions

---

# 13. HTB Nmap Workflow

```
1. Host Discovery
        |
2. Full Port Scan
        |
3. Service Detection
        |
4. NSE Enumeration
        |
5. Manual Enumeration
        |
6. Exploitation
```

Example:

```bash
nmap -p- <IP>

nmap -sC -sV -p PORTS <IP>

nmap --script vuln -p PORTS <IP>
```

---

# 14. Useful Commands


Scan multiple targets:

```bash
nmap 10.10.10.1 10.10.10.2
```

From file:

```bash
nmap -iL targets.txt
```

Save results:

```bash
nmap -oA scan <IP>
```

Verbose:

```bash
nmap -v <IP>
```

Debug:

```bash
nmap -d <IP>
```

---

# References

- https://nmap.org/
- https://nmap.org/nsedoc/
- https://book.hacktricks.xyz/
