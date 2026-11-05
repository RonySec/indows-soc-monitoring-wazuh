# SOC Home Lab — Setup & Configuration

**Environment:** Virtualized Cybersecurity Home Lab
**Platform:** Oracle VirtualBox
**Last Updated:** October 29, 2026

## Objective

Build a virtualized cybersecurity home lab to practice security monitoring, log analysis, threat detection, identity and access management, vulnerability assessment, and incident investigation.
The lab provides a reusable environment for multiple cybersecurity portfolio projects, beginning with Windows endpoint monitoring using Wazuh SIEM and Sysmon.

## Lab Environment


### Host Machine

- Computer: Apple iMac (2017)
- Processor: Intel Core i5, 3.4 GHz
- Memory: 16 GB RAM
- Operating System: macOS Ventura
- Storage: 1 TB Fusion Drive
- Virtualization Platform: Oracle VirtualBox

### Wazuh SIEM Server

- Deployment: Wazuh virtual appliance
- Allocated Memory: 8 GB RAM
- Processors: 4 virtual CPUs
- Components: Wazuh Manager, Indexer, Dashboard, and API
- Dashboard Address: https://192.168.56.2
- Secondary NAT IP: 10.0.2.15

### Windows 11 Endpoint

- Operating System: Windows 11
- Allocated Memory: 4 GB RAM
- Processors: 2 virtual CPUs
- Storage: 80 GB dynamically allocated
- Wazuh Agent Name: WIN-SOC-LAB
- Agent ID: 001
- Agent IP: 192.168.56.3
- Monitoring Tools: Wazuh Agent and Microsoft Sysmon

## Lab Architecture

The environment consists of two virtual machines hosted on the iMac using Oracle VirtualBox.

`Windows 11 Endpoint → Wazuh Agent → Wazuh Server → Wazuh Dashboard`

The Windows 11 VM serves as the monitored endpoint. Sysmon records detailed Windows system activity, including process creation events.
The Wazuh agent collects configured Windows event channels and forwards telemetry to the Wazuh Manager.
The Wazuh server processes incoming events, evaluates detection rules, and stores generated alerts for investigation through the dashboard.
The dashboard is accessible from the macOS host through the lab's virtual network.

## Setup Progress


### Completed

- Created Wazuh server virtual machine.
- Installed Windows 11 virtual machine.
- Configured virtual machine resources.
- Installed and enrolled the Wazuh agent.
- Verified Windows agent connectivity.
- Installed Microsoft Sysmon.
- Configured the Wazuh agent to collect Sysmon events.
- Verified Sysmon-generated alerts in the Wazuh dashboard.
- Restored Wazuh Manager operation following a service failure.

### Pending Verification

- Independently verify Windows Security event channel collection.
- Confirm stable operation of the lab after restarting both virtual machines.

## Sysmon Integration Validation

**Validation Date:** October 29, 2026

### Configuration

The Windows 11 endpoint was configured to collect events from:
`Microsoft-Windows-Sysmon/Operational`
The Wazuh agent configuration includes:

```xml
<localfile>
<location>Microsoft-Windows-Sysmon/Operational</location>
<log_format>eventchannel</log_format>
</localfile>
```


### Validation Results

- Endpoint: WIN-SOC-LAB
- Wazuh Agent ID: 001
- Confirmed Event Type: Sysmon Event ID 1 — Process Creation
- Detection Results: 20 matching Sysmon alerts during the selected seven-day search period
- Event Source: Microsoft-Windows-Sysmon
- Dashboard: Wazuh Threat Hunting → Events

### Outcome

Successfully confirmed that Sysmon events generated on the Windows 11 endpoint are collected by the Wazuh agent, forwarded to the Wazuh server, and processed into searchable security alerts.
This validates the Sysmon-to-Wazuh alert pipeline.
**Evidence Location:** Cybersecurity-SOC-Lab/Screenshots/

## Troubleshooting and Maintenance


### Wazuh API Connection Failure

**Date:** October 29, 2026
**Issue:** The Wazuh dashboard displayed an "API is down" error.
**Diagnosis:** The Wazuh Manager service returned a failed status.
**Resolution:** Restarted the Wazuh Manager service using:

```bash
sudo systemctl restart wazuh-manager
```

**Verified recovery using:**

```bash
sudo systemctl is-active wazuh-manager
```

**Result:** Service returned active, and the Wazuh dashboard resumed normal operation.

### Resource Considerations

The iMac contains 16 GB of physical memory.
The Wazuh and Windows 11 virtual machines have a combined allocation of 12 GB RAM.
Running both virtual machines simultaneously may create memory pressure on the host.
When reviewing previously indexed security alerts, the Windows 11 VM can remain powered off to conserve system resources.
Both virtual machines must be running to generate and forward new Windows security events.

## Remaining Lab Setup Tasks

1. Verify Windows Security event channel collection independently of Sysmon.
2. Confirm that Wazuh Manager and its associated services operate reliably after VM startup.
3. Record any additional configuration changes or troubleshooting procedures.

## Lab Setup Status

**Status:** Core infrastructure operational — final validation pending.
The Wazuh SIEM environment is functional, the Windows agent has successfully communicated with the server, and Sysmon-generated security alerts have been verified.
The environment is ready to support the first cybersecurity portfolio project while the remaining configuration checks are completed.
