# Nmap Reconnaissance

## Overview
A network port scan performed to discover open ports, running services, and potential vulnerabilities on the target machine. This simulates an adversary mapping out the network environment during the initial discovery phase of an attack.

## MITRE ATT&CK
- **Tactic:** Discovery
- **Technique ID:** T1046
- **Technique Name:** Network Service Discovery

## Tools Used
- Nmap

## Target
- Machine: win-target (192.168.56.101)
- Service/Port attacked: All network ports

## Attack Execution
### Command Used
```bash
nmap -sV -sC -p- 192.168.56.101
```

### What It Does
This command scans all 65,535 TCP ports (`-p-`) on the target machine, attempts to determine the exact version of the running services (`-sV`), and executes default reconnaissance scripts (`-sC`) to gather additional context.

## Telemetry Generated
| Event ID | Source | Description |
|---|---|---|
| 5156 | WinEventLog:Security | The Windows Filtering Platform has allowed a connection. A high volume of these connections across multiple ports from a single IP indicates a scan. |

## Detection SPL

```spl
index=main sourcetype=WinEventLog:Security EventCode=5156 
| stats count by Source_Address, Destination_Port 
| where count > 5 
| sort -count
```

## Evidence
See screenshots/phase2/

## Notes
Detection relies heavily on Event ID 5156. Ensure that Windows Filtering Platform (WFP) connection auditing is enabled via Group Policy on the Windows target; otherwise, the events will not be generated in the Security log.
