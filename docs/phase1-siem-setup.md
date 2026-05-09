# Phase 1 — SIEM Setup & First Logs

## Overview

Built a working Splunk SIEM instance on Ubuntu Server 26.04 receiving
real Windows Event Logs from a Windows 10 target machine via the
Splunk Universal Forwarder.

## Environment

| Component      | Details                                |
| -------------- | -------------------------------------- |
| Splunk Server  | Ubuntu 26.04, IP 192.168.56.10         |
| Windows Target | Windows 10 Pro, IP 192.168.56.101      |
| Splunk Version | 10.2.3                                 |
| Network        | VirtualBox Host-Only (192.168.56.0/24) |

## What Was Built

- Ubuntu Server VM with static IP via Netplan
- Splunk Enterprise installed and configured as splunk user
- Windows 10 VM with Splunk Universal Forwarder
- Log pipeline: WinEventLog Security/System/Application → Splunk index=main

## Key Issues & Fixes

- Forwarder not reading inputs.conf → fixed by adding NT SERVICE\SplunkForwarder to Event Log Readers group
- Splunk disk space error → expanded LVM logical volume from 19GB to 38GB
- Splunk running as root deprecated → created splunk user and transferred ownership

## Verification

- index=main | stats count by sourcetype
- Returned 113 events across WinEventLog:Security, Application, System.

## Screenshots

- See screenshots/phase1/
