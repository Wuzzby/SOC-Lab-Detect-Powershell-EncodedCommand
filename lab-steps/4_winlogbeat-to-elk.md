# Step 4: Forwarding Sysmon Logs to ELK with Winlogbeat

## Goal

Forward Sysmon logs to **Elasticsearch** using **Winlogbeat**, enabling centralized detection and analysis in **Kibana**.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Winlogbeat | Shipper that forwards Windows Event Logs to Elasticsearch |
| Sysmon | Source of rich, structured security logs |
| ELK Stack (ElasticSearch + Kibana) | Ingests and visualizes log data for threat hunting and alerting |

---

##  Why Use Winlogbeat?

- Helps **centralize logs** from many systems into a single pane of glass
- Essential in **real-world SOCs** where analysts monitor multiple endpoints
- Allows writing **KQL queries** to search for attack behaviors like obfuscated PowerShell

---

##  Configuration Steps

###  1. Unzip Winlogbeat on Windows 10 VM

- Extract to: `C:\Program Files\Winlogbeat` (or Desktop for testing)
- Open **PowerShell as Administrator**

---

###  2. Configure the Winlogbeat YAML File

Open the file:

powershell
    notepad winlogbeat.yml

Modify:

    Enable Sysmon Logs
        winlogbeat.event_logs:
            - name: Microsoft-Windows-Sysmon/Operational
                ignore_older: 72h

    Set Output to Elasticsearch
    Change this block:
        output.elasticsearch:
            hosts: ["http://<YOUR-HOST-IP>:9200"]
    Replace <YOUR-HOST-IP> with the actual IP of your host machine running Elasticsearch.

### 3. Install and Start the Winlogbeat Service

Still in PowerShell:
    cd "C:\Program Files\Winlogbeat"
    .\install-service-winlogbeat.ps1
    Start-Service winlogbeat
Check that it's running:
    Get-Service winlogbeat
You should see Status: Running

## Verifying Logs in Kibana
1. Open a browser on your host
2. Go to:
    http://localhost:5601
3. Navigate to: 
    Kibana -> Discover
4. Use this query to find your PowerShell event:
    process.name: "powershell.exe" AND process.command_line: "*-EncodedCommand*"
You should see your obfuscated PowerShell execution show up, complete with timestamp, user, and command line!


## Detection Logic Created
Saved KQL detection:
    process.name: "powershell.exe" AND process.command_line: "*-EncodedCommand*"
This helps detect:
    Obfuscated execution attempts


## (Optional) Sigma Rule
Located in detections/powershell-encodedcommand.yml
Mapped to: 
    MITRE T1059.001
    T1027
    T1105


## Outcome
Successfully forwarded Sysmon logs to ELK using Winlogbeat
Created a search that detects obfuscated PowerShell attacks
Built a detection pipeline from endpoint to SIEM -- just like in a real SOC

## What's Next?
You can now:
    Write alerts in Kibana or Elastic SIEM
    Add Sigma RUles
    Export detection timelines
    Build additional detections