# Memory & State — AI Agents Learning Series

Five notebooks that teach every type of memory an AI agent can use — from a plain
Python dictionary all the way to a persistent JSON store that survives restarts.

**Stack:** Python · LangChain · LangGraph · Groq (free LLM) · python-dotenv

---

## Project Overview

| # | Notebook | Topic | Key Concept | LLM? |
|---|----------|-------|-------------|------|
| 01 | **Why Memory Matters** | Stateless vs Stateful chatbot | Python dict/list memory store | No |
| 02 | **Conversation Buffer** | Multi-turn chatbot | Message history list + window trimming | Yes |
| 03 | **Summary Memory** | Long conversations | LLM compresses old messages | Yes |
| 04 | **LangGraph Sessions** | Multi-user chatbot | `MemorySaver` checkpointer + `thread_id` | Yes |
| 05 | **Persistent Memory** | Personal assistant | JSON file — survives Python restart | Yes |

---

## Folder Structure

```
memory-and-state/
├── README.md               ← you are here
├── .env.example            ← copy to .env and fill in your Groq key
├── requirements.txt        ← all Python dependencies
│
├── 01-why-memory-matters/
│   └── why_memory_matters.ipynb    ← pure Python, no API key needed
│
├── 02-conversation-buffer/
│   └── conversation_buffer.ipynb   ← buffer + window memory
│
├── 03-summary-memory/
│   └── summary_memory.ipynb        ← LLM-based compression
│
├── 04-langgraph-sessions/
│   └── langgraph_sessions.ipynb    ← MemorySaver + thread_id
│
└── 05-persistent-memory/
    └── persistent_memory.ipynb     ← JSON long-term memory
```

---

## Setup (one-time)

### 1. Navigate to this folder

```bash
cd ~/ai-agents/memory-and-state
```

### 2. Create and activate a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate        # macOS / Linux
# .venv\Scripts\activate         # Windows
```

### 3. Install all dependencies

```bash
pip install -r requirements.txt
```

### 4. Set up your API key

```bash
cp .env.example .env
```

Open `.env` and add your Groq key (see below).

### 5. Launch Jupyter

```bash
jupyter notebook
# or
jupyter lab
```

---

## Running Each Notebook

Open each notebook and run cells **top to bottom**.

| Notebook | Key requirement |
|----------|----------------|
| 01-why-memory-matters | **None** — pure Python |
| 02-conversation-buffer | `GROQ_API_KEY` |
| 03-summary-memory | `GROQ_API_KEY` |
| 04-langgraph-sessions | `GROQ_API_KEY` |
| 05-persistent-memory | `GROQ_API_KEY` |

> **Start with Notebook 01** — no API key needed, builds the mental model.

---

## API Keys

### Groq — free LLM API (required for notebooks 02–05)

1. Sign up at **https://console.groq.com/**
2. Go to **API Keys → Create API Key**
3. Add to `.env`:
   ```
   GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
   ```

Model used: `llama3-8b-8192` — fast, capable, free tier.

### LangSmith — tracing (optional)

Lets you visualise every LLM call and inspect message history in a dashboard.

```
LANGCHAIN_API_KEY=lsv2_xxxxxxxxxxxxxxxxxxxx
LANGCHAIN_TRACING_V2=true
LANGCHAIN_PROJECT=Memory-And-State
```

---

## Concept Map

```
Every AI conversation ──► needs to be stored somewhere ──► that's MEMORY

Where can we store it?
┌───────────────────────────────────────────────────────────┐
│  In RAM (Python)          In RAM (LangGraph)    On Disk   │
│  ├── dict / list          ├── MemorySaver       └── JSON  │
│  ├── Buffer list          └── thread_id              file │
│  └── Summary (compressed)                               │
└───────────────────────────────────────────────────────────┘
                 ↑ Notebooks 01–03          ↑ 04      ↑ 05
```

---

## Notebook Details

### 01 — Why Memory Matters *(no API key)*

Starts with a "forgetful bot" that can never remember your name, then builds a
`SimpleMemory` class from scratch using a Python dict and a list.

**Key takeaways:**
- Memory is just data stored in a variable
- Without memory, every message is processed independently
- A dict stores facts; a list stores conversation history

---

### 02 — Conversation Buffer *(Groq required)*

Passes the **entire message history** to the LLM on every call, so it always
knows what was said earlier.

**Two modes demonstrated:**
- **Full buffer** — keeps every message forever
- **Window buffer** — keeps only the last `N` messages to limit token cost

```python
# Every LLM call sends: [system] + [all previous messages] + [new message]
messages = [SYSTEM_MSG] + buffer
response = llm.invoke(messages)
```

---

### 03 — Summary Memory *(Groq required)*

Instead of dropping old messages (window memory), uses an LLM to **compress** them
into a 2–3 sentence summary. The summary + recent messages are passed to the LLM.

**Token savings:**
- A 12-turn full buffer might use ~480 tokens per call
- Summary memory keeps that under ~120 tokens — a 75% reduction

```python
# When buffer exceeds MAX_RECENT, compress the oldest half
new_summary = llm.invoke([HumanMessage(content=f"Summarise:\n{old_messages}")])
```

---

### 04 — LangGraph Sessions *(Groq required)*

Uses LangGraph's built-in **`MemorySaver`** checkpointer. Each conversation gets
a unique `thread_id` — the graph saves and restores state automatically.

```python
checkpointer = MemorySaver()
graph        = builder.compile(checkpointer=checkpointer)

# Alice's session
graph.invoke({"messages": [HumanMessage("Hi, I'm Alice")]},
             {"configurable": {"thread_id": "alice"}})

# Bob's session — completely separate
graph.invoke({"messages": [HumanMessage("Hi, I'm Bob")]},
             {"configurable": {"thread_id": "bob"}})
```

**`add_messages` reducer** — automatically appends new messages instead of
replacing the whole list.

---

### 05 — Persistent Memory *(Groq required)*

Saves all facts and conversation history to a `.json` file on disk. Reloads
automatically on the next run — memory survives Python restarts.

**What's stored:**
- User facts (name, age, city, job) — auto-extracted from natural language
- Preferences (hobbies, favourites) — auto-extracted
- Last 20 messages (10 exchanges)
- Session count and last-seen timestamp

```python
mem = PersistentMemory("agent_memory.json")
mem.start_session()   # loads from file if it exists
personal_agent("Hi, I'm Vikram.", mem)   # facts saved to disk immediately
```

---

## Memory Types — Quick Reference

| Type | Where stored | Survives restart? | Best for |
|------|-------------|------------------|----------|
| Dict / List (01) | Python variable | ❌ No | Learning the concept |
| Buffer (02) | Python variable | ❌ No | Short Q&A sessions |
| Window (02) | Python variable | ❌ No | Long chat, don't need old context |
| Summary (03) | Python variable | ❌ No | Long chat, token budget |
| MemorySaver (04) | Python RAM | ❌ No | Multi-user apps, same process |
| JSON file (05) | Disk | ✅ Yes | Personal assistants, long-term agents |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `ModuleNotFoundError` | Run `pip install -r requirements.txt` with venv active |
| `GROQ_API_KEY not set` | Check `.env` file exists and key is correct |
| `ImportError: langgraph` | Run `pip install langgraph>=0.4.8` |
| `agent_memory.json` has stale data | Delete the file to start fresh |
| LLM replies seem out of context | Make sure you're running cells top-to-bottom |

---

## What to Explore Next

- **Redis-backed memory** — replace the JSON file with a Redis store for multi-user production apps
- **Vector memory** — store past conversations as embeddings and retrieve by semantic similarity
- **LangGraph SqliteSaver** — SQLite-backed checkpointer for persistent LangGraph sessions
- **Conversation summarisation chains** — `ConversationSummaryBufferMemory` in LangChain
- **Tool Use & Function Calling** — the next section in this learning series
