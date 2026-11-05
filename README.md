# Windows 11 SOC Monitoring Lab — Wazuh & Sysmon

## Project Overview

Built a virtualized Security Operations Center (SOC) home lab to practice Windows endpoint monitoring, security alert analysis, and incident investigation using Wazuh SIEM and Microsoft Sysmon.

The project demonstrates how security events are collected, analyzed, and correlated to distinguish potentially suspicious activity from legitimate system operations.

## Lab Environment

| Component | Configuration |
|---|---|
| Host Machine | Apple iMac (2017), macOS Ventura |
| Virtualization | Oracle VirtualBox |
| SIEM Platform | Wazuh 4.14.8 |
| Monitored Endpoint | Windows 11 |
| Endpoint Monitoring | Wazuh Agent and Microsoft Sysmon |
| Network | VirtualBox NAT and Host-Only Network |

## Project Objectives

- Deploy and configure a functional SIEM environment.
- Integrate Windows 11 endpoint telemetry with Wazuh.
- Configure Sysmon to monitor process creation activity.
- Investigate security alerts using command-line and process information.
- Correlate related security events and assess potential threats.

## Implementation and Validation

1. Deployed the Wazuh virtual appliance and Windows 11 virtual machine using VirtualBox.
2. Installed and enrolled the Wazuh agent on Windows 11.
3. Installed Sysmon and configured the Wazuh agent to collect Sysmon events.
4. Verified that the Windows endpoint appeared as an active agent in Wazuh.
5. Confirmed Sysmon Event ID 1 (Process Creation) alerts were searchable in Wazuh Threat Hunting.

## Security Investigation — PowerShell and SecEdit

During security monitoring, Wazuh generated alerts involving PowerShell file deletion and Windows Security Configuration Editor (SecEdit) execution.

**Detection rules:**

- Rule 92021 — PowerShell file deletion, mapped to MITRE ATT&CK T1070.004.
- Rule 92066 — SecEdit execution through PowerShell, mapped to MITRE ATT&CK T1059.001.

### Investigation Process

Reviewed the original JSON alerts, command-line arguments, process execution details, parent-child relationships, and correlated events within the Wazuh dashboard.

The commands exported Windows security policy information, inspected settings including `LockoutBadCount` and `PasswordHistorySize`, and removed temporary configuration files.

### Findings

The activity was assessed as **likely benign security configuration assessment activity**, supported by the observed command behavior and process relationships.

The investigation highlighted the importance of evaluating security alerts in context rather than treating every detection as confirmed malicious activity.

## Skills Demonstrated

- SIEM deployment and endpoint monitoring
- Windows event log and Sysmon analysis
- Security alert triage
- PowerShell command-line interpretation
- Process correlation
- MITRE ATT&CK mapping
- Investigation documentation

## Supporting Evidence

Supporting screenshots, original Wazuh JSON alerts, lab configuration notes, and the investigation report are included in this repository.

## Project Status

**Completed — October 2026**

The lab successfully collected Windows Sysmon telemetry, generated searchable alerts, and supported a documented security investigation.
