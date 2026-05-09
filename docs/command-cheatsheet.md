# SOC Home Lab — Command Cheatsheet

## Lab IP Reference

| Machine                | IP             |
| ---------------------- | -------------- |
| Splunk Server (Ubuntu) | 192.168.56.10  |
| Windows Target         | 192.168.56.101 |
| Kali Attacker          | 192.168.56.102 |

---

## Ubuntu / Splunk Server

### Splunk Management

```bash
# Start Splunk
sudo -u splunk /opt/splunk/bin/splunk start

# Stop Splunk
sudo -u splunk /opt/splunk/bin/splunk stop

# Restart Splunk
sudo -u splunk /opt/splunk/bin/splunk restart

# Check Splunk status
sudo -u splunk /opt/splunk/bin/splunk status

# Check receiving port
sudo -u splunk /opt/splunk/bin/splunk list forward-server
```

### Network

```bash
# Show all network interfaces and IPs
ip a

# Check disk usage
df -h

# Check open listening ports
sudo ss -t -l -n -p
```

### System

```bash
# Shut down Ubuntu
sudo shutdown now

# Check LVM volume group free space
sudo vgdisplay

# Expand logical volume to use all free space
sudo lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```

---

## Windows Target (PowerShell as Admin)

### Splunk Forwarder

```powershell
# Start forwarder
Start-Service SplunkForwarder

# Stop forwarder
Stop-Service SplunkForwarder -Force

# Restart forwarder
Stop-Service SplunkForwarder -Force
Start-Service SplunkForwarder

# Check forwarder config
type "C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf"
type "C:\Program Files\SplunkUniversalForwarder\etc\system\local\outputs.conf"

# Check forwarder logs
type "C:\Program Files\SplunkUniversalForwarder\var\log\splunk\splunkd.log" | Select-Object -Last 30
```

### Network & Audit

```powershell
# Test connectivity to Splunk server
Test-NetConnection -ComputerName 192.168.56.10 -Port 9997

# Enable network connection auditing (for 5156/5157 events)
auditpol /set /subcategory:"Filtering Platform Connection" /success:enable /failure:enable

# Check all network adapters
ipconfig
```

---

## Kali Linux

### Reconnaissance

```bash
# Full service and version scan
nmap -sV -sC <target>

# Ping test (4 packets)
ping <target> -c 4
```

---

## SPL Queries (Splunk)

### General

```spl
# Count events by sourcetype
index=main | stats count by sourcetype

# Count events by EventCode
index=main sourcetype=WinEventLog:Security | stats count by EventCode
```

### Detection Rules

```spl
# Detect network reconnaissance (port scan from single IP)
index=main sourcetype=WinEventLog:Security EventCode=5156
| stats count by Source_Address, Destination_Port
| where count > 5
| sort -count

# View Nmap scan traffic from Kali
index=main sourcetype=WinEventLog:Security EventCode=5156 OR EventCode=5157 Source_Address=192.168.56.102
| table _time, Source_Address, Destination_Address, Destination_Port, Application_Name
```

---

## Git

```powershell
# Stage all changes
git add .

# Commit with message
git commit -m "your message here"

# Push to GitHub
git push origin main
```
