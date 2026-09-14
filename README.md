# SOC Home Lab

A cybersecurity SOC home lab environment built with VirtualBox, Splunk SIEM, Kali Linux, and Windows 10. This project serves as a comprehensive portfolio piece targeting SOC Analyst and Security Engineer roles, focusing on SIEM deployment, log forwarding, attack simulation, detection engineering, and automation.

## Architecture & Environment Stack

- **SIEM:** Splunk Enterprise 10.2.3 on Ubuntu Server 26.04 (`192.168.56.10`)
- **Attack Target:** Windows 10 Pro (`192.168.56.101`)
- **Attacker:** Kali Linux (`192.168.56.102`)
- **Network Environment:** VirtualBox Host-Only Network (`192.168.56.0/24`)
- **Scripting:** Python 3 (Automation)
- **Query Language:** SPL (Splunk Search Processing Language)

## Repository Structure

- `docs/` — Phase write-ups, configuration documentation, and cheatsheets.
- `splunk/`
  - `queries/` — `.spl` detection rule files, mapped to MITRE ATT&CK techniques.
  - `dashboards/` — Exported dashboard XML files.
  - `alerts/` — Alert configurations.
- `scripts/` — Python automation scripts.
- `attack-scenarios/` — Markdown write-ups documenting specific attack simulations.
- `screenshots/` — Evidence screenshots categorized by phase.
- `incident-reports/` — Documentation for incident response exercises.

## Project Phases

- [x] **Phase 1: Complete** — SIEM setup and log forwarding (Splunk Universal Forwarder).
- [x] **Phase 2: Complete** — Attack simulation and Splunk detection rule engineering.
- [/] **Phase 3: In Progress** — Sysmon deployment and advanced logging configuration.
- [ ] **Phase 4: Not started** — Python automation and VirusTotal API integration.
- [ ] **Phase 5: Not started** — Incident response procedures (optional).

## Detection Engineering Methodology

- All detection queries are written in SPL and thoroughly tested in Splunk.
- Each detection rule corresponds to a specific **MITRE ATT&CK** technique ID.
- Detection thresholds and logic are documented alongside the rules to provide context and reasoning.

## Lab Credentials

- **Splunk Web Interface:** [http://192.168.56.10:8000](http://192.168.56.10:8000)
- **Ubuntu Server (SSH/Console):** `analyst` / `Analyst@2026!`
- **Windows 10 Target:** `analyst` / `Analyst@2026!`
- **Kali Linux Attacker:** `kali` / `kali`

## Usage & Disclaimer

- **Network Restrictions:** All attack simulations are strictly confined to the `192.168.56.0/24` Host-Only network.
- **Documentation:** For VM configurations and step-by-step setup details, please refer to the `docs/` directory.

---

_This repository is a demonstration of practical cybersecurity skills and is intended for educational and portfolio purposes only._
