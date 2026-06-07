# 📈 Mad Money AI Automation

> **Automatically analyze Jim Cramer's Mad Money every morning — powered by AI**

![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-orange?style=for-the-badge&logo=n8n)
![Gemini](https://img.shields.io/badge/Google%20Gemini-AI%20Analysis-blue?style=for-the-badge&logo=google)
![Gmail](https://img.shields.io/badge/Gmail-Email%20Delivery-red?style=for-the-badge&logo=gmail)
![Render](https://img.shields.io/badge/Render-Cloud%20Hosted-green?style=for-the-badge)
![Free](https://img.shields.io/badge/Cost-100%25%20Free-brightgreen?style=for-the-badge)

---

## 🎯 What This Does

Every day at **5 AM**, this automation:

1. 🎙️ **Fetches** the latest Mad Money podcast episode via RSS
2. ⬇️ **Downloads** the full audio episode (~43MB MP3)
3. 🤖 **Sends it to Google Gemini AI** for transcription and analysis
4. 📊 **Applies a custom financial analysis prompt** to extract stock picks
5. 📧 **Emails you a formatted report** with every stock mentioned

**Zero manual effort. Runs 24/7 in the cloud. Completely free.**

---

## 📬 Sample Output

Every morning you receive an email like this:

```
📈 Mad Money Analysis — Friday, June 6, 2026

### Top Recommendations and 'Buys'
• CrowdStrike (CRWD) — BUY
  Rationale: Strong guidance + 4-for-1 stock split announced.
  AI tailwinds creating more demand for cybersecurity.

• Timken (TKR) — BUY
  Rationale: 55% earnings growth projected through 2028.
  Reinventing itself into mission-critical applications.

### Speculative or 'Field Bets'
• Quantum Computing IPO — SPECULATIVE
  Rationale: "Pure rank speculation" — Cramer's words.

### Cautionary Notes or 'Sells'
• Occidental Petroleum (OXY) — SELL IF WAR ENDS
  Rationale: Stock propped up by geopolitical conflict.

• Dollar Tree (DLTR) — CAUTION
  Rationale: Lower-income shoppers not returning as expected.
```

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    WORKFLOW PIPELINE                         │
│                                                             │
│  ⏰ Schedule      →  📡 Fetch RSS   →  🔍 Extract URL      │
│     (5 AM)            (Simplecast)       (Code node)        │
│                                              ↓              │
│  📧 Gmail Send   ←  🎨 Format HTML  ←  🤖 Gemini AI        │
│     (Your inbox)     (Code node)        (Audio analysis)    │
│                                              ↑              │
│                       ⏳ Wait 30s   ←  ⬇️ Download MP3     │
└─────────────────────────────────────────────────────────────┘
```

### Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Workflow Engine | n8n (self-hosted) | Orchestrates all automation steps |
| AI Analysis | Google Gemini 2.0 Flash | Transcribes + analyses audio |
| Data Source | Mad Money Podcast RSS | Daily episode feed |
| Notifications | Gmail OAuth2 | Sends formatted email report |
| Cloud Hosting | Render.com (free tier) | Runs 24/7 without your PC |
| Database | PostgreSQL (Render free) | Persists workflow data |
| Uptime Monitor | UptimeRobot (free) | Keeps server awake |

---

## 🚀 Quick Start

### Prerequisites
- Windows / Mac / Linux PC
- Gmail account
- Google account (for Gemini AI)
- Free accounts at: render.com, uptimerobot.com

### 1. Clone this repository
```bash
git clone https://github.com/YOUR_USERNAME/mad-money-automation.git
cd mad-money-automation
```

### 2. Install n8n locally (for testing)
```bash
# Install Node.js from nodejs.org first, then:
npm install -g n8n

# Start n8n
n8n
```
Open http://localhost:5678 in your browser.

### 3. Import the workflow
- In n8n, click **⋯ menu → Import from file**
- Select `workflow/mad-money-workflow.json`

### 4. Set up credentials
You need two credentials in n8n:
- **Google Gemini API** — get free key at [aistudio.google.com](https://aistudio.google.com)
- **Gmail OAuth2** — set up via [Google Cloud Console](https://console.cloud.google.com)

### 5. Deploy to cloud (Render.com)
See the [full deployment guide](docs/INSTALLATION.md) for step-by-step Render setup.

---

## 📋 Key URLs & Configuration

```
Mad Money RSS Feed:    https://feeds.simplecast.com/TkQfZXMD
Gemini Model:          models/gemini-2.0-flash
Schedule:              5 AM Europe/Berlin (configurable)
Workflow Trigger:      Schedule Trigger → automatic daily
```

### Environment Variables (for Render deployment)

```env
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=your-password
N8N_HOST=0.0.0.0
N8N_PORT=10000
N8N_PROTOCOL=https
N8N_EDITOR_BASE_URL=https://your-app.onrender.com
WEBHOOK_URL=https://your-app.onrender.com
NODE_TLS_REJECT_UNAUTHORIZED=0
DB_TYPE=postgresdb
DB_POSTGRESDB_URL=your-postgresql-internal-url
```

---

## 🤖 The AI Analysis Prompt

The core of this project is a carefully crafted financial analysis prompt:

```
Act as an expert financial analyst. Analyze the provided audio from 
Jim Cramer's Mad Money. Your task is to identify every stock mentioned 
and provide a comprehensive breakdown including:

1. Stock Name and Ticker
2. Recommendation: Buy / Sell / Take Profits / Speculative
3. Rationale: Specific reasons cited in the episode

Organize into categories:
* Top Recommendations and 'Buys'
* Speculative or 'Field Bets'  
* Industry-Specific Groups (AI, Defense, etc.)
* Cautionary Notes or 'Sells'

Use bold text for key takeaways.
```

---

## 🔧 Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| `SELF_SIGNED_CERT_IN_CHAIN` | Corporate network firewall | `npm config set strict-ssl false` |
| `Invalid URL (HTTPS_PROXY)` | Empty proxy variable | Run `set HTTPS_PROXY=` then `set HTTP_PROXY=` |
| `Too many requests (429)` | Gemini daily quota exceeded | Wait until midnight (resets daily) |
| `redirect_uri_mismatch` | Google Cloud URL mismatch | Match OAuth URL exactly to what n8n shows |
| Workflow lost on Render | No database connected | Add `DB_TYPE` and `DB_POSTGRESDB_URL` env vars |
| Workflow misses 5 AM | Render server sleeping | Set up UptimeRobot to ping every 5 minutes |

---

## 📊 Gemini API Free Tier Limits

| Model | Daily (RPD) | Per Minute (RPM) |
|-------|-------------|------------------|
| gemini-2.0-flash | 200 | 15 |
| gemini-2.5-flash | 20 | 5 |

> ✅ This workflow uses **1 request per day** — well within free limits!

---

## 🗺️ Roadmap

- [x] RSS feed fetching
- [x] Audio download
- [x] Gemini AI transcription + analysis
- [x] Gmail email delivery
- [x] Cloud deployment (Render)
- [x] Persistent database (PostgreSQL)
- [ ] Telegram notifications
- [ ] Google Sheets tracking (historical picks)
- [ ] Web dashboard for stock pick trends
- [ ] Sentiment scoring system
- [ ] Support for additional financial podcasts

---

## 📁 Project Structure

```
mad-money-automation/
│
├── workflow/
│   └── mad-money-workflow.json     ← Import this into n8n
│
├── docs/
│   ├── INSTALLATION.md             ← Full setup guide
│   ├── TROUBLESHOOTING.md          ← Common errors & fixes
│   └── Mad_Money_Automation_Guide.docx  ← Complete reference
│
├── prompts/
│   └── analysis-prompt.txt         ← The Gemini AI prompt
│
└── README.md                       ← You are here
```

---

## 💡 What I Learned Building This

This project was built as a **learning journey** into automation and AI:

- **n8n** — visual workflow automation with 500+ integrations
- **REST APIs** — fetching and parsing RSS/XML data
- **Google Gemini AI** — audio transcription and financial analysis
- **OAuth2** — secure Gmail authentication flow
- **Cloud deployment** — hosting on Render with PostgreSQL
- **Corporate network challenges** — SSL certificates and proxy issues
- **JavaScript** — code nodes for data transformation

> *Started with zero knowledge of n8n and built a fully working AI automation in one day!*

---

## ⚠️ Disclaimer

This project is for **educational and personal use only**. The stock analysis generated by AI is based on Jim Cramer's commentary and should **not** be taken as financial advice. Always do your own research before making investment decisions.

---

## 📄 License

MIT License — feel free to use, modify, and share!

---

## 🙋 About

Built by **Vamsi Krishna** as a personal learning project to explore AI automation and workflow orchestration.

📍 Stuttgart, Germany  
🔗 [LinkedIn](https://linkedin.com/in/YOUR_PROFILE)  
⭐ If this helped you, please give it a star!

---

*Made with ❤️ using n8n + Google Gemini AI*
