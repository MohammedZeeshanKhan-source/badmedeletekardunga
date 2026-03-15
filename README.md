# AquaRisk (Hackathon Prototype)

AquaRisk is a simple demo app that helps you answer:

**“Which lake looks risky right now, why, and what should we do next?”**

It combines multiple signals (bloom, disease, groundwater, drainage, sewage, weather) into a single operational view, with an India-focused map + actions you can show to judges.

Open the app at: **http://127.0.0.1:5000**

---

## What you can show in a hackathon demo (3 minutes)

1) **Map → pick a lake**
    - The map is locked to India and shows risk at a glance.

2) **Detail panel → “Ops-ready” view**
    - Risk gauges + key signal cards.
    - **Confidence / data freshness** badges (so it feels real, not magic).

3) **Explainability → what drove the score**
    - Top contributing signals are shown in a short “Explainability” panel.

4) **Action Playbook + Sampling Plan**
    - The app suggests 3–5 actions and a simple sampling plan.

5) **Scenario Toggle (what-if)**
    - Change rainfall / sewage load sliders and see the operational estimate shift.

6) **Reports + Alerts**
    - Download an **Executive PDF** for any lake.
    - **Emergency PDF + Send Alerts** are gated for “Extreme” lakes.
    - Optional: subscribe a Telegram chat to get a 30‑minute digest.

---

## Run locally (Windows PowerShell)

From the repo root:

```powershell
cd aquarisk

# Create a virtual environment
py -3 -m venv .venv

# Install dependencies
.\.venv\Scripts\python -m pip install -U pip
.\.venv\Scripts\python -m pip install -r requirements.txt

# Start the server
.\.venv\Scripts\python app\server.py
```

Then open: **http://127.0.0.1:5000**

Notes:
- If `http://localhost:5000` doesn’t load on Windows, use `127.0.0.1`.
- If the port is “stale” (wrong endpoints), stop any Python/Flask on port 5000 and restart.

---

## Optional: enable AI + Telegram (server-side only)

The app is safe by default: there is **no client-side API key**.

### AI Assistant (Gemini)

Set an environment variable before starting the server:

```powershell
$env:GEMINI_API_KEY = "YOUR_KEY_HERE"
```

If it’s not set, the AI endpoint returns “not configured”.

### Telegram digest (every 30 minutes)

Set:

```powershell
$env:TELEGRAM_BOT_TOKEN = "YOUR_BOT_TOKEN_HERE"
```

In the lake detail panel, paste a **Chat ID** and click **Subscribe**.
(Tip: message `@userinfobot` in Telegram to find your Chat ID.)

---

## How it works (simple)

1) **Data** (demo CSVs) → bloom/disease/groundwater/drainage/sewage
2) **Models** (LightGBM) → bloom severity + disease risk
3) **Backend** (Flask) → `/api/*` endpoints (scores, timelines, summaries)
4) **Frontend** (HTML/JS) → India map + charts + ops panels

The main “combined risk” you see is a 0–1 score. The “operational” view also blends sewage/drainage/groundwater to feel closer to real field constraints.

---

## API endpoints you might care about

- `GET /api/lakes` — all lake risk objects (what the dashboard loads)
- `GET /api/timeline/<lake_id>` — history + 15‑day outlook (for charts)
- `GET /api/summary` — national digest (counts + top hotspots)
- `GET /api/weekly_summary` — weekly payload (used for reports/digest)
- `POST /api/ai` — AI proxy (requires `GEMINI_API_KEY`)

Telegram:

- `GET /api/telegram/status`
- `POST /api/telegram/subscribe` `{ "chat_id": "..." }`
- `POST /api/telegram/unsubscribe` `{ "chat_id": "..." }`
- `POST /api/telegram/send_now` (demo trigger)

---

## Project map (where to change things)

```
aquarisk/
  app/server.py            # Flask API + risk calculations + Telegram loop
  data/                    # Demo CSVs + lake metadata
  models/                  # Training + model pickle files
  frontend/index.html      # Main UI
  frontend/app.js          # Dashboard logic
  frontend/style.css       # Styling
```

---

## Disclaimer

This is a hackathon prototype. The default data is demo/synthetic and the outputs should not be used for real policy decisions without validation.
