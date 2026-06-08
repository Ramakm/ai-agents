# Run Guide

How to set up and run every project in this repository.

---

## Repository Structure

```
ai-agents/
|-- langchain/
|-- langgraph/
|-- memory-and-state/
|-- RAG/
|-- vectorlessRAG/
|-- README.md
`-- run.md              <- you are here
```

---

## General Setup Flow

Every project follows the same four-step pattern before you open Jupyter.

```
 cd into the project folder
          |
          v
 python3 -m venv .venv
 source .venv/bin/activate
          |
          v
 pip install -r requirements.txt
          |
          v
 create .env and add API keys      <-- skip if the project has no API requirement
          |
          v
 jupyter notebook
          |
          v
 open the notebook -> run cells top to bottom
```

---

## Projects

---

### 1. langchain

**Folder:** `langchain/`

Five notebooks covering LangChain fundamentals: models, prompts, tool
calling, LCEL chains, and streaming.

**API keys required:**

| Key | Where to get it | Required for |
|-----|----------------|-------------|
| GROQ_API_KEY | https://console.groq.com/ | All notebooks |
| GOOGLE_API_KEY | https://aistudio.google.com/app/apikey | Notebooks that use Gemini |

**Steps:**

```bash
cd langchain
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Create a `.env` file manually (no `.env.example` provided):

```bash
touch .env
```

Add your keys inside `.env`:

```
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
GOOGLE_API_KEY=AIxxxxxxxxxxxxxxxxxxxxxxxxx
```

Launch Jupyter:

```bash
jupyter notebook
```

**Notebook order:**

```
01_langchain_fundamentals.ipynb
        |
        v
02_prompt_templates_and_parsers.ipynb
        |
        v
03_tool_calling.ipynb
        |
        v
04_lcel_chains.ipynb
        |
        v
05_streaming_and_batch.ipynb
```

---

### 2. langgraph

**Folder:** `langgraph/`

Five notebooks covering LangGraph state graphs: sequential flows,
conditional edges, loops, multi-agent collaboration, and supervisor patterns.

**API keys required:**

| Key | Where to get it | Required for |
|-----|----------------|-------------|
| GROQ_API_KEY | https://console.groq.com/ | Notebooks 02, 03, 04, 05 |
| TAVILY_API_KEY | https://app.tavily.com/ | Notebook 05 only |
| LANGCHAIN_API_KEY | https://smith.langchain.com/ | Optional (tracing) |

**Steps:**

```bash
cd langgraph
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Create `.env`:

```
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
TAVILY_API_KEY=tvly-xxxxxxxxxxxxxxxxxxxx
```

```bash
jupyter notebook
```

**Notebook order and API key map:**

```
01-simple-graph/mood_detector.ipynb         no API key needed
        |
        v
02-quiz-bot/quiz_bot.ipynb                  GROQ_API_KEY
        |
        v
03-code-reviewer/code_reviewer.ipynb        GROQ_API_KEY
        |
        v
04-travel-planner/travel_planner.ipynb      GROQ_API_KEY
        |
        v
05-news-analyst/news_analyst.ipynb          GROQ_API_KEY + TAVILY_API_KEY
```

---

### 3. RAG

**Folder:** `RAG/`

Five notebooks covering retrieval-augmented generation: cosine similarity
from scratch, FAISS, LangChain LCEL chains, ChromaDB with metadata
filtering, and agentic RAG with a self-correcting loop.

**API keys required:**

| Key | Where to get it | Required for |
|-----|----------------|-------------|
| GROQ_API_KEY | https://console.groq.com/ | Notebooks 03, 04, 05 |
| LANGCHAIN_API_KEY | https://smith.langchain.com/ | Optional (tracing) |

**Note:** The first run of any notebook will download the
`all-MiniLM-L6-v2` sentence-transformers model (about 90 MB). This is
cached automatically and does not repeat on subsequent runs.

**Steps:**

```bash
cd RAG
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Create `.env`:

```
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
```

```bash
jupyter notebook
```

**Notebook order and API key map:**

```
01-rag-foundations/rag_foundations.ipynb        no API key needed
        |
        v
02-vector-store-rag/vector_store_rag.ipynb      no API key needed
        |
        v
03-qa-pipeline/qa_pipeline.ipynb                GROQ_API_KEY
        |
        v
04-multi-doc-rag/multi_doc_rag.ipynb            GROQ_API_KEY
        |
        v
05-agentic-rag/agentic_rag.ipynb                GROQ_API_KEY
```

**Data files** (used across all notebooks):

```
RAG/data/
|-- space_facts.txt
|-- nutrition_guide.txt
|-- python_tips.txt
`-- world_history.txt
```

---

### 4. memory-and-state

**Folder:** `memory-and-state/`

Five notebooks covering agent memory: in-memory Python dict, conversation
buffer, LLM-based summary compression, LangGraph MemorySaver sessions, and
JSON-based persistent memory that survives restarts.

**API keys required:**

| Key | Where to get it | Required for |
|-----|----------------|-------------|
| GROQ_API_KEY | https://console.groq.com/ | Notebooks 02, 03, 04, 05 |
| LANGCHAIN_API_KEY | https://smith.langchain.com/ | Optional (tracing) |

**Steps:**

```bash
cd memory-and-state
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```

Open `.env` and fill in `GROQ_API_KEY`.

```bash
jupyter notebook
```

**Notebook order and API key map:**

```
01-why-memory-matters/why_memory_matters.ipynb      no API key needed
        |
        v
02-conversation-buffer/conversation_buffer.ipynb    GROQ_API_KEY
        |
        v
03-summary-memory/summary_memory.ipynb              GROQ_API_KEY
        |
        v
04-langgraph-sessions/langgraph_sessions.ipynb      GROQ_API_KEY
        |
        v
05-persistent-memory/persistent_memory.ipynb        GROQ_API_KEY
```

**Note:** Notebook 05 creates a file called `agent_memory.json` in the
same directory. Delete it to reset all saved memory.

---

### 5. vectorlessRAG

**Folder:** `vectorlessRAG/`

Five notebooks covering RAG without a vector database: TF-IDF from
scratch, Okapi BM25, sliding window chunking, LLM reranking, and
map-reduce over an entire document collection.

**API keys required:**

| Key | Where to get it | Required for |
|-----|----------------|-------------|
| GROQ_API_KEY | https://console.groq.com/ | Notebooks 03, 04, 05 |
| LANGCHAIN_API_KEY | https://smith.langchain.com/ | Optional (tracing) |

**Steps:**

```bash
cd vectorlessRAG
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```

Open `.env` and fill in `GROQ_API_KEY`.

```bash
jupyter notebook
```

**Notebook order and API key map:**

```
01-tfidf-retrieval/tfidf_retrieval.ipynb            no API key needed
        |
        v
02-bm25-retrieval/bm25_retrieval.ipynb              no API key needed
        |
        v
03-sliding-window-rag/sliding_window_rag.ipynb      GROQ_API_KEY
        |
        v
04-llm-reranker-rag/llm_reranker_rag.ipynb          GROQ_API_KEY
        |
        v
05-map-reduce-rag/map_reduce_rag.ipynb              GROQ_API_KEY
```

**Data files** (used across all notebooks):

```
vectorlessRAG/data/
|-- ancient_civilizations.txt
|-- climate_science.txt
|-- machine_learning_basics.txt
`-- world_geography.txt
```

---

## API Keys Reference

| Key | Free | Link | Used in |
|-----|------|------|---------|
| GROQ_API_KEY | Yes | https://console.groq.com/ | langchain, langgraph, RAG, memory-and-state, vectorlessRAG |
| GOOGLE_API_KEY | Yes (limited) | https://aistudio.google.com/app/apikey | langchain |
| TAVILY_API_KEY | Yes (limited) | https://app.tavily.com/ | langgraph (notebook 05) |
| LANGCHAIN_API_KEY | Yes | https://smith.langchain.com/ | Optional in all projects |

All LLM calls in this repository use `llama3-8b-8192` via Groq unless
otherwise noted. Groq's free tier is sufficient to run every notebook.

---

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `ModuleNotFoundError` | Package not installed | Run `pip install -r requirements.txt` with venv active |
| `GROQ_API_KEY not set` | Missing .env file or wrong key name | Check .env exists and key is spelled correctly |
| `jupyter: command not found` | Jupyter not installed in venv | Run `pip install jupyter` inside the venv |
| `allow_dangerous_deserialization` | FAISS load in RAG notebooks | Pass `allow_dangerous_deserialization=True` to `FAISS.load_local` |
| ChromaDB collection already exists | Re-running notebook 04 in RAG | Delete the `chroma_study/` folder and re-run |
| HuggingFace download times out | First-run model download on slow connection | Wait and retry; model is cached after first download |
