# Nmap IDS for Wazuh Server

Detect Nmap scans targeting a Wazuh server. This repository contains a simple local Wazuh rule, test data, and instructions to reproduce detection and publish the project to GitHub.

> **Warning:** Use this repository only on systems you own or have permission to test. Unauthorized scanning or intrusion is illegal.

## Repository layout

```
Nmap_IDS_for_Wazuh_Server/
├─ README.md
├─ LICENSE
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
   ![step-1-local-rules](https://github.com/user-attachments/assets/5ca78fc6-f1fc-4b2e-ae11-f2a36fa5e869)

2. Validate the XML:
   ```bash
   sudo xmllint --noout /var/ossec/etc/rules/local_rules.xml
   ```
3. Restart Wazuh manager:
   ```bash
   sudo systemctl restart wazuh-manager
   ```
4. Reproduce a test scan from an attacker machine (replace `10.0.0.XX` with your Wazuh server IP):
   ```bash
   nmap -sS 10.0.0.XX
   nmap -A 10.0.0.XX
   nmap -p 22,80,443 10.0.0.XX
   ```
   ![step-4-kali-nmap](https://github.com/user-attachments/assets/b2c45043-6b11-4b7d-9a5b-75f1f7d7f426)

   
5. Tail the alerts to confirm detection:
   ```bash
   sudo tail -f /var/ossec/logs/alerts/alerts.log
   ```
   ![IMG_5899](https://github.com/user-attachments/assets/3f6bf46d-808b-4e8c-be88-706ec4f753af)


What I Learned
Working on this project gave me practical, end‑to‑end experience in detection engineering and reinforced several professional skills:
- Custom Rule Development

      Built and deployed a Wazuh local_rules.xml rule to detect Nmap activity, learning how to translate a detection idea into a working, testable configuration.

- Validation and Troubleshooting

      Strengthened my ability to validate XML configurations (xmllint), restart services safely, and confirm rule behavior by analyzing live Wazuh alert logs.

- Adversary Simulation

      Practiced simulating attacker behavior with different Nmap scan types (-sS, -A, targeted ports) to verify detection coverage and understand how attackers leave traces in logs.

- Detection Trade‑offs

      Gained insight into the balance between simple string‑based detections (fast, low‑friction) and more advanced approaches like correlation rules or rate‑limiting to reduce false positives.

- Operational Workflow

      Improved my workflow for safely deploying changes, validating results, and documenting reproducible steps — habits that are critical in SOC and detection engineering roles.

- Professional Communication

      Learned how to turn a technical lab into a clear, recruiter‑facing portfolio project with structured documentation, visuals, and reproducible instructions.

My LiknedIn: www.linkedin.com/in/
adnan-siyat-439542309
