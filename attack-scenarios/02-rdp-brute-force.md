# RDP Brute Force

## Overview
An attempt to guess the password of a valid user account by rapidly and repeatedly authenticating against the Remote Desktop Protocol (RDP) service using a dictionary of common passwords. This simulates an adversary attempting to gain initial access or escalate privileges via Credential Access.

## MITRE ATT&CK
- **Tactic:** Credential Access
- **Technique ID:** T1110.001
- **Technique Name:** Brute Force: Password Guessing

## Tools Used
- Hydra

## Target
- Machine: win-target (192.168.56.101)
- Service/Port attacked: RDP (TCP 3389)

## Attack Execution
### Command Used
```bash
hydra -l analyst -P /usr/share/wordlists/rockyou.txt rdp://192.168.56.101
```

### What It Does
Uses the Hydra network logon cracker alongside the standard `rockyou.txt` wordlist to automate login attempts against the Windows target machine over RDP, targeting the local user `analyst`.

## Telemetry Generated
| Event ID | Source | Description |
|---|---|---|
| 4625 | WinEventLog:Security | An account failed to log on. A massive spike in these events indicates brute force activity. |
| 4624 | WinEventLog:Security | An account was successfully logged on. Important to track to see if the brute force was successful. |

## Detection SPL

```spl
index=main sourcetype=WinEventLog:Security (EventCode=4625 OR EventCode=4624)
| eval event_type=if(EventCode=4625, "FAILURE", "SUCCESS")
| bucket _time span=60s
| stats count(eval(event_type="FAILURE")) as failures, 
        count(eval(event_type="SUCCESS")) as successes 
        by _time, Source_Network_Address, Account_Name
| where failures > 5
```

## Evidence
See screenshots/phase2/

## Notes
The detection query uses `bucket _time span=60s` to look for more than 5 failed logon attempts within a 1-minute window from a single source address. By including Event ID 4624, the query can also highlight if the attack eventually resulted in a successful logon (`successes > 0`).
