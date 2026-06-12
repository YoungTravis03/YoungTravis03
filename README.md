# <a href="https://www.linkedin.com/in/travis-young-516156102/">Travis Young</a>'s IT and Cybersecurity Project Portfolio 🔐

I'm passionate about cybersecurity and love tackling complex challenges through hands-on projects. From vulnerability management to threat detection, these projects allow me to dive deep into the ever-evolving landscape of cybersecurity. Please feel free to check them out and see the work I’ve put into enhancing security operations and processes!


## ⚠️ Vulnerability Management Projects

- **[Vulnerability Management Program Implementation](https://github.com/YoungTravis03/vulnerability-management-program-/tree/main)**
- **[Programmatic Vulnerability Remediations (PowerShell, BASH, & Shell Commands)](https://github.com/joshcybertest/programmatic-vulnerability-remediations)**

## 🚨 Threat Hunting and Security Operations

- **[Threat Hunting Scenario (Tor Browser Usage)](https://github.com/joshmadakor0/threat-hunting-scenario-tor)**

<hr/>

## 🤳 Connect With Me

[<img align="left" alt="___________ | YouTube" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/youtube.svg" />][youtube]
[<img align="left" alt="___________ | Twitter" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/twitter.svg" />][twitter]
[<img align="left" alt="https://www.linkedin.com/in/travis-young-516156102/| LinkedIn" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/linkedin.svg" />][linkedin]
[<img align="left" alt="___________ | Instagram" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/instagram.svg" />][instagram]

[twitter]: https://twitter.com/___________
[youtube]: https://www.youtube.com/c/___________
[instagram]: https://www.instagram.com/___________
[linkedin]: https://linkedin.com/in/https://www.linkedin.com/in/travis-young-516156102/

<!--
<img width="35" alt="image" src="https://github.com/user-attachments/assets/2f41c7cd-5ea8-4475-b451-a37161b6c3fb"> 
<img width="35" alt="image" src="https://github.com/user-attachments/assets/77649969-9910-4994-8b96-74a116cfb2a8">
-->
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

- [Vulnerability Management Program](# 🛡️ Enterprise Vulnerability Management Program
> **Tools:** Tenable Vulnerability Management · Microsoft Azure · Windows Server · Linux  
> **Environment:** Live Production SOC — Josh Madakor's Cyber Range  
> **Role:** Vulnerability Analyst / Security Engineer

---

## 📋 Overview

Deployed and managed an end-to-end vulnerability management program across a shared enterprise environment consisting of Windows and Linux virtual machines hosted in Microsoft Azure. Leveraged **Tenable Vulnerability Management** to conduct authenticated scans, analyze findings, and prioritize remediation based on CVSS severity scores and asset criticality.

---

## 🎯 Objectives

- Identify and catalog vulnerabilities across all in-scope Azure-hosted assets
- Prioritize findings using risk-based scoring (CVSS + asset exposure)
- Remediate or mitigate critical and high vulnerabilities within defined SLAs
- Produce executive-level and technical remediation reports

---

## 🔧 Technical Breakdown

### 1. Scan Configuration
- Configured **credentialed scan policies** in Tenable to maximize coverage and reduce false positives
- Scoped scans to target Azure VM subnets and defined scan windows to minimize performance impact
- Created asset groups and tags for organized reporting and priority tracking

### 2. Analysis & Prioritization
- Triaged findings by filtering on **Critical/High CVSS scores** and active exploit availability
- Cross-referenced Tenable results with **NIST NVD** and vendor security advisories to validate severity
- Used **VPR (Vulnerability Priority Rating)** alongside CVSS for risk-based prioritization

### 3. Remediation
- Applied OS patches, updated misconfigured services, and hardened default configurations on **Windows Server** and **Ubuntu** systems
- Documented remediation steps and tracked progress through Tenable's remediation workflow
- Re-ran **validation scans** post-remediation to confirm vulnerability closure

---

## 📊 Key Results

| Metric | Result |
|--------|--------|
| Vulnerabilities identified | 100+ across Windows & Linux assets |
| Critical/High reduction | ~80% within scan cycle |
| Scan type | Credentialed (authenticated) |
| Validation | Post-remediation re-scan confirmed closure |

---

## 🧠 Skills Demonstrated

- `Tenable Vulnerability Management` — scan policy creation, asset management, dashboards
- `CVSS & VPR scoring` — risk-based prioritization
- `Windows Server & Linux hardening` — patch management, service configuration
- `Microsoft Azure` — VM management, network scoping
- `Reporting` — technical and executive-level remediation documentation

---

## 📁 Repo Structure

```
vulnerability-management-program/
├── README.md
├── scan-policies/
│   └── credentialed-scan-policy-notes.md
├── remediation/
│   └── remediation-tracker-template.md
└── reports/
    └── sample-executive-summary.md
```

---

## 🔗 Related Projects

- [SIEM & Threat Detection with Microsoft Sentinel](../sentinel-siem-threat-detection)
- [EDR Incident Response with Microsoft Defender](# 🔍 EDR Investigation & Incident Response with Microsoft Defender
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
- [AI-Powered Security Tooling — LLM & Agentic AI](# 🤖 AI-Powered Security Tooling — LLM API & Agentic AI
> **Tools:** OpenAI API · Anthropic Claude API · Python · Microsoft Sentinel · Azure Functions  
> **Environment:** Cyber Range AI/Agentic AI Track  
> **Role:** AI Security Engineer / Developer

---

## 📋 Overview

Built AI-powered security tooling by integrating **Large Language Model (LLM) APIs** into a SOC workflow. Developed an **agentic AI pipeline** capable of autonomously analyzing security alerts, querying log sources, enriching findings, and generating structured incident reports — dramatically reducing analyst triage time.

---

## 🎯 Objectives

- Integrate LLM APIs (OpenAI / Claude) into the SOC for automated alert summarization
- Build an agentic AI assistant that queries Sentinel and returns plain-language threat summaries
- Apply prompt engineering best practices for security analysis tasks
- Understand and defend against LLM-specific attack surfaces (prompt injection)

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    AGENTIC AI PIPELINE                  │
│                                                         │
│  Sentinel Alert  →  [AI Agent]  →  Incident Report     │
│       │                │                                │
│       │         ┌──────┴──────┐                        │
│       │         │  Tool Use   │                        │
│       │         ├─────────────┤                        │
│       │         │ • Query KQL │                        │
│       │         │ • IP Lookup │                        │
│       │         │ • CVE Search│                        │
│       └────────►│ • Hash Check│                        │
│                 └─────────────┘                        │
└─────────────────────────────────────────────────────────┘
```

---

## 🔧 Technical Breakdown

### 1. LLM API Integration
- Used **OpenAI GPT-4** and **Anthropic Claude** APIs via Python to build an alert summarization tool
- Converts raw Sentinel JSON alerts into concise, human-readable incident briefs with structured output
- Engineered **system prompts** to constrain model behavior to security analysis and produce consistent JSON + narrative output

### 2. Agentic AI Pipeline
Multi-step autonomous workflow:
1. **Receive** — Ingest raw alert payload from Sentinel webhook
2. **Query** — Agent calls Log Analytics API to retrieve related events via KQL
3. **Enrich** — Perform IOC lookups (IP reputation, file hash checks, CVE details)
4. **Analyze** — LLM synthesizes all context into a threat assessment
5. **Report** — Generate structured draft incident report ready for analyst review

Implemented **function calling / tool use** to allow the agent to interact with external APIs autonomously without hardcoded logic.

### 3. LLM Security (Prompt Injection Defense)
- Studied and tested **prompt injection** scenarios where attacker-controlled log data attempts to hijack model behavior
- Implemented **input sanitization** to strip potential injection payloads from log content before passing to LLM
- Added **output validation** layer to enforce expected response schema and reject anomalous model outputs
- Applied **least-privilege prompt design** — system prompt explicitly limits model actions and scope

---

## 💻 Code Samples

**Alert Summarization — Core API Call**
```python
import anthropic

client = anthropic.Anthropic()

def summarize_alert(alert_json: dict) -> dict:
    system_prompt = """You are a SOC analyst assistant. Analyze the security alert 
    and return a JSON object with keys: severity, summary, affected_assets, 
    recommended_actions, and confidence_score. Be concise and factual."""
    
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=1024,
        system=system_prompt,
        messages=[{
            "role": "user",
            "content": f"Analyze this alert: {str(alert_json)}"
        }]
    )
    return response.content[0].text
```

**Agentic Tool Use — KQL Query Function**
```python
tools = [
    {
        "name": "run_kql_query",
        "description": "Query Microsoft Sentinel Log Analytics for related security events",
        "input_schema": {
            "type": "object",
            "properties": {
                "query": {"type": "string", "description": "KQL query to execute"},
                "timespan": {"type": "string", "description": "Time range e.g. PT1H, P1D"}
            },
            "required": ["query"]
        }
    },
    {
        "name": "lookup_ip_reputation",
        "description": "Check IP address reputation against threat intelligence feeds",
        "input_schema": {
            "type": "object",
            "properties": {
                "ip_address": {"type": "string"}
            },
            "required": ["ip_address"]
        }
    }
]
```

**Input Sanitization — Prompt Injection Defense**
```python
import re

INJECTION_PATTERNS = [
    r"ignore (previous|above|all) instructions",
    r"you are now",
    r"new system prompt",
    r"disregard your",
    r"act as"
]

def sanitize_log_input(log_data: str) -> str:
    for pattern in INJECTION_PATTERNS:
        log_data = re.sub(pattern, "[REDACTED]", log_data, flags=re.IGNORECASE)
    return log_data[:4000]  # Enforce max input length
```

---

## 📊 Key Results

| Metric | Result |
|--------|--------|
| Triage time reduction | 15 min → < 60 seconds per alert |
| API integrations | OpenAI GPT-4, Anthropic Claude, Sentinel Log Analytics |
| Agentic tools implemented | KQL query, IP reputation, CVE lookup, hash check |
| Security hardening | Prompt injection detection + output validation |

---

## 🧠 Skills Demonstrated

- `LLM API Integration` — OpenAI & Anthropic Claude APIs, chat completions, tool use
- `Prompt Engineering` — system prompts, structured output, role constraints
- `Agentic AI` — multi-step autonomous pipelines, function calling, tool orchestration
- `LLM Security` — prompt injection attack/defense, input sanitization, output validation
- `Python` — REST API calls, JSON processing, async workflows
- `Microsoft Sentinel` — webhook integration, KQL, Log Analytics API

---

## 📁 Repo Structure

```
llm-agentic-security-tooling/
├── README.md
├── src/
│   ├── alert_summarizer.py       # Core LLM summarization logic
│   ├── agentic_pipeline.py       # Multi-step agentic workflow
│   ├── tools/
│   │   ├── kql_runner.py         # Sentinel KQL query tool
│   │   ├── ip_lookup.py          # IP reputation enrichment
│   │   └── cve_search.py         # CVE detail lookup
│   └── security/
│       ├── input_sanitizer.py    # Prompt injection defense
│       └── output_validator.py   # Response schema validation
├── prompts/
│   └── system_prompts.md         # Engineered prompts library
├── tests/
│   └── injection_tests.py        # Adversarial prompt test cases
└── docs/
    └── architecture.md
```

---

## ⚠️ Security Considerations

| Risk | Mitigation |
|------|-----------|
| Prompt injection via log data | Input sanitization + pattern detection |
| Sensitive data in LLM context | PII stripping before API call |
| Model hallucination | Output schema validation + confidence scoring |
| API key exposure | Environment variables, never hardcoded |
| Excessive model autonomy | Human-in-the-loop review before any action |

---

## 🔗 Related Projects

- [Vulnerability Management Program](../vulnerability-management-program)
- [SIEM & Threat Detection with Microsoft Sentinel](../sentinel-siem-threat-detection)
- [EDR Incident Response with Microsoft Defender](../defender-edr-incident-response)

---

> 💼 *Completed as part of Josh Madakor's Cyber Range — AI/Agentic AI track. Demonstrates the integration of modern LLM capabilities with enterprise security operations.*)

---

> 💼 *Completed as part of Josh Madakor's Cyber Range — a live production SOC environment with real attacker activity and enterprise-grade security tools.*)
- [AI-Powered Security Tooling — LLM & Agentic AI](../llm-agentic-security-tooling)

---

> 💼 *Completed as part of Josh Madakor's Cyber Range — a live production SOC environment with enterprise-grade security tools.*)
- [SIEM & Threat Detection with Microsoft Sentinel](../sentinel-siem-threat-detection)
- [AI-Powered Security Tooling — LLM & Agentic AI](../llm-agentic-security-tooling)

---

> 💼 *Completed as part of Josh Madakor's Cyber Range — a live production SOC environment with real attacker activity and enterprise-grade security tools.*
