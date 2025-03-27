# Step 1: Sysmon Setup

## Goal

To install and configure **Sysmon** on a Windows 10 VM in order to collect rich endpoint telemetry such as process creation, network connections, and command-line activity. This data will later be used for threat detection and investigation.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| [Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) | Logs detailed Windows event data |
| [SwiftOnSecurity Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config) | Provides a battle-tested XML config to reduce noise and increase value |

---

## Why Use Sysmon?

In a SOC environment, **default Windows event logs** often miss important forensic details. Sysmon fills in the gaps by:
- Logging **process creation**, including **parent-child relationships**
- Recording **network connections** made by processes
- Capturing **command-line arguments**
- Assigning unique **GUIDs** to processes to track them across log sources

---

## Installation Steps

> These steps were performed inside a Windows 10 virtual machine running in VirtualBox.

### Step 1: Download Sysmon

1. Visit: [Sysmon download page](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
2. Download the `.zip` file and extract it
3. Move the contents to a new folder on your desktop called `Sysmon`

### Step 2: Download the Config File

1. Open PowerShell
2. Run this command to download the config file directly to the desktop:

```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml" -OutFile "$env:USERPROFILE\Desktop\sysmonconfig-export.xml"


### Step 3: Install Sysmon

1. Open Commmand Prompt as Admin
2. Navigate to the Sysmon Folder
    cd %USERPROFILE%\Desktop\Sysmon
3. Run the install command:
    Sysmon64.exe -accepteula -i ..\sysmonconfig-export.xml

## Sample Output

When installed correctly, Sysmon should return:
    Sysmon installed.
    Configuration file validated.
And it will begin logging immediately

## Where Logs Go
Sysmon logs are wrriten to:
    Event Viewer → Applications and Services Logs → Microsoft → Windows → Sysmon → Operational
You'll see:
    Event ID 1 → Process creation

    Event ID 3 → Network connections

    Event ID 10 → Process access (if enabled)

## What You Should See
If everything is set up properly:
    Sysmon appears as a running service
    Event Viewer shows real-time logs under the Sysmon Operational channel

## What This Enables
This config lays the foundation for the rest of the lab
    You'll simulate attacker behavior next
    Then use these logs to detect malicious PowerShell activity