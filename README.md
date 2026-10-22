# Wellness Companion

The first version of my wellness companion: a chat coach that listens, tracks how you feel and suggests routines. It runs on FastAPI, with replies written by an LLM on Groq, and falls back to rule-based replies when no API key is set.

> **Continued in [Ai-Agent-Final](https://github.com/baerozgaarmard/Ai-Agent-Final)**, which adds learning and safety-first crisis detection. Live demo: https://ai-agent-final-reet.onrender.com

## How it works

Each message goes through the `Orchestrator` in `wellness_agent/`:

| Module | Job |
|---|---|
| `state_machine.py` | Moves the conversation between stages (rapport, free talk, deeper questions) based on emotion and topic signals |
| `emotion_engine.py` | Reads the emotion behind a message |
| `memory.py` | Remembers what you've shared across the session |
| `conversation_planner.py`, `question_planner.py` | Decide what to say next and which question to ask, open or multiple choice |
| `root_cause.py` | Traces likely causes behind sleep, stress, relationship, exercise and work problems |
| `routine_generator.py` | Builds a routine of small actions for a cause or goal |
| `reports.py` | Builds reports with metrics, trends, observations and goals |
| `llm_service.py` | Turns the planned reply into natural language with Groq |
| `synthetic_data.py` | Generates sample conversations for testing |

## Run it locally

```bash
pip install -r requirements.txt
cp .env.example .env    # add GROQ_API_KEY, or leave it out to use rule-based replies
python app.py           # web chat at http://localhost:8000
python main.py          # chat in the terminal
```

In the terminal, type `/help` to see commands such as `/summary`, `/memory`, `/insight`, `/routine` and `/report`.

Or run it with Docker:

```bash
docker build -t wellness-companion .
docker run -p 8000:8000 -e GROQ_API_KEY=your_key wellness-companion
```

## API

| Route | What it does |
|---|---|
| `GET /` | Sign-in page (just your name) |
| `GET /chat`, `POST /chat` | Chat page and message endpoint |
| `GET /summary/{session_id}` | Session summary |
| `GET /memory/{session_id}` | What the coach remembers |
| `GET /insight/{session_id}` | Insights from the conversation |
| `GET /routine/{session_id}` | Suggested routine |
| `POST /report/{session_id}` | Full report |
| `POST /reset/{session_id}` | Start over |
| `GET /health` | Health check |

> This is a demo project, not a medical or crisis service. If you are in crisis, contact your local emergency number.
