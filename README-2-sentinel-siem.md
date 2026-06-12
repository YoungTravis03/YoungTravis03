# 📡 SIEM Build-Out & Threat Detection with Microsoft Sentinel
> **Tools:** Microsoft Sentinel · KQL · Log Analytics Workspace · Azure Monitor  
> **Environment:** Live Production SOC — Josh Madakor's Cyber Range  
> **Role:** Security Operations Engineer / Detection Engineer

---

## 📋 Overview

Designed, configured, and actively maintained a cloud-native SIEM using **Microsoft Sentinel** connected to a Log Analytics Workspace. Ingested security logs from multiple data sources, built custom detection rules using **KQL (Kusto Query Language)**, and created dashboards to visualize real attacker activity across a live enterprise environment.

---

## 🎯 Objectives

- Centralize log ingestion from endpoint, network, and cloud sources into Sentinel
- Develop custom KQL analytics rules to detect real attacker behavior
- Build analyst-ready dashboards for rapid triage and investigation
- Investigate and document generated incidents end-to-end

---

## 🔧 Technical Breakdown

### 1. Data Connectors & Log Sources
- Connected **Microsoft Defender for Endpoint**, Azure Activity logs, and Windows Security Event logs to Sentinel
- Configured diagnostic settings on Azure VMs to forward **Syslog** and **Windows Event logs** to the Log Analytics Workspace
- Enabled **Microsoft Threat Intelligence** connector for automated IOC matching

### 2. Detection Engineering (KQL)
- Wrote custom **Scheduled Analytic Rules** to detect:
  - Brute force login attempts (failed sign-ins threshold)
  - Anomalous account activity and off-hours access
  - Lateral movement indicators (RDP/SMB from unexpected sources)
  - Data staging and exfiltration patterns
- Tuned alert thresholds to reduce false positives while maintaining detection coverage

### 3. Dashboards & Workbooks
- Built interactive **Sentinel Workbooks** to track incident trends, alert volumes by source, and top attacker IPs
- Created a **geographic attack map** visualizing inbound malicious traffic by country of origin
- Designed an analyst triage dashboard surfacing open incidents by severity and age

---

## 🔍 Sample KQL Queries

**Brute Force Detection — Multiple Failed Logins**
```kql
SecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count() by Account, IpAddress, bin(TimeGenerated, 1h)
| where FailedAttempts > 10
| order by FailedAttempts desc
```

**Successful Login After Multiple Failures**
```kql
let failed = SecurityEvent
| where EventID == 4625
| summarize FailCount = count() by Account, IpAddress;
SecurityEvent
| where EventID == 4624
| join kind=inner failed on Account, IpAddress
| where FailCount > 5
| project TimeGenerated, Account, IpAddress, FailCount
```

**Anomalous Geographic Login**
```kql
SigninLogs
| where ResultType == 0
| summarize LoginCount = count() by UserPrincipalName, Location, bin(TimeGenerated, 1d)
| where LoginCount < 2
| order by TimeGenerated desc
```

---

## 📊 Key Results

| Metric | Result |
|--------|--------|
| Log sources connected | 4+ (Defender, Azure Activity, Windows Events, Syslog) |
| Custom detection rules | 8+ analytic rules created |
| Incidents triaged | Multiple real attacker events detected |
| Dashboard workbooks | 3 (Attack Map, Triage Dashboard, Trend Analysis) |

---

## 🧠 Skills Demonstrated

- `Microsoft Sentinel` — data connectors, analytic rules, incident management
- `KQL (Kusto Query Language)` — detection queries, hunting, aggregation
- `Log Analytics Workspace` — log ingestion, schema exploration
- `Azure Monitor` — diagnostic settings, VM log forwarding
- `Detection Engineering` — rule tuning, false positive reduction

---

## 📁 Repo Structure

```
sentinel-siem-threat-detection/
├── README.md
├── kql-queries/
│   ├── brute-force-detection.kql
│   ├── lateral-movement.kql
│   ├── data-exfiltration.kql
│   └── anomalous-logins.kql
├── analytic-rules/
│   └── rule-configurations.md
└── workbooks/
    └── attack-map-setup.md
```

---

## 🔗 Related Projects

- [Vulnerability Management Program](../vulnerability-management-program)
- [EDR Incident Response with Microsoft Defender](../defender-edr-incident-response)
- [AI-Powered Security Tooling — LLM & Agentic AI](../llm-agentic-security-tooling)

---

> 💼 *Completed as part of Josh Madakor's Cyber Range — a live production SOC with real attacker activity and enterprise-grade security tools.*
