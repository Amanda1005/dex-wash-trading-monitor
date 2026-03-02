# DEX Wash Trading Monitor 🔍

Automated monitoring system that fetches daily wash trading data from a Dune Analytics dashboard and sends an **email alert** when the wash trading rate exceeds **7%**.

> Part of the [DEX Wash Trading Detection & Risk Control Framework](https://dune.com/amandatw/dex-wash-trading-detection-and-risk-control-framework) project.

---

## Architecture

```
Dune Analytics (S5 Query)
        ↓  REST API (every day 09:00 UTC)
  monitor.py  ←  GitHub Actions (cron)
        ↓  if wash_trading_pct > 7%
  Email Alert (SMTP / Gmail)
```

---

## Features

| Feature | Detail |
|---|---|
| **Data source** | Dune Analytics API v1 |
| **Threshold** | 7% (configurable via `THRESHOLD` env var) |
| **Schedule** | Daily at 09:00 UTC via GitHub Actions |
| **Alert channel** | Email (Gmail SMTP) |
| **Manual trigger** | GitHub Actions → Run workflow |

---

## Setup

### 1. Fork / Clone this repo

```bash
git clone https://github.com/YOUR_USERNAME/dex-wash-trading-monitor.git
cd dex-wash-trading-monitor
```

### 2. Get your Dune API Key

1. Login to [dune.com](https://dune.com) → Settings → API Keys
2. Create a new key and copy it

### 3. Get your Query ID

Open the S5 daily monitoring query in Dune → the URL will be:
`https://dune.com/queries/XXXXXXX` — copy `XXXXXXX`

### 4. Set up Gmail App Password

1. Enable 2-Step Verification on your Google account
2. Go to [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords)
3. Create an app password for "Mail" → copy the 16-character password

### 5. Add GitHub Secrets

Go to your repo → **Settings → Secrets and variables → Actions → New repository secret**

| Secret Name | Value |
|---|---|
| `DUNE_API_KEY` | Your Dune API key |
| `DUNE_QUERY_ID` | Your S5 query ID (numbers only) |
| `SMTP_USER` | Your Gmail address |
| `SMTP_PASSWORD` | Gmail App Password (16 chars) |
| `ALERT_TO` | Recipient email address |

### 6. Adjust column names (if needed)

In `monitor.py`, the `get_latest_wash_pct()` function expects these Dune column names:
- `block_date` — the date column
- `wash_trading_pct` — the percentage column

If your query uses different names, update lines 58–64 in `monitor.py`.

---

## Local Testing

```bash
# Install dependency
pip install requests

# Set env vars
export DUNE_API_KEY="your_key"
export DUNE_QUERY_ID="1234567"
export SMTP_USER="you@gmail.com"
export SMTP_PASSWORD="your_app_password"
export ALERT_TO="recipient@email.com"

# Run
python monitor.py
```

---

## Email Alert Example

When wash trading exceeds 7%, you'll receive:

```
Subject: 🚨 Wash Trading Alert – 9.43% detected on 2025-03-01

⚠️ Wash Trading Threshold Exceeded
Date:             2025-03-01
Wash Trading %:   9.43%
Threshold:        7.0%
Excess:           +2.43%

📊 View Full Dashboard
```

---

## Tech Stack

- **Python 3.11** — core scripting
- **requests** — Dune REST API calls
- **smtplib** — email delivery
- **GitHub Actions** — free cloud scheduling (cron)

---

## Related

- 📊 [Dune Dashboard](https://dune.com/amandatw/dex-wash-trading-detection-and-risk-control-framework)
- 🎯 Built for: Chainlink Data Risk Operations Analyst · Rain Data Scientist (Fraud Risk) · Binance Data Analytics Manager (Risk)