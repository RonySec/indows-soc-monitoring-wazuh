# Investigation 01 — PowerShell and SecEdit Alert Analysis

**Project:** Windows 11 Security Monitoring with Wazuh SIEM and Sysmon  
**Investigation Date:** October 28, 2026  
**Endpoint:** WIN-SOC-LAB  
**Tools:** Wazuh SIEM, Microsoft Sysmon, Windows PowerShell  
**Classification:** Likely Benign — Security Configuration Assessment

## 1. Investigation Objective

Investigate PowerShell and SecEdit security alerts detected by Wazuh to determine whether the observed activity represents potentially malicious behavior or legitimate system operations.

The investigation focuses on reviewing process execution, command-line activity, parent-child process relationships, and associated MITRE ATT&CK classifications.

## 2. Initial Detection

While reviewing security events in Wazuh Threat Hunting, multiple alerts were observed involving PowerShell and the Windows Security Configuration Editor (`SecEdit.exe`).

Two detection rules were selected for analysis:

| Rule ID | Detection | MITRE ATT&CK |
| --- | --- | --- |
| 92021 | PowerShell used to delete files or directories | T1070.004 — File Deletion |
| 92066 | SecEdit launched through PowerShell from a flagged location | T1059.001 — PowerShell |

Both alerts were generated from Sysmon Event ID 1 (Process Creation) and collected from the Windows 11 endpoint.

## 3. Evidence Analysis

### Alert 1 — PowerShell File Deletion

The first alert identified a PowerShell command that exported the local Windows security policy, searched for a specific security setting, and deleted a temporary file.

**Observed command:**

```powershell
powershell secedit /export /cfg $env:TEMP\secpol.cfg; Get-Content $env:TEMP\secpol.cfg | Select-String "LockoutBadCount"; Remove-Item $env:TEMP\secpol.cfg
```

The command performs three operations:

1. Exports the Windows security policy to a temporary configuration file.
2. Searches the exported file for the account lockout threshold setting (`LockoutBadCount`).
3. Deletes the temporary configuration file after the check.

The deletion operation triggered Wazuh rule 92021, associated with MITRE ATT&CK T1070.004 (File Deletion).

Although file deletion can be associated with defense evasion, the observed command was consistent with temporary-file cleanup following a security configuration assessment.

**Additional evidence:**

- **Parent process:** `wazuh-agent.exe`
- **Execution account:** `NT AUTHORITY\SYSTEM`
- **Event source:** `Microsoft-Windows-Sysmon/Operational`
- **Event ID:** 1 — Process Creation

The parent process relationship suggested that the command was initiated through the Wazuh agent rather than an unidentified interactive user.

### Alert 2 — SecEdit Process Execution

A related alert identified the execution of `SecEdit.exe` through Windows PowerShell.

The recorded parent command performed another security policy check, this time targeting `PasswordHistorySize`.

**Observed command:**

```powershell
"C:\WINDOWS\system32\SecEdit.exe" /export /cfg C:\WINDOWS\TEMP\secpol.cfg
```

Wazuh generated rule 92066, identifying SecEdit execution through PowerShell.

The event was associated with MITRE ATT&CK T1059.001 (PowerShell).

Reviewing the parent command line revealed that the process was part of a security configuration assessment examining Windows password history settings.

The SecEdit event and PowerShell file-deletion alert represented separate command executions, but both followed a similar pattern of exporting and inspecting Windows security policy.

## 4. Event Correlation

The Wazuh Threat Hunting dashboard displayed multiple PowerShell and SecEdit alerts within the same investigation period.

The events shared several characteristics:

- Originated from the same Windows 11 endpoint.
- Involved PowerShell and Windows security configuration commands.
- Were recorded through Sysmon process creation monitoring.
- Used temporary security policy exports.
- Were consistent with automated configuration assessment activity.

Reviewing the command lines and parent-child process relationships provided additional context that was not apparent from the alert descriptions alone.

## 5. Investigation Findings

The observed activity was assessed as **likely benign security configuration assessment activity**.

This conclusion was supported by the commands' identifiable security assessment purpose, the involvement of the Wazuh agent, and the repeated pattern of policy inspection followed by temporary-file cleanup.

The reviewed evidence did not establish malicious intent or confirmed system compromise.

However, the available records alone do not conclusively prove that every related process was authorized.

## 6. Conclusion

This investigation demonstrated how security alerts can appear suspicious when viewed without sufficient context.

By examining Sysmon process creation events, PowerShell command lines, parent processes, and related Wazuh alerts, the activity could be assessed more accurately.

**Skills demonstrated:**

- SIEM alert triage using Wazuh
- Windows event log analysis
- Sysmon Event ID 1 interpretation
- PowerShell command-line analysis
- Parent-child process correlation
- MITRE ATT&CK mapping
- Distinguishing likely benign activity from suspicious detections

**Final disposition:** Likely Benign — Security Configuration Assessment

## 7. Supporting Evidence

- `04-PowerShell-Event-Correlation.png` — Wazuh Threat Hunting event correlation screenshot
- `Investigation-01-Raw-Alert.json` — PowerShell file-deletion detection
- `Investigation-01-SecEdit-Event.json` — Related SecEdit process creation event
