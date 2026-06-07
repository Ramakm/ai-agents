# LangGraph Projects Collection

Five original, progressively complex LangGraph projects — each built around a different real-world idea and a new set of graph concepts.

**Stack:** Python · LangGraph · Groq (free LLM API) · Tavily (free search API)

---

## Project Overview

| # | Project | What it does | New concept | LLM? |
|---|---------|-------------|-------------|------|
| 01 | **Mood Detector** | Classifies text mood and replies | State, Nodes, Sequential Edges | No |
| 02 | **Quiz Bot** | Interactive MCQ quiz on any topic | Conditional Edges, Loops | Yes |
| 03 | **Code Reviewer** | Finds bugs + suggests fixes + scores your code | 4-node pipeline, LLM per node | Yes |
| 04 | **Travel Planner** | Builds a complete trip plan from scratch | Multi-agent collaboration | Yes |
| 05 | **News Analyst** | Fetches, summarises, and analyses live news | Supervisor pattern, Tavily search | Yes |

---

## Folder Structure

```
langgraph/
├── README.md               ← you are here
├── .env.example            ← copy this to .env and fill in your keys
├── requirements.txt        ← all Python dependencies
│
├── 01-simple-graph/
│   └── mood_detector.py    ← no LLM, pure graph logic
│
├── 02-quiz-bot/
│   └── quiz_bot.py         ← interactive CLI quiz, looping graph
│
├── 03-code-reviewer/
│   └── code_reviewer.py    ← 4-node sequential pipeline
│
├── 04-travel-planner/
│   └── travel_planner.py   ← 3 specialised agents + compiler
│
└── 05-news-analyst/
    └── news_analyst.py     ← supervisor routing + Tavily live search
```

---

## Setup (one-time)

### 1. Go to the project folder

```bash
cd ~/ai-agents/langgraph
```

### 2. Create and activate a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate        # macOS / Linux
# .venv\Scripts\activate         # Windows
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Set up your API keys

```bash
cp .env.example .env
```

Then open `.env` and fill in the keys (see the API Keys section below).

---

## Running Each Project

Every project is a single Python script — just `cd` into the folder and run it.

```bash
# 01 — No LLM needed, runs immediately
cd 01-simple-graph
python mood_detector.py

# 02 — Interactive quiz (you answer in the terminal)
cd 02-quiz-bot
python quiz_bot.py

# 03 — Paste/edit your code inside the script, then run
cd 03-code-reviewer
python code_reviewer.py

# 04 — Enter destination, days, and budget when prompted
cd 04-travel-planner
python travel_planner.py

# 05 — Enter a news topic; fetches live results via Tavily
cd 05-news-analyst
python news_analyst.py
```

---

## API Keys

### Groq — free LLM API (required for 02, 03, 04, 05)

1. Sign up at **https://console.groq.com/**
2. Go to **API Keys** → **Create API Key**
3. Add to `.env`:
   ```
   GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
   ```

Groq provides free access to Llama 3 models with generous rate limits.  
Model used in all projects: `llama3-8b-8192`

### Tavily — web search API (required for 05 only)

1. Sign up at **https://app.tavily.com/**
2. Copy your API key from the dashboard
3. Add to `.env`:
   ```
   TAVILY_API_KEY=tvly-xxxxxxxxxxxxxxxxxxxx
   ```

Tavily's free tier gives 1,000 searches/month — more than enough.

### LangSmith — tracing & debugging (optional, any project)

LangSmith lets you visualise graph runs, inspect every node's input/output, and debug failures — highly recommended while learning.

1. Sign up at **https://smith.langchain.com/**
2. Create a new project and copy your API key
3. Add to `.env`:
   ```
   LANGCHAIN_API_KEY=lsv2_xxxxxxxxxxxxxxxxxxxx
   LANGCHAIN_TRACING_V2=true
   LANGCHAIN_PROJECT=LangGraph-Projects
   ```

Once set, every graph run is automatically traced. Visit smith.langchain.com to explore.

---

## LangGraph Concepts — Quick Reference

| Concept | Where it's shown | What it means |
|---------|-----------------|---------------|
| `TypedDict` State | All projects | The shared data object that flows through the graph |
| Node | All projects | A Python function that reads state and returns a partial update |
| Sequential edge (`add_edge`) | 01, 03, 04 | Fixed path: node A always goes to node B |
| Conditional edge (`add_conditional_edges`) | 02, 05 | Node A routes to B or C based on a function |
| Loop | 02 (quiz-bot) | A conditional edge that can route back to an earlier node |
| Multi-agent pipeline | 04 (travel-planner) | Multiple specialised nodes each doing one job |
| Supervisor pattern | 05 (news-analyst) | One "manager" node decides which worker runs next |
| `add_messages` reducer | 02 (quiz-bot) | Appends to a list instead of overwriting it |

---

## Project Details

### 01 — Mood Detector

No external API. Demonstrates that a LangGraph graph is just Python functions connected by edges.

**Flow:** `detect_mood` → `craft_response` → `format_output`

Run it and you'll see each node print its step as the state travels through.

---

### 02 — Quiz Bot

Generates quiz questions on any topic you choose using Groq, then loops until you've answered 3 questions. Uses a conditional edge that either loops back for another question or exits to the score screen.

**Key idea — the loop:**
```
generate_question → get_answer ──(total < 3)──▶ generate_question
                               ──(total >= 3)─▶ show_result → END
```

Edit `MAX_QUESTIONS` at the top of the file to change the quiz length.

---

### 03 — Code Reviewer

Passes your code through 4 sequential LLM calls, each doing one specific job. Edit `CODE_TO_REVIEW` and `LANGUAGE` in the script to review your own code.

**Pipeline:** `analyze_bugs` → `suggest_improvements` → `score_quality` → `write_report`

---

### 04 — Travel Planner

Three specialised agents build a complete trip plan. Each agent gets the output of the previous one as context, so the itinerary aligns with the research and the budget.

**Agents:**
- `destination_researcher` — attractions, culture, transport, food
- `budget_planner` — daily breakdown per category
- `itinerary_writer` — day-by-day morning/afternoon/evening plan

---

### 05 — News Analyst

Demonstrates the **supervisor pattern**: a central node reads the current state and routes to whichever worker is needed next. Workers always return to the supervisor after finishing, so the supervisor always controls the flow.

**Routing logic:**
```
no raw_news  →  fetcher   →  supervisor
no summary   →  summarizer →  supervisor
no analysis  →  analyst   →  supervisor
all done     →  compile_report → END
```

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `ModuleNotFoundError: langgraph` | Run `pip install -r requirements.txt` with the venv activated |
| `AuthenticationError: GROQ_API_KEY` | Check `.env` exists and the key is correct |
| `TavilyError` | Check `TAVILY_API_KEY` in `.env`; project 05 falls back gracefully if search fails |
| `RateLimitError` | Groq free tier is generous but has per-minute limits — wait 30 s and retry |
| Quiz bot stuck | It uses `input()` — just type A, B, C, or D and press Enter |

---

## Next Steps

Once comfortable with these projects, explore:

- **Checkpointers / Memory** — persist graph state across sessions with `MemorySaver` or a database checkpointer
- **Human-in-the-loop** — use `interrupt()` to pause a graph and wait for human approval
- **Streaming** — use `graph.stream()` to get token-by-token output
- **LangGraph Studio** — visual debugger built into LangSmith
- **Parallel nodes** — `Send` API to fan out work across multiple nodes simultaneously
