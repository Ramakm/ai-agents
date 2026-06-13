# Multi-Agent Systems

Five original, progressively complex multi-agent projects — each teaching a new collaboration pattern on top of the last.

**Stack:** Python · LangGraph · Groq (free LLM API) · Tavily (optional, free search API)

---

## Project Overview

| # | Notebook | What it does | New concept | LLM? |
|---|----------|-------------|-------------|------|
| 01 | **Agent Basics** | Rule-based task agent that routes to calculator or weather tools | State, Nodes, Agent Loop | No |
| 02 | **Tool Calling Agent** | LLM picks the right tool from a natural language question | `@tool`, `bind_tools`, `ToolNode`, ReAct | Yes |
| 03 | **Two-Agent System** | Researcher gathers facts, Writer turns them into an article | Agent handoff, sequential pipeline | Yes |
| 04 | **Supervisor Pattern** | Supervisor dynamically routes work to Analyst, Summarizer, Fact-Checker | Dynamic routing, supervisor loop | Yes |
| 05 | **Collaborative Pipeline** | 5-agent pipeline with a quality gate and revision loop | Full production pattern, critic, iteration limit | Yes |

---

## Folder Structure

```
multi-agent-systems/
├── README.md               ← you are here
├── .env.example            ← copy to .env and fill in your keys
├── requirements.txt        ← all Python dependencies
│
├── 01-agent-basics/
│   └── agent_basics.ipynb          ← no LLM, pure graph logic
│
├── 02-tool-calling-agent/
│   └── tool_calling_agent.ipynb    ← LLM decides which tool to use
│
├── 03-two-agent-system/
│   └── two_agent_system.ipynb      ← researcher + writer pipeline
│
├── 04-supervisor-pattern/
│   └── supervisor_pattern.ipynb    ← supervisor routes 3 workers
│
└── 05-collaborative-pipeline/
    └── collaborative_pipeline.ipynb ← 5-agent system with quality gate
```

---

## Setup (one-time)

### 1. Go to the project folder

```bash
cd ~/ai-agents/multi-agent-systems
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

Then open `.env` and fill in the keys (see API Keys section below).

### 5. Launch Jupyter

```bash
jupyter notebook
# or
jupyter lab
```

---

## Running Each Notebook

Open each notebook and run cells **top to bottom**.

| Notebook | Key requirement | Run time |
|----------|----------------|----------|
| 01-agent-basics | None — no API key | ~5 sec |
| 02-tool-calling-agent | `GROQ_API_KEY` | ~10 sec |
| 03-two-agent-system | `GROQ_API_KEY` | ~20 sec |
| 04-supervisor-pattern | `GROQ_API_KEY` | ~30 sec |
| 05-collaborative-pipeline | `GROQ_API_KEY` (+ optional Tavily) | ~60 sec |

---

## API Keys

### Groq — free LLM API (required for notebooks 02–05)

1. Sign up at **https://console.groq.com/**
2. Go to **API Keys** → **Create API Key**
3. Add to `.env`:
   ```
   GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
   ```

Groq provides free access to Llama 3 with generous rate limits.  
Model used: `llama3-8b-8192`

### Tavily — web search API (optional, notebook 05 only)

Without Tavily, notebook 05 falls back to the LLM's training knowledge — it still works, just not with live web results.

1. Sign up at **https://app.tavily.com/**
2. Copy your API key from the dashboard
3. Add to `.env`:
   ```
   TAVILY_API_KEY=tvly-xxxxxxxxxxxxxxxxxxxx
   ```

### LangSmith — tracing & debugging (optional, any notebook)

Lets you visualise every agent step, inspect state changes, and debug routing decisions.

1. Sign up at **https://smith.langchain.com/**
2. Add to `.env`:
   ```
   LANGCHAIN_API_KEY=lsv2_xxxxxxxxxxxxxxxxxxxx
   LANGCHAIN_TRACING_V2=true
   LANGCHAIN_PROJECT=Multi-Agent-Systems
   ```

---

## Multi-Agent Patterns — Quick Reference

| Pattern | Where shown | When to use |
|---------|------------|-------------|
| **Agent loop** | 01, 02 | Keep acting until a goal is met |
| **ReAct** (Reason + Act) | 02 | LLM decides which tool to call based on the question |
| **Sequential pipeline** | 03 | Steps always run in the same order |
| **State handoff** | 03, 04, 05 | Agent B reads Agent A's output from shared state |
| **Supervisor routing** | 04 | One manager decides which worker runs next |
| **Quality gate** | 05 | A critic scores output and triggers revision if too low |
| **Revision loop** | 05 | Writer loops until quality threshold is met |

---

## Notebook Details

### 01 — Agent Basics *(no API key)*

Demonstrates that a multi-agent system is just Python functions connected by edges — no LLM required.

A **Task Agent** processes a list of tasks, routing each one to the right tool:

**Flow:**
```
START → pick_next_task
              │
    ┌─────────┴──────────┐
(calculate)          (weather)
    │                    │
run_calculator      run_weather
    │                    │
    └────────┬───────────┘
             ▼
       pick_next_task  (loop until empty)
             │
           done
             │
            END
```

The loop repeats until all tasks are processed. Edit the task list in the final cell to try your own inputs.

---

### 02 — Tool Calling Agent *(Groq required)*

Replaces hand-written routing rules with an **LLM** that reads the question and chooses the right tool.

**Three tools available:**
- `calculator` — evaluates math expressions
- `word_counter` — counts words, characters, and sentences
- `unit_converter` — converts km/miles, kg/pounds, °C/°F, liters/gallons

**ReAct loop:**
```
Question → LLM reasons → calls tool → sees result → LLM answers
                ↑                                        │
                └─────────── (if tool call made) ────────┘
```

The LLM automatically chains tools when multiple steps are needed (e.g. "5 miles a day for a week, in km").

---

### 03 — Two-Agent System *(Groq required)*

Two agents with **different system prompts** collaborate on the same task.

**Pipeline:**
```
START → researcher → writer → END
```

- **Researcher** → gathers 5-7 facts, statistics, trends, and sub-topics
- **Writer** → turns the notes into a structured article with intro, sections, and conclusion

The writer explicitly receives the researcher's notes via state — this is the handoff.  
Change `topic` and `word_count_target` in the initial state to generate articles on any subject.

---

### 04 — Supervisor Pattern *(Groq required)*

A supervisor LLM dynamically controls a team of three workers.

**Workers:**
- `analyst` — extracts data points and metrics
- `summarizer` — condenses findings into 3-5 insights
- `fact_checker` — reviews content, flags uncertain claims

**Routing loop:**
```
START → supervisor → analyst    ──┐
             ↑──────── summarizer  ├── each worker returns to supervisor
             ↑──────── fact_checker┘
             │
         (all done)
             │
        compile_report → END
```

The supervisor reads `completed_workers` from state to decide who goes next.  
It never routes to a worker that's already done.

---

### 05 — Collaborative Pipeline *(Groq required)*

A full production-style multi-agent system with 5 agents and a quality control loop.

**Agents:**
| Agent | Reads | Writes |
|-------|-------|--------|
| Planner | `topic`, `audience` | `research_plan` |
| Researcher | `research_plan` | `raw_research` |
| Analyst | `raw_research` | `analysis` |
| Writer | `raw_research`, `analysis`, `feedback` | `draft` |
| Critic | `draft` | `quality_score`, `feedback` |

**Quality gate:**
- Score ≥ 7 → approved, go to END
- Score < 7 AND revisions remaining → Writer revises
- Max revisions reached → accept draft as-is

Call `create_brief(topic, audience)` in the final cell to produce a brief on any topic.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `ModuleNotFoundError` | Run `pip install -r requirements.txt` with venv active |
| `AuthenticationError: GROQ_API_KEY` | Check `.env` exists and the key is correct |
| `RateLimitError` | Groq free tier has per-minute limits — wait 30 s and retry |
| Notebook 05 loops forever | Check `MAX_REVISIONS` is set (default: 2) |
| No live search in 05 | Add `TAVILY_API_KEY` to `.env` — falls back to LLM knowledge without it |
| Graph visualisation fails | Install `pip install pygraphviz` or just read the mermaid text output |

---

## What to Explore Next

- **Memory & checkpointing** — persist agent state across sessions with `MemorySaver`
- **Human-in-the-loop** — use `interrupt()` to pause and wait for human approval before continuing
- **Parallel agents** — use the `Send` API to fan out work to multiple agents simultaneously
- **Streaming** — use `graph.stream()` to get real-time updates as each node completes
- **LangGraph Platform** — deploy your multi-agent system as a scalable REST API
