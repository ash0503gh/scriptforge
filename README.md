# ScriptForge

Generate video scripts in your unique voice — plain HTML/CSS/JS frontend, Python FastAPI backend.

---

## Stack

| Layer     | Tech                          |
|-----------|-------------------------------|
| Frontend  | Plain HTML + CSS + JS         |
| Backend   | Python 3.11 + FastAPI + Uvicorn |
| LLM       | Google Gemini (`gemini-2.5-flash` via `google-genai`) |
| Transcripts | youtube-transcript-api      |
| Hosting   | Render                        |

---

## Project Structure

```
scriptforge/
├── main.py            FastAPI app — all routes + Gemini LLM calls
├── youtube.py         Channel scraping + transcript fetching
├── requirements.txt
├── index.html         Single-page app (all pages in one file)
├── style.css          Full design system
├── app.js             All page logic + SSE client
├── config.js          Runtime frontend config
├── render.yaml        Render deployment blueprint
├── .gitignore
└── README.md
```

---

## Local Development

### Requirements
- Python 3.10+
- A Google Gemini API key → https://aistudio.google.com/app/apikey

### Run Locally

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

# Set your Gemini API key:
export GEMINI_API_KEY="your-gemini-api-key"

uvicorn main:app --reload --port 8000
# Running at http://localhost:8000 (serves both API and frontend)
```

Open http://localhost:8000 in your browser.

---

## Deploy to Render

### Single Web Service (Recommended)

1. Go to https://render.com → **New** → **Web Service**
2. Connect your GitHub repository (`scriptforge`)
3. Fill in:

| Field             | Value                                    |
|-------------------|------------------------------------------|
| Name              | `scriptforge`                            |
| Runtime           | `Python 3`                               |
| Build Command     | `pip install -r requirements.txt`        |
| Start Command     | `uvicorn main:app --host 0.0.0.0 --port $PORT` |

4. Under **Environment Variables**, add:

| Key                  | Value                          |
|----------------------|--------------------------------|
| `GEMINI_API_KEY`     | `AIzaSy...your Gemini key`     |
| `YOUTUBE_API_KEY`    | *(optional: YouTube Data v3)*  |
| `SCRAPER_API_KEY`    | *(optional: proxy service)*    |
| `GEMINI_MODEL`       | `gemini-2.5-flash` (default)   |

5. Click **Deploy**. Render will build and host both the backend API and the frontend from the same service.

---

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `GEMINI_API_KEY` | Yes | Google Gemini API key (supports `GOOGLE_API_KEY` or `ANTHROPIC_API_KEY` fallback) |
| `GEMINI_MODEL` | No | Model to use (defaults to `gemini-2.5-flash`, can also use `gemini-2.5-pro`) |
| `YOUTUBE_API_KEY` | Optional | YouTube Data API v3 key |
| `SCRAPER_API_KEY` | Optional | ScraperAPI key for transcript proxies |

---

## Notes

**Cold starts** — Render free tier spins down after inactivity. First request after sleep can take 30–60 s.

**Transcripts** — If a channel has captions disabled on recent videos, ScriptForge displays a helpful notice.

**Model & Search Grounding** — Uses Google Gemini with native Google Search Grounding to research live facts before generating scripts in the creator's voice.

