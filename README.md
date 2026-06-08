# AI Agents - Learning Series

A hands-on, concept-by-concept journey through building AI agents.
Each folder is self-contained with notebooks you can run immediately.

---

## Connect

[![GitHub](https://img.shields.io/badge/GitHub-%40Ramakm-181717?logo=github&logoColor=white)](https://github.com/Ramakm)
[![Medium](https://img.shields.io/badge/Medium-%40techwithram-black?logo=medium&logoColor=white)](https://medium.com/@techwithram)
[![X](https://img.shields.io/badge/X-%40techwith__ram-black?logo=x)](https://x.com/techwith_ram)
[![Instagram](https://img.shields.io/badge/Instagram-%40techwith.ram-E4405F?logo=instagram&logoColor=white)](https://instagram.com/techwith.ram)

---

## Progress

| # | Topic | Status | Folder |
|---|-------|--------|--------|
| 1 | LangChain | ✅ Done | [`langchain/`](./langchain/) |
| 2 | LangGraph | ✅ Done | [`langgraph/`](./langgraph/) |
| 3 | Memory & State | ✅ Done | [`memory-and-state/`](./memory-and-state/) |
| 4 | RAG | ✅ Done | [`RAG/`](./RAG/) |
| 5 | VectorlessRAG | ✅ Done | [`vectorlessRAG/`](./vectorlessRAG/) |
| 6 | Tool Use & Function Calling | Coming soon | - |
| 7 | Multi-Agent Systems | Coming soon | - |
| 8 | Agent Evaluation & Observability | Coming soon | - |

---

## 1. LangChain

Five notebooks covering LangChain from the ground up: models, prompts,
tool calling, LCEL chains, and streaming.

| Notebook | What you will learn |
|----------|---------------------|
| `01_langchain_fundamentals.ipynb` | Models, messages, multi-turn chat |
| `02_prompt_templates_and_parsers.ipynb` | PromptTemplate, ChatPromptTemplate, output parsers |
| `03_tool_calling.ipynb` | @tool, .bind_tools(), ReAct agent loop |
| `04_lcel_chains.ipynb` | Pipe operator, RunnableParallel, RunnableBranch |
| `05_streaming_and_batch.ipynb` | stream(), batch(), async, astream_events() |

**API keys:** `GROQ_API_KEY`, `GOOGLE_API_KEY`

**Setup:**
```bash
cd langchain
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
# Create a .env file and add GROQ_API_KEY and GOOGLE_API_KEY
jupyter notebook
```

---

## 2. LangGraph

Five notebooks covering LangGraph state graphs: sequential flows,
conditional edges, loops, multi-agent collaboration, and a supervisor
pattern with live web search.

| Notebook | What you will learn |
|----------|---------------------|
| `01-simple-graph/mood_detector.ipynb` | StateGraph, nodes, sequential edges - no LLM needed |
| `02-quiz-bot/quiz_bot.ipynb` | Conditional edges and loop until correct |
| `03-code-reviewer/code_reviewer.ipynb` | Four-node sequential pipeline |
| `04-travel-planner/travel_planner.ipynb` | Multi-agent collaboration |
| `05-news-analyst/news_analyst.ipynb` | Supervisor pattern with Tavily web search |

**API keys:** `GROQ_API_KEY`, `TAVILY_API_KEY` (notebook 05 only)

**Setup:**
```bash
cd langgraph
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
# Create a .env file and add GROQ_API_KEY and TAVILY_API_KEY
jupyter notebook
```

> Notebook 01 needs no API key. Start there.

---

## 3. Memory & State

Five notebooks covering every type of agent memory: plain Python dict,
conversation buffer, LLM-based summary compression, LangGraph MemorySaver
sessions, and JSON-based persistent memory that survives restarts.

| Notebook | What you will learn |
|----------|---------------------|
| `01-why-memory-matters/why_memory_matters.ipynb` | Stateless vs stateful - Python dict/list memory store |
| `02-conversation-buffer/conversation_buffer.ipynb` | Full buffer and window trimming to control token cost |
| `03-summary-memory/summary_memory.ipynb` | LLM compresses old messages to save tokens |
| `04-langgraph-sessions/langgraph_sessions.ipynb` | MemorySaver checkpointer and thread_id multi-user sessions |
| `05-persistent-memory/persistent_memory.ipynb` | JSON file memory that survives Python restarts |

**API keys:** `GROQ_API_KEY` (notebooks 02-05)

**Setup:**
```bash
cd memory-and-state
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env   # add GROQ_API_KEY
jupyter notebook
```

> Notebook 01 needs no API key. Start there.

---

## 4. RAG

Five notebooks covering retrieval-augmented generation: cosine similarity
from scratch, FAISS index, LangChain LCEL chains, ChromaDB with metadata
filtering, and agentic RAG with a self-correcting retry loop.

| Notebook | What you will learn |
|----------|---------------------|
| `01-rag-foundations/rag_foundations.ipynb` | Cosine similarity from scratch, no vector DB |
| `02-vector-store-rag/vector_store_rag.ipynb` | FAISS index, save/load, semantic vs keyword search |
| `03-qa-pipeline/qa_pipeline.ipynb` | LangChain LCEL chain with PromptTemplate and Groq |
| `04-multi-doc-rag/multi_doc_rag.ipynb` | ChromaDB with metadata filtering and source citations |
| `05-agentic-rag/agentic_rag.ipynb` | LangGraph agentic RAG with self-correcting retrieval loop |

**API keys:** `GROQ_API_KEY` (notebooks 03-05)

**Note:** The first run downloads the `all-MiniLM-L6-v2` embedding model (~90 MB).
It is cached automatically after that.

**Setup:**
```bash
cd RAG
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
# Create a .env file and add GROQ_API_KEY
jupyter notebook
```

> Notebooks 01 and 02 need no API key. Start there.

---

## 5. VectorlessRAG

Five notebooks covering RAG without a vector database. Uses TF-IDF, BM25,
sliding window chunking, LLM reranking, and map-reduce to retrieve and
answer questions from a local knowledge base.

| Notebook | What you will learn |
|----------|---------------------|
| `01-tfidf-retrieval/tfidf_retrieval.ipynb` | TF-IDF scoring built from scratch with pure Python |
| `02-bm25-retrieval/bm25_retrieval.ipynb` | Okapi BM25 via rank-bm25, compared against TF-IDF |
| `03-sliding-window-rag/sliding_window_rag.ipynb` | Overlapping chunk windows, BM25 retrieval, Groq generation |
| `04-llm-reranker-rag/llm_reranker_rag.ipynb` | BM25 candidates scored by an LLM for semantic relevance |
| `05-map-reduce-rag/map_reduce_rag.ipynb` | MAP each chunk with the LLM, REDUCE into a final answer |

**API keys:** `GROQ_API_KEY` (notebooks 03-05)

**Setup:**
```bash
cd vectorlessRAG
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env   # add GROQ_API_KEY
jupyter notebook
```

> Notebooks 01 and 02 need no API key. Start there.

---

## Running Any Project

See [`run.md`](./run.md) for the full setup guide, API key sources, notebook
order diagrams, and a common errors reference for every project.
