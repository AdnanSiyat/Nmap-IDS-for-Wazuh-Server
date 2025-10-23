# Nmap IDS for Wazuh Server

Detect Nmap scans targeting a Wazuh server. This repository contains a simple local Wazuh rule, test data, and instructions to reproduce detection and publish the project to GitHub.

> **Warning:** Use this repository only on systems you own or have permission to test. Unauthorized scanning or intrusion is illegal.

## Repository layout

```
Nmap_IDS_for_Wazuh_Server/
├─ README.md
├─ LICENSE
├─ .gitignore
├─ wazuh/
│  └─ local_rules.xml
├─ test-data/
│  ├─ nmap-commands.txt
│  └─ nmap-scan-output.txt
├─ docs/
│  ├─ images/
│  │  ├─ step-1-local-rules.png
│  │  ├─ step-4-kali-nmap.png
│  │  └─ step-5-alerts-success.png
│  └─ reproduction.md
└─ scripts/
   └─ validate-and-restart.sh
```

---

## Summary / Goal

Create a low-friction detection for Nmap scans against a Wazuh server using a local Wazuh rule. The rule in `wazuh/local_rules.xml` detects the string `nmap` in incoming logs and raises a Level 10 alert. This is a pragmatic demonstration: it works in environments where the Nmap user-agent or command string is visible in logs (for example, web server logs showing `user-agent: Nmap Scripting Engine`). For greater coverage, see **Extensions** below.

## Quick start (Deploy to your Wazuh Manager)

1. Copy the rule file to your Wazuh manager (example path):
   ```bash
   sudo cp wazuh/local_rules.xml /var/ossec/etc/rules/local_rules.xml
   ```
2. Validate the XML:
   ```bash
   sudo xmllint --noout /var/ossec/etc/rules/local_rules.xml
   ```
3. Restart Wazuh manager:
   ```bash
   sudo systemctl restart wazuh-manager
   ```
4. Reproduce a test scan from an attacker machine (replace `10.0.0.121` with your Wazuh server IP):
   ```bash
   nmap -sS 10.0.0.121
   nmap -A 10.0.0.121
   nmap -p 22,80,443 10.0.0.121
   ```
5. Tail the alerts to confirm detection:
   ```bash
   sudo tail -f /var/ossec/logs/alerts/alerts.log
   ```

## What is included
* `wazuh/local_rules.xml` — working rule that generated the alerts in testing.
* `test-data/` — the commands used and sanitized Nmap output for reference.
* `docs/images/` — screenshots (steps 1, 4, 5) showing the rule file, attacker scan, and alerts.
* `scripts/validate-and-restart.sh` — helper to validate XML and restart Wazuh safely.

## Extensions / Improvements (recommended)
* Correlation rule: detect multiple distinct port connection attempts within short time window (common for port scanners).
* Network-based detection: use Zeek or Suricata signatures to detect SYN floods / intense SYN activity (Nmap `-sS`).
* Agent-side detection: capture `process`/`cmdline` events and auditd logs to match `nmap` in executed commands.
* Rate-limiting rules: fire only when threshold of connections in X seconds is exceeded to reduce false positives.

## License
MIT (see `LICENSE` file).

---
If you'd like, I can also open a PR template, add CI to run `xmllint` automatically on push, or help you create a real GitHub repo (I will give you the exact git commands to run).

