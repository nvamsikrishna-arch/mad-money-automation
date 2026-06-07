# 🛠️ Complete Installation Guide

## Table of Contents
1. [Local Setup (Windows)](#local-setup-windows)
2. [Render Cloud Deployment](#render-cloud-deployment)
3. [Gmail OAuth Setup](#gmail-oauth-setup)
4. [Gemini API Setup](#gemini-api-setup)
5. [UptimeRobot Setup](#uptimerobot-setup)
6. [Importing the Workflow](#importing-the-workflow)

---

## Local Setup (Windows)

### Step 1 — Install Node.js
1. Go to [nodejs.org](https://nodejs.org) and download the **LTS version**
2. Run installer with all defaults
3. Verify: open Command Prompt and run:
```
node --version
```
Expected output: `v22.x.x`

### Step 2 — Fix Corporate Network SSL (if needed)
```
npm config set strict-ssl false
```

If you see `Invalid URL` or `HTTPS_PROXY` errors:
```
set HTTPS_PROXY=
set HTTP_PROXY=
set NODE_TLS_REJECT_UNAUTHORIZED=0
```

### Step 3 — Install n8n
```
npm install -g n8n
```
Takes 3-5 minutes.

### Step 4 — Start n8n
```
set NODE_TLS_REJECT_UNAUTHORIZED=0 && n8n
```
Open http://localhost:5678 in your browser and create an owner account.

### Step 5 — Auto-start n8n with Windows (Optional)
1. Press `Windows + R` → type `shell:startup` → Enter
2. Create file `start-n8n.bat` with:
```
@echo off
set NODE_TLS_REJECT_UNAUTHORIZED=0
start cmd /k n8n
```

---

## Render Cloud Deployment

### Step 1 — Create PostgreSQL Database FIRST
1. Go to [render.com](https://render.com) and sign up (free, no credit card)
2. Click **New +** → **PostgreSQL**
3. Name: `mad-money-db`, Region: Frankfurt, Instance: Free
4. Click **Create Database**
5. Copy the **Internal Database URL**

### Step 2 — Deploy n8n
1. Click **New +** → **Web Service**
2. Select **Existing Image** tab
3. Image URL: `docker.io/n8nio/n8n`
4. Name: `mad-money-n8n`
5. Region: Frankfurt (EU Central)
6. Instance Type: **Free**

### Step 3 — Add All Environment Variables
Add ALL of these before clicking Deploy:

```
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=your-chosen-password
N8N_HOST=0.0.0.0
N8N_PORT=10000
N8N_PROTOCOL=https
N8N_EDITOR_BASE_URL=https://your-app.onrender.com
WEBHOOK_URL=https://your-app.onrender.com
NODE_TLS_REJECT_UNAUTHORIZED=0
DB_TYPE=postgresdb
DB_POSTGRESDB_URL=your-postgresql-internal-url
```

> ⚠️ **Important:** Replace `your-app.onrender.com` with your actual Render URL after deployment.

### Step 4 — Click Deploy Web Service
Wait 3-5 minutes for deployment to complete.

---

## Gmail OAuth Setup

### Step 1 — Create Google Cloud Project
1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Click project dropdown → **New Project**
3. Name: `n8n Mad Money` → Create

### Step 2 — Enable Gmail API
1. Search for **Gmail API**
2. Click **Enable**

### Step 3 — Create OAuth Credentials
1. Go to **APIs & Services** → **Credentials**
2. Click **+ Create Credentials** → **OAuth Client ID**
3. Configure consent screen: External, App name: `n8n`
4. Add yourself as Test User under **Audience → Test Users**
5. Application type: **Web application**

### Step 4 — Add Authorized URLs

**Authorized JavaScript Origins:**
```
http://localhost:5678
https://your-app.onrender.com
```

**Authorized Redirect URIs:**
```
http://localhost:5678/rest/oauth2-credential/callback
https://your-app.onrender.com/rest/oauth2-credential/callback
```

> ⚠️ The redirect URI must EXACTLY match what n8n shows in the Gmail credential screen.

### Step 5 — Connect in n8n
1. Copy Client ID and Client Secret
2. In n8n → Gmail node → Create new credential
3. Paste Client ID and Client Secret
4. Click **Sign in with Google**
5. Select your account and Allow all permissions

---

## Gemini API Setup

1. Go to [aistudio.google.com](https://aistudio.google.com)
2. Sign in with your Google account
3. Click **Get API Key** → **Create API Key**
4. Copy the key (starts with `AIzaSy...`)
5. In n8n → Analyze Audio node → Create credential → **Google Gemini(PaLM) Api**
6. Paste your API key → Save

---

## UptimeRobot Setup

1. Go to [uptimerobot.com](https://uptimerobot.com) and create a free account
2. Click **Add New Monitor**
3. Monitor Type: **HTTP(s)**
4. Friendly Name: `Mad Money n8n`
5. URL: `https://your-app.onrender.com`
6. Monitoring Interval: **5 minutes**
7. Click **Create Monitor**

---

## Importing the Workflow

1. Open n8n (local or Render)
2. Click **+** → **New workflow**
3. Click **⋯ menu** (top right) → **Import from file**
4. Select `workflow/mad-money-workflow.json`
5. Reconnect credentials (Gemini + Gmail)
6. Click **Publish**

---

## Verification Checklist

```
✅ n8n running (local or Render)
✅ PostgreSQL database connected
✅ Workflow imported from JSON
✅ Gemini API credential added
✅ Gmail OAuth credential connected
✅ Workflow Published (= Active)
✅ UptimeRobot monitoring set up
✅ Test email received in inbox
```

You're done! 🎉
