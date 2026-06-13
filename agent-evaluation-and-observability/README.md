# Agent Evaluation & Observability

You built an agent. Now the hard question: **is it actually any good — and how do you know?**

This series teaches you how to *measure* agents and *see inside* them. Five
notebooks, each building on the last, taking you from "string matching from
scratch" to a full CI-style evaluation suite that catches regressions before
your users do.

**Stack:** Python · Groq (free LLM API) · plain standard library (no heavy eval framework — you build the pieces yourself so you understand them)

---

## Why this matters

> "We can't improve what we can't measure, and we can't fix what we can't see."

- **Evaluation** answers: *Is the output good?* (correctness, relevance, faithfulness)
- **Observability** answers: *What happened inside?* (every step, latency, tokens, cost, errors)

Most teams ship agents on vibes — they eyeball a few outputs and call it done.
Then a prompt tweak silently breaks 30% of cases and nobody notices for a week.
The skills in this folder are what separate a demo from a product.

---

## Project Overview

| # | Notebook | What it does | New concept | LLM? |
|---|----------|-------------|-------------|------|
| 01 | **Evaluation Foundations** | Score outputs against expected answers with metrics you write by hand | Test cases, exact/fuzzy match, token-F1, latency & cost | No |
| 02 | **LLM-as-a-Judge** | Use an LLM to grade open-ended answers no string can check | Binary & rubric judges, multi-criteria, pairwise, judge bias | Yes |
| 03 | **Tracing & Observability** | Capture every step of an agent — timings, tokens, nesting | Spans, traces, `@trace` decorator, cost capture, trace trees | Yes |
| 04 | **RAG & Agent Metrics** | Score a retrieval agent: did it retrieve well, stay grounded, answer the question? | Context precision/recall, faithfulness, answer relevance, tool-call correctness | Yes |
| 05 | **Evaluation Pipeline** | A repeatable eval suite that A/B-tests versions and fails on regressions | Golden dataset, metric registry, A/B compare, regression gate | Yes |

---

## Folder Structure

```
agent-evaluation-and-observability/
├── README.md               ← you are here
├── .env.example            ← copy to .env and add your GROQ_API_KEY
├── requirements.txt        ← all Python dependencies
│
├── 01-evaluation-foundations/
│   └── evaluation_foundations.ipynb     ← no LLM, metrics from scratch
│
├── 02-llm-as-judge/
│   └── llm_as_judge.ipynb               ← let an LLM grade the answers
│
├── 03-tracing-and-observability/
│   └── tracing_and_observability.ipynb  ← see inside the agent
│
├── 04-rag-agent-metrics/
│   └── rag_agent_metrics.ipynb          ← RAG triad + tool-call checks
│
└── 05-eval-pipeline/
    └── eval_pipeline.ipynb              ← full suite + regression gate
```

---

## Setup (one-time)

### 1. Go to the project folder

```bash
cd ~/ai-agents/agent-evaluation-and-observability
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

### 4. Set up your API key

```bash
cp .env.example .env
```

Then open `.env` and paste in your `GROQ_API_KEY` (see the API Keys section below).

### 5. Launch Jupyter

```bash
jupyter notebook
# or
jupyter lab
```

> **Start with notebook 01 — it needs no API key at all.** It's pure Python and
> teaches the mental model everything else builds on.

---

## Running Each Notebook

Open each notebook and run the cells **top to bottom**.

| Notebook | Key requirement | Run time |
|----------|----------------|----------|
| 01-evaluation-foundations | None — no API key | ~5 sec |
| 02-llm-as-judge | `GROQ_API_KEY` | ~20 sec |
| 03-tracing-and-observability | `GROQ_API_KEY` | ~20 sec |
| 04-rag-agent-metrics | `GROQ_API_KEY` | ~30 sec |
| 05-eval-pipeline | `GROQ_API_KEY` | ~60 sec |

---

## API Keys

### Groq — free LLM API (required for notebooks 02–05)

1. Sign up at **https://console.groq.com/**
2. Go to **API Keys** → **Create API Key**
3. Add it to `.env`:
   ```
   GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
   ```

Groq gives free access to Llama 3 with generous rate limits.
Model used throughout: `llama3-8b-8192`.

### LangSmith — production tracing (optional, shown in notebook 03)

Notebook 03 builds a tracer *from scratch* so you understand the idea. At the
end it shows how real teams get the same thing for free by flipping two env
vars. You do **not** need this to run anything.

1. Sign up at **https://smith.langchain.com/**
2. Add to `.env`:
   ```
   LANGCHAIN_API_KEY=lsv2_xxxxxxxxxxxxxxxxxxxx
   LANGCHAIN_TRACING_V2=true
   LANGCHAIN_PROJECT=Agent-Evaluation-And-Observability
   ```

---

## Evaluation Concepts — Quick Reference

| Concept | Plain-English meaning | Where shown |
|---------|----------------------|-------------|
| **Test case** | One input + the answer you expected | 01 |
| **Exact / fuzzy match** | Does the text match, strictly or loosely? | 01 |
| **Token-F1** | How much of the right answer's words did we get? | 01 |
| **Latency & cost** | How slow and how expensive was each call? | 01, 03 |
| **LLM-as-a-judge** | Ask a strong LLM to grade a weaker one's answer | 02, 04, 05 |
| **Rubric scoring** | Score 1–5 against written criteria, with reasoning | 02 |
| **Pairwise comparison** | Which of A vs B is better? | 02 |
| **Span / trace** | A timed record of one step / the whole run | 03 |
| **Faithfulness** | Did the answer stick to the retrieved facts? | 04 |
| **Answer relevance** | Did the answer actually address the question? | 04 |
| **Context precision/recall** | Did retrieval fetch the right documents? | 04 |
| **Tool-call correctness** | Did the agent call the right tool with the right args? | 04 |
| **Golden dataset** | The fixed set of cases you test every version against | 05 |
| **Regression gate** | A pass/fail threshold that blocks a bad release | 05 |

---

## Notebook Details

### 01 — Evaluation Foundations *(no API key)*

The mental model for everything that follows: an evaluation is just
`compare(what_the_agent_said, what_you_expected)` run over many cases and
averaged. You implement five metrics by hand — exact match, normalized match,
keyword coverage, token-level F1, and latency/cost tracking — then wire them
into a tiny reusable **eval harness** and run it over a sample agent.

### 02 — LLM-as-a-Judge *(Groq required)*

String matching breaks the moment answers are open-ended ("Explain why the sky
is blue"). The fix: ask a strong LLM to grade the answer. You build a binary
judge, a 1–5 rubric judge that explains its reasoning, a multi-criteria judge
(correctness + relevance + coherence), a reference-free judge, and a pairwise
"A vs B" judge — plus the bias traps (position bias, verbosity bias) and how to
avoid them.

### 03 — Tracing & Observability *(Groq required)*

Scores tell you *that* something is wrong; traces tell you *where*. You build a
minimal `Span`/`Tracer` and a `@trace` decorator from scratch, instrument a
multi-step agent, capture real token counts and cost from Groq responses, and
print the whole run as a readable trace tree. Ends with how to get this for
free in production via LangSmith / Langfuse / OpenTelemetry.

### 04 — RAG & Agent Metrics *(Groq required)*

Real agents retrieve and use tools, so they need their own metrics. Over a tiny
in-memory knowledge base you measure the **RAG triad** — context
precision/recall, faithfulness (is the answer grounded in the retrieved text?),
and answer relevance — plus **tool-call correctness** for agents that call
functions. The output is a per-question scorecard.

### 05 — Evaluation Pipeline *(Groq required)*

Everything comes together into a repeatable suite. A **golden dataset** runs
through a swappable agent (version A vs version B), every metric from notebooks
01–04 scores each case, results aggregate into a report (pass rate, avg score,
p95 latency, total cost), and a **regression gate** fails the run if quality
drops below a saved baseline — exactly what you'd wire into CI.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `ModuleNotFoundError` | Run `pip install -r requirements.txt` with the venv active |
| `AssertionError: Add GROQ_API_KEY` | Check `.env` exists in this folder and the key is correct |
| `RateLimitError` | Groq free tier has per-minute limits — wait 30 s and retry |
| Judge returns unparseable text | The notebooks show a `safe_parse` fallback — re-run the cell |
| Scores feel random | LLM judges aren't perfect; we set `temperature=0` and ask for reasoning to reduce noise |
| Notebook 01 asks for a key | It shouldn't — 01 needs no LLM. Make sure you opened `01-evaluation-foundations` |

---

## What to Explore Next

- **Open-source eval frameworks** — `ragas`, `deepeval`, `promptfoo`, `trulens`
  give you these metrics off the shelf once you understand the ideas.
- **Hosted observability** — LangSmith, Langfuse, Arize Phoenix, and
  OpenTelemetry-based stacks for production tracing and dashboards.
- **Human-in-the-loop eval** — collect thumbs-up/down from real users and use it
  to calibrate your automated judges.
- **Online vs offline eval** — offline (this folder) runs on a fixed dataset;
  online eval scores live production traffic in real time.
- **Guardrails** — turn failing eval criteria into runtime checks that block bad
  outputs before they reach the user.
