# 🤖 AI-Powered Security Tooling — LLM API & Agentic AI
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

> 💼 *Completed as part of Josh Madakor's Cyber Range — AI/Agentic AI track. Demonstrates the integration of modern LLM capabilities with enterprise security operations.*
