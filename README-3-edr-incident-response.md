# 🔍 EDR Investigation & Incident Response with Microsoft Defender
> **Tools:** Microsoft Defender for Endpoint · Microsoft Sentinel · Azure VMs · KQL  
> **Environment:** Live Production SOC — Josh Madakor's Cyber Range  
> **Role:** Incident Responder / Security Engineer

---

## 📋 Overview

Used **Microsoft Defender for Endpoint** as the primary EDR solution to onboard virtual machines, monitor endpoint telemetry, investigate security alerts, and respond to active threats. Conducted live incident response against real attacker activity within the Cyber Range shared SOC environment — not a simulation.

---

## 🎯 Objectives

- Onboard Windows and Linux VMs to Defender for Endpoint for continuous protection
- Investigate endpoint alerts and trace full attack chains using the Defender incident timeline
- Perform containment actions including device isolation and process termination
- Document incidents in structured IR report format

---

## 🔧 Technical Breakdown

### 1. Endpoint Onboarding
- Deployed **Defender for Endpoint sensors** to Azure VMs via onboarding packages (local script and Intune methods)
- Verified telemetry ingestion in the Microsoft 365 Defender portal
- Configured device groups and tagging for asset classification

### 2. Alert Investigation
- Investigated alerts for:
  - **Remote Code Execution (RCE)** — traced process tree from initial execution to child processes
  - **Credential access** — identified LSASS access and suspicious PowerShell activity
  - **Persistence mechanisms** — detected registry run keys and scheduled task creation
- Used **Advanced Hunting (KQL)** to pivot from a single IOC to related events across device and user scope
- Leveraged the **Incident Graph** to visualize attack chain relationships between alerts, users, devices, and IPs

### 3. Containment & Remediation
- Executed **device isolation** via Defender to cut off compromised VMs from the network while preserving forensic telemetry
- Terminated malicious processes and removed persistence mechanisms (scheduled tasks, registry modifications)
- Ran **full antivirus scans** post-containment and validated clean state before releasing isolation

---

## 🔍 Sample Advanced Hunting Queries (KQL)

**Suspicious Process Execution**
```kql
DeviceProcessEvents
| where FileName in~ ("cmd.exe", "powershell.exe", "wscript.exe")
| where InitiatingProcessFileName !in~ ("explorer.exe", "services.exe")
| project Timestamp, DeviceName, FileName, ProcessCommandLine, InitiatingProcessFileName
| order by Timestamp desc
```

**LSASS Access Attempts**
```kql
DeviceEvents
| where ActionType == "OpenProcessApiCall"
| where FileName =~ "lsass.exe"
| project Timestamp, DeviceName, InitiatingProcessFileName, InitiatingProcessCommandLine
```

**Scheduled Task Creation**
```kql
DeviceEvents
| where ActionType == "ScheduledTaskCreated"
| project Timestamp, DeviceName, InitiatingProcessFileName, AdditionalFields
| order by Timestamp desc
```

**Network Connections to Suspicious IPs**
```kql
DeviceNetworkEvents
| where RemotePort in (4444, 1337, 8080, 9001)
| project Timestamp, DeviceName, RemoteIP, RemotePort, InitiatingProcessFileName
| order by Timestamp desc
```

---

## 📋 Incident Response Process

```
1. DETECTION     →  Alert fires in Defender / Sentinel
2. TRIAGE        →  Assess severity, affected assets, and initial scope
3. INVESTIGATION →  Device timeline, advanced hunting, incident graph
4. CONTAINMENT   →  Isolate device, block IOCs, disable accounts
5. ERADICATION   →  Remove malware, persistence, and attacker artifacts
6. RECOVERY      →  Validate clean state, re-integrate device
7. DOCUMENTATION →  IR report with timeline, scope, actions, and lessons learned
```

---

## 📊 Key Results

| Metric | Result |
|--------|--------|
| VMs onboarded | Windows Server + Ubuntu endpoints |
| Incident types investigated | RCE, credential access, persistence, lateral movement |
| Containment actions | Device isolation, process kill, persistence removal |
| Documentation | Structured IR reports per incident |

---

## 🧠 Skills Demonstrated

- `Microsoft Defender for Endpoint` — onboarding, alert triage, device isolation
- `Advanced Hunting (KQL)` — cross-asset threat pivoting
- `Incident Response` — full IR lifecycle (detect → contain → recover → report)
- `Forensic Analysis` — device timeline, process tree analysis
- `Microsoft Sentinel` — SIEM/EDR integration for correlated investigation

---

## 📁 Repo Structure

```
defender-edr-incident-response/
├── README.md
├── hunting-queries/
│   ├── suspicious-processes.kql
│   ├── lsass-access.kql
│   ├── persistence-detection.kql
│   └── network-anomalies.kql
├── ir-process/
│   └── incident-response-playbook.md
└── reports/
    └── ir-report-template.md
```

---

## 🔗 Related Projects

- [Vulnerability Management Program](../vulnerability-management-program)
- [SIEM & Threat Detection with Microsoft Sentinel](../sentinel-siem-threat-detection)
- [AI-Powered Security Tooling — LLM & Agentic AI](../llm-agentic-security-tooling)

---

> 💼 *Completed as part of Josh Madakor's Cyber Range — a live production SOC environment with real attacker activity and enterprise-grade security tools.*
