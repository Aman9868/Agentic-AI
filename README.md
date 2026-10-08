# 🤖 Agentic-AI: Autonomous AI Agents & Workflows

A repository of production-ready, autonomous AI agents and n8n workflows for cybersecurity, e-commerce, and productivity automation.

---

## 📂 Agent Directory

### 🛡️ [Cybersecurity AI Agents Suite](./cybersecurity/)
Autonomous agents and workflows designed to defend infrastructure, codebases, and communications:

- **[Attack Surface Monitor (ASM)](./cybersecurity/README.md#1--attack-surface-monitor-asm)**: Continuous external asset enumeration using Certificate Transparency, DNS-over-HTTPS, Shodan InternetDB, and CISA KEV with automated Slack briefings.
- **[CI/CD Pipeline Security Auditor v3](./cybersecurity/README.md#2--cicd-pipeline-security-auditor-v3)**: Static analysis of GitHub Actions workflows, risk scoring, and interactive human-in-the-loop Slack approval to dispatch automated remediation PRs.
- **[Leaked Secret Scanner](./cybersecurity/README.md#3--leaked-secret-scanner)**: Deep git repository and commit diff scanner with 25+ secret patterns, Shannon entropy detection, AI triage, and Slack incident reporting.
- **[Phishing Triage Agent](./cybersecurity/phishing_tirage/)**: Autonomous email ingestion, Groq LLM IOC extraction, VirusTotal/URLScan verification, and multi-channel alerting.

### 🛒 [E-Commerce Chat & Support Agent](./ecommerce_chat_agent/)
Conversational AI shopping assistant powered by Groq LLMs. Integrates with webhooks to manage customer inquiries, search product catalogs, and automate cart operations.

### 💼 [Job Search & Auto-Apply Agent](./job_search_agent/)
Autonomous career agent that continuously scrapes opportunities, matches candidate profiles against openings, auto-applies via email, and delivers real-time WhatsApp updates.

---

## 🚀 Quick Setup & Import

1. **Install n8n**: Run n8n locally via Docker, npm, or cloud hosting.
2. **Import Workflow**:
   - In your n8n workspace, navigate to **Workflows** → **Import from File**.
   - Select any `.json` workflow file from the respective directory.
3. **Configure Credentials**: Set up required API credentials (e.g. GitHub, Slack, OpenAI, Groq, Telegram) in n8n.
4. **Activate**: Toggle the workflow to active and test with a sample payload.

---

## 🔒 Security & Best Practices

All workflows in this repository are designed with security in mind:
- Raw secret values are masked and never transmitted to LLM endpoints.
- High-impact remediation actions (such as automated fix PRs and public disclosures) require explicit human-in-the-loop approval.
- Always ensure you have authorization before performing external attack surface enumeration or repository scanning.
