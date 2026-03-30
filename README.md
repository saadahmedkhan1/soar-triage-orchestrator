# 🛡️ Automated Threat Intelligence & Triage Orchestrator (SOAR)

## 📌 Overview
Security Operations Centers (SOC) lose countless hours to alert fatigue, manually copying and pasting suspicious IP addresses into various threat databases. 

To solve this, I engineered a custom **Security Orchestration, Automation, and Response (SOAR)** pipeline using n8n. This zero-touch orchestrator automatically ingests IP addresses, enriches them via global threat intelligence APIs, calculates a deterministic risk score, and uses AI to generate human-readable triage summaries—alerting the team *only* when a critical threat is confirmed.

[![Watch the Video Demonstration Here](https://img.youtube.com/vi/9Q3KuFhh50c/maxresdefault.jpg)](https://www.youtube.com/watch?v=9Q3KuFhh50c)

---

## 🏗️ Architecture & Data Flow

1. **Ingestion Queue:** A Google Sheet acts as the database. The pipeline polls for newly added IP addresses in batches.
2. **State Management (The Gatekeeper):** An ingress filter evaluates the database status. If an IP is already marked as "Scanned," it is immediately dropped to protect API rate limits and optimize compute resources.
3. **Parallel Enrichment:** Fresh IPs are processed concurrently through the **VirusTotal API** and **AbuseIPDB API** to gather malicious flags and confidence scores.
4. **Custom Risk Scoring Engine:** A custom JavaScript Code Node parses the nested JSON payloads from both APIs. It applies weighted logic to calculate a definitive Risk Score (0-100) and assigns a severity tier (Low, Medium, High).
5. **AI Threat Translation:** The raw telemetry is passed to OpenAI (`gpt-4o-mini`), acting as an automated Tier 1 Analyst to write a concise, actionable summary of the threat.
6. **Resolution & Routing:** * The Google Sheet is automatically updated with the Risk Score, Risk Level, AI Summary, and a "Scanned" status.
   * A final logic gate evaluates the Risk Level. **Low-risk IPs are silenced**, while **High-risk IPs trigger an automated webhook** to the SOC's Slack channel for immediate remediation.

---

## 🧠 Core Engineering Highlights

* **Batch & Array Processing:** Designed to handle arrays of multiple IPs simultaneously using `Run Once for Each Item` looping logic without cross-contaminating data.
* **Alert Fatigue Prevention:** Implemented logical gating to ensure Slack routing *only* triggers for High-Risk indicators, keeping SOC channels completely quiet for benign network traffic (like public DNS servers).
* **Data Parsing & Transformation:** Extracted deeply nested parameters from complex API JSON responses to build standardized, cross-platform alert templates.

---

## 🛠️ Technology Stack

* **Automation Engine:** n8n
* **Threat Intelligence:** VirusTotal API, AbuseIPDB API
* **Scripting:** Custom JavaScript (n8n Code Nodes)
* **AI/LLM:** OpenAI API
* **Integrations:** Google Sheets API, Slack OAuth

---

## 🚀 How to Use (Local Deployment)
To run this pipeline in your own n8n instance:
1. Create a Google Sheet with the headers: `IP Address` | `Status` | `Risk Score` | `Risk Level` | `AI Summary`.
2. Connect your credentials for Google Sheets, Slack, OpenAI, VirusTotal, and AbuseIPDB in n8n.
3. Replicate the node flow as seen in the architecture and apply the custom JavaScript scoring logic.
4. Add test IPs to the sheet and activate the workflow.

---

## 📬 Contact
[Let's Connect!](https://shorturl.at/ourBN)