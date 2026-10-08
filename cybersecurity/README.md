# 🛡️ Cybersecurity AI Agents Suite (n8n Workflows)

An enterprise-ready collection of AI-augmented cybersecurity agent workflows built for **n8n**. These workflows automate critical security operations including external attack surface discovery, CI/CD pipeline auditing with automated remediation PRs, git secret scanning, and automated phishing triage.

---

## 📂 Cybersecurity Agents Directory

| Agent / Workflow | Directory | Documentation | Primary Integrations |
| :--- | :--- | :--- | :--- |
| **🛰️ Attack Surface Monitor (ASM)** | [`attack_surface_monitor/`](./attack_surface_monitor/) | [README](./attack_surface_monitor/README.md) | crt.sh, CertSpotter, Google DoH, Shodan, CISA KEV, Slack, Groq/OpenAI |
| **⚙️ CI/CD Pipeline Security Auditor v3** | [`cicd_pipeline_security/`](./cicd_pipeline_security/) | [README](./cicd_pipeline_security/README.md) | GitHub API, Groq LLM, Slack Interactive Approvals |
| **🔐 Leaked Secret & Credential Scanner** | [`leaked_secret_scanner/`](./leaked_secret_scanner/) | [README](./leaked_secret_scanner/README.md) | GitHub API, OpenAI LLM, Shannon Entropy, Slack Alerts |
| **🎣 Phishing Triage Agent** | [`phishing_tirage/`](./phishing_tirage/) | [README](./phishing_tirage/README.md) | Gmail, VirusTotal, URLScan.io, Groq LLM, Telegram, Google Sheets |

---

## 🧩 Architectural Highlights

### 1. 🛰️ [Attack Surface Monitor (ASM)](./attack_surface_monitor/README.md)
* **Continuous Reconnaissance:** Automated discovery of external infrastructure using Certificate Transparency, DNS-over-HTTPS, Shodan InternetDB, and CISA KEV.
* **Delta Alerting:** Establishes an asset baseline on first run and only notifies the security team upon perimeter changes or newly detected vulnerabilities.
* **Asset Inventory API:** Exposes an on-demand `GET /webhook/asm/inventory` endpoint to export the real-time asset inventory to SIEMs or CMDBs.

### 2. ⚙️ [CI/CD Pipeline Security Auditor v3](./cicd_pipeline_security/README.md)
* **GitHub Actions Static Analysis:** Identifies unpinned 3rd-party actions, overly broad token scopes, and script injection vulnerabilities.
* **Human-in-the-Loop Slack Decision:** Dispatches interactive Slack cards allowing engineers to trigger automated remediation PRs, open tracking issues, or accept risks.
* **Closed-Loop Verification:** Automatically re-audits the pipeline upon Pull Request merge to confirm the fix.

### 3. 🔐 [Leaked Secret & Credential Scanner](./leaked_secret_scanner/README.md)
* **Dual Tree & Git History Scan:** Inspects both the current working tree and the diffs of recent commits to uncover deleted secrets lingering in git history.
* **25+ Pattern & Entropy Engine:** Regex patterns for all major cloud providers plus Shannon entropy analysis for unknown high-entropy keys.
* **Zero Secret Leakage:** Sanitized previews and non-reversible fingerprints protect confidentiality. AI triage filters out test fixtures and placeholders.

### 4. 🎣 [Phishing Triage Agent](./phishing_tirage/README.md)
* **Intelligent Email Parsing:** Groq LLM parses incoming emails, extracting IOCs and analyzing emotional manipulation or urgency.
* **OSINT Verification:** Automatic verification via VirusTotal and URLScan.io sandbox results.
* **Automated SOC Routing:** Dispatches instant alerts via Telegram, logs incidents in Google Sheets, and opens ITSM tickets.

---

## 🚀 Quick Start & Import Guide

1. **Deploy n8n:** Run n8n locally, on Docker, or via n8n Cloud.
2. **Import Workflow:**
   - In n8n, navigate to **Workflows** → **Add Workflow** (`+`).
   - Click the top-right menu (`...`) and select **Import from File**.
   - Select the respective `.json` file from any of the subdirectories.
3. **Configure Environment & Credentials:**
   - Add your API credentials in n8n (GitHub, Slack, OpenAI, Groq, Telegram).
   - Set target variables such as repository names, domain scopes, and Slack channel IDs.
4. **Test & Activate:**
   - Run a test execution to verify connectivity.
   - Toggle the workflow switch to **Active**.

---

## 🔒 Security Principles

- **Authorization:** Only execute scans against repositories, infrastructure, and domain names you own or are authorized to assess.
- **Least Privilege:** Configure API tokens with minimal required scopes.
- **Data Privacy:** Raw secret values are never transmitted to LLM endpoints or logged in cleartext.
