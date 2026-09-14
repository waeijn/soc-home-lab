# Phase 2 — Attack Simulation & Detection

## Overview
Simulated real-world attacks from Kali Linux against the Windows 10 target,
then built SPL detection rules and Splunk alerts for each attack scenario.

## Environment
| Component | Details |
|---|---|
| Attacker | Kali Linux, IP 192.168.56.102 |
| Target | Windows 10 Pro, IP 192.168.56.101 |
| SIEM | Splunk Enterprise, IP 192.168.56.10 |

## Attacks Simulated

### 1. Network Reconnaissance (Nmap)
- **Tool:** Nmap 7.98
- **Command:** `nmap -sV -sC 192.168.56.101`
- **Finding:** Port 3389 (RDP) open, hostname DESKTOP-TAGLU79
- **Events Generated:** EventCode 5156 (Filtering Platform Connection)
- **MITRE ATT&CK:** T1046 — Network Service Scanning

### 2. RDP Brute Force (Hydra)
- **Tool:** Hydra v9.6
- **Command:** `hydra -l analyst -P wordlist.txt -t 1 rdp://192.168.56.101`
- **Result:** Successfully cracked `analyst:Analyst@2026!`
- **Events Generated:** EventCode 4625 (Failed Logon), EventCode 4624 (Successful Logon)
- **MITRE ATT&CK:** T1110 — Brute Force

### 3. PowerShell Execution
- **Techniques:** Download cradle (IEX), Base64 encoded commands, reconnaissance
- **Events Generated:** EventCode 4104 (Script Block Logging), EventCode 4688 (Process Creation)
- **MITRE ATT&CK:** T1059.001 — PowerShell

## Visibility Gaps & Fixes
| Gap | Fix |
|---|---|
| 5156 events not generating | Enabled Filtering Platform Connection auditing via auditpol |
| 4625 events not generating | Enabled Logon auditing via auditpol |
| 4104 events not generating | Enabled PowerShell Script Block Logging via registry |
| 4688 events missing cmdline | Enabled Process Creation command-line auditing via registry |
| Forwarder not reading inputs | Added NT SERVICE\SplunkForwarder to Event Log Readers group |

## Detection Rules
| File | Detects | Event IDs |
|---|---|---|
| detect-network-recon.spl | Port scan from single IP | 5156 |
| detect-brute-force.spl | Multiple failed logons | 4625 |
| detect-powershell-scriptblock.spl | Malicious PowerShell keywords | 4104 |
| detect-process-creation.spl | Suspicious process execution | 4688 |
| extract-powershell-payload.spl | De-obfuscated script extraction | 4104 |

## Alerts Created
| Alert | Severity | Type | Trigger |
|---|---|---|---|
| Brute Force - Multiple Failed Logons | High | Real-time | Per Result |
| Network Recon - Port Scan Detected | Medium | Real-time | Per Result |
| Suspicious PowerShell - Malicious Keywords | High | Real-time | Per Result |

## Key Troubleshooting Notes
- Hydra RDP module is experimental — NLA must be disabled for it to work
- Windows Account Lockout Policy triggers during brute force — unlock with `net user analyst /active:yes`
- 5156 events only generate for ports with active listeners — not just open firewall rules
- Filtering Platform auditing resets after VM rebuild — must be re-enabled manually

## Screenshots
See screenshots/phase2/
