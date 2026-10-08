# 🛡️ Cybersecurity AI Agents Suite (n8n Workflows)

An enterprise-ready collection of AI-augmented cybersecurity agent workflows built for **n8n**. These workflows automate critical security operations including external attack surface discovery, CI/CD pipeline auditing with automated remediation PRs, git secret scanning, and automated phishing triage.

---

## 📑 Table of Contents

- [Workflows Overview](#-workflows-overview)
- [1. Attack Surface Monitor (ASM)](#1--attack-surface-monitor-asm)
- [2. CI/CD Pipeline Security Auditor v3](#2--cicd-pipeline-security-auditor-v3)
- [3. Leaked Secret Scanner](#3--leaked-secret-scanner)
- [4. Phishing Triage Agent](#4--phishing-triage-agent)
- [🚀 Quick Start & Import Guide](#-quick-start--import-guide)
- [🔒 Security & Safe Operation Principles](#-security--safe-operation-principles)

---

## 🧩 Workflows Overview

| Workflow | File | Primary Triggers | AI Integration | Primary Integrations |
| :--- | :--- | :--- | :--- | :--- |
| **Attack Surface Monitor** | [`Attack Surface Monitor (ASM).json`](./Attack%20Surface%20Monitor%20(ASM).json) | Schedule (Daily 06:00), Webhook | OpenAI / Groq | crt.sh, CertSpotter, Google DoH, Shodan, CISA KEV, Slack |
| **CI/CD Pipeline Security Auditor** | [`CI CD Pipeline Security.json`](./CI%20CD%20Pipeline%20Security.json) | Webhook, Manual Trigger | Groq LLM Agent | GitHub API, Slack Interactive Actions |
| **Leaked Secret Scanner** | [`name Leaked Secret Scanner.json`](./name%20Leaked%20Secret%20Scanner.json) | Webhook (`POST /webhook/secret-scan`), Manual | OpenAI LLM | GitHub API, Slack Interactive Actions |
| **Phishing Triage Agent** | [`phishing_tirage/Phishing Triage.json`](./phishing_tirage/Phishing%20Triage.json) | Gmail Trigger, Webhook | Groq LLM | VirusTotal, URLScan.io, Telegram, Google Sheets |

---

## 1. 🛰️ Attack Surface Monitor (ASM)

**File:** [`Attack Surface Monitor (ASM).json`](./Attack%20Surface%20Monitor%20(ASM).json)

### Overview
The **Attack Surface Monitor (ASM)** automates external reconnaissance and continuous asset tracking across organization domains. It establishes a baseline asset inventory on the initial run, and alerts security teams only when changes, newly discovered subdomains, or high-risk findings occur.

### Key Capabilities
- **Multi-Source Subdomain Enumeration:** Queries Certificate Transparency logs in real time via `crt.sh` and `CertSpotter`.
- **Active DNS & Service Probing:** Performs DNS-over-HTTPS lookups via Google Public DNS and verifies live HTTPS responses.
- **Port & Exposure Intelligence:** Enriches discovered IP addresses and hosts using Shodan InternetDB (open ports, vulnerabilities, CPEs) without requiring a paid API key.
- **Vulnerability Correlation:** Cross-references active services against the CISA Known Exploited Vulnerabilities (KEV) catalog.
- **Email Security Posture:** Audits DNS records for SPF and DMARC enforcement.
- **AI Executive Briefing:** Synthesizes technical findings into a concise security briefing for Slack using LLMs (OpenAI/Groq).
- **Asset Inventory API:** Exposes an on-demand `GET /webhook/asm/inventory` endpoint to export the current inventory and open findings.

### Setup Instructions
1. **Scope Configuration:** Open the `Scope Config` node and configure your authorized root `domains`.
2. **LLM Credentials:** Attach an OpenAI or Groq credential to the `Briefing Model` node.
3. **Slack Alerts:** Connect your Slack bot credential to the `Send Slack Alert` node and configure the destination channel.
4. **Initial Baseline:** Execute the workflow manually once to generate the baseline state.
5. **Activate Schedule:** Enable the workflow schedule trigger (runs daily at 06:00 UTC).

---

## 2. ⚙️ CI/CD Pipeline Security Auditor v3

**File:** [`CI CD Pipeline Security.json`](./CI%20CD%20Pipeline%20Security.json)

### Overview
An automated pipeline security engineer that audits GitHub Actions workflows, identifies supply chain risks and insecure configurations, and generates automated remediation Pull Requests with mandatory human approval.

### Workflow Lifecycle
```
[Trigger / Webhook] 
       ↓
[Audit Workflow Files & Actions]
       ↓
[AI Risk Scoring & Remediation Suggestion]
       ↓
[Slack Interactive Approval Request (24h timeout)]
       ↓
   ┌───────────────────────┬────────────────────────┐
   │ Approve (Auto-Fix PR) │ Create Issue / Track   │ Accept Risk
   ↓                       ↓                        ↓
[Atomic Commit & PR]    [GitHub Issue Opened]    [State Saved]
       ↓
[Re-audit Webhook on PR Merge]
```

### Key Capabilities
- **GitHub Workflow Static Analysis:** Scans `.github/workflows/*.yml` for:
  - Untrusted code checkout and script injections (`pull_request_target`).
  - Overly permissive default `GITHUB_TOKEN` permissions.
  - Unpinned 3rd-party actions (flagging mutable tags vs. immutable commit SHAs).
  - Dangerous secrets exposure and artifact poisoning risks.
- **Human-in-the-Loop Slack Actions:** Sends audit scorecards to Slack with actionable interactive buttons:
  - **Create Fix PR:** Automatically generates the hardened YAML on a fresh branch and opens a Pull Request.
  - **Open Issue:** Logs a tracking issue on GitHub.
  - **Accept Risk:** Stores risk acceptance in persistent workflow static data.
- **Atomic & Safe Remediation:** Never commits directly to the default branch; creates isolated branches with single atomic commits.
- **Continuous Re-audit on Merge:** Listens for PR merge events to re-assess the repository and record updated posture scores.

### Required Credentials
- **GitHub Read Token:** For reading repository contents and workflows.
- **GitHub Write Token:** Personal Access Token (PAT) with `contents:write`, `pull_requests:write`, `workflows:write`, and `issues:write`.
- **Groq API Key:** Powers the automated remediation agent.
- **Slack Bot Token:** Scopes `chat:write` for interactive alert delivery.

---

## 3. 🔐 Leaked Secret Scanner

**File:** [`name Leaked Secret Scanner.json`](./name%20Leaked%20Secret%20Scanner.json)

### Overview
A deep git scanner that analyzes code repositories and recent git commit history diffs to locate accidentally committed API keys, tokens, certificates, and credentials before adversaries exploit them.

### Key Capabilities
- **Dual Scope Scanning:**
  - **Repository Tree:** Recursively inspects all text files while automatically skipping binaries, dependencies (`node_modules/`, `vendor/`), and package lockfiles.
  - **Git History Diffs:** Scans the diffs of the last *N* commits to catch secrets that were committed and subsequently deleted from the working tree.
- **Multi-Vector Detection Engine:**
  - **25+ Provider Regex Patterns:** Detects AWS access keys, GitHub tokens, Slack Webhooks, OpenAI API keys, Stripe secret keys, SSH private keys, and more.
  - **Shannon Entropy Analysis:** Identifies high-entropy generic tokens and secrets that do not match known regex prefixes.
  - **Sensitive Filenames:** Detects exposed configuration files (`.env`, `.tfstate`, `id_rsa`, `credentials.json`).
- **Privacy & Security Preserving:**
  - **Zero Raw Secret Exposure:** Raw secrets are **never** stored, logged, or sent to the LLM. Only masked previews (e.g. `sk-proj-****...`) and cryptographic SHA fingerprints are processed.
- **AI Triage Layer:** LLM distinguishes between real credentials, dummy/test values, and template placeholders to eliminate false-positive alert fatigue.
- **Interactive Remediation:** Dispatches a structured Slack alert with a one-click button to open an actionable GitHub remediation issue.

### Webhook API Usage
Trigger scans via HTTP:
```bash
curl -X POST https://your-n8n-instance/webhook/secret-scan \
  -H "x-api-key: YOUR_WEBHOOK_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "repo": "owner/repository-name",
    "branch": "main",
    "max_files": 250,
    "history_commits": 20
  }'
```

---

## 4. 🎣 Phishing Triage Agent

**Folder:** [`phishing_tirage/`](./phishing_tirage/) | **Documentation:** [`phishing_tirage/README.md`](./phishing_tirage/README.md)

An end-to-end automated email phishing analysis pipeline integrating Gmail, Groq LLM parsing, VirusTotal, and URLScan.io intelligence lookups, with notifications routed via Telegram and tracking via Google Sheets.

---

## 🚀 Quick Start & Import Guide

1. **Deploy n8n:** Run n8n locally, on Docker, or via n8n Cloud.
2. **Import Workflow:**
   - In n8n, navigate to **Workflows** → **Add Workflow** (`+`).
   - Click the top-right menu (`...`) and select **Import from File**.
   - Select the respective `.json` file from this repository.
3. **Configure Environment & Credentials:**
   - Go to **Credentials** in n8n and add your API keys (GitHub, Slack, OpenAI, Groq).
   - Update node configuration variables (such as target repository, domains, and Slack channel IDs).
4. **Test & Activate:**
   - Click **Execute workflow** to run a test execution.
   - Toggle the workflow switch to **Active**.

---

## 🔒 Security & Safe Operation Principles

- **Authorization:** Only execute scans against repositories, infrastructure, and domain names you own or have explicit authorization to assess.
- **Least Privilege:** Always configure API tokens with the minimum required scopes.
- **Secret Redaction:** Workflows are designed not to log or output raw credential values.
