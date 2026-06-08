# AI Agents — Learning Series

A hands-on, concept-by-concept journey through building AI agents.  
Each section is a self-contained folder with notebooks you can run immediately.

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
| 4 | Tool Use & Function Calling | 🔜 | — |
| 5 | Multi-Agent Systems | 🔜 | — |
| 6 | RAG (Retrieval-Augmented Generation) | ✅ Done | [`RAG/`](./RAG/) |
| 7 | VectorlessRAG | ✅ Done | [`vectorlessRAG/`](./vectorlessRAG/) |
| 8 | Agent Evaluation & Observability | 🔜 | — |

---

## 1. LangChain ✅

Five notebooks covering LangChain from the ground up:

| Notebook | What you'll learn |
|----------|-------------------|
| `01_langchain_fundamentals.ipynb` | Models, messages, multi-turn chat |
| `02_prompt_templates_and_parsers.ipynb` | PromptTemplate, ChatPromptTemplate, output parsers |
| `03_tool_calling.ipynb` | `@tool`, `.bind_tools()`, ReAct agent loop |
| `04_lcel_chains.ipynb` | Pipe operator, RunnableParallel, RunnableBranch |
| `05_streaming_and_batch.ipynb` | stream(), batch(), async, astream_events() |

**Setup:**
```bash
cd langchain
pip install -r requirements.txt
# Add your API keys to a .env file
# GROQ_API_KEY=...
# GOOGLE_API_KEY=...
```

---

## 3. Memory & State ✅

Five notebooks covering every type of agent memory — from a plain Python dict
to a persistent JSON store that survives Python restarts.

| Notebook | What you'll learn |
|----------|-------------------|
| `01_why_memory_matters.ipynb` | Stateless vs stateful chatbot — Python dict/list memory |
| `02_conversation_buffer.ipynb` | Full buffer + window trimming to control token cost |
| `03_summary_memory.ipynb` | LLM compresses old messages — 75% token saving |
| `04_langgraph_sessions.ipynb` | `MemorySaver` checkpointer + `thread_id` multi-user sessions |
| `05_persistent_memory.ipynb` | JSON file — facts and history survive restarts |

**Setup:**
```bash
cd memory-and-state
pip install -r requirements.txt
cp .env.example .env   # add your GROQ_API_KEY
jupyter notebook
```

> Notebook 01 needs no API key — start there.

---

## 7. VectorlessRAG

Five notebooks covering retrieval-augmented generation without a vector
database. Uses TF-IDF, BM25, sliding windows, LLM reranking, and map-reduce
to retrieve and answer questions from a local knowledge base.

| Notebook | What you will learn |
|----------|---------------------|
| `01_tfidf_retrieval.ipynb` | TF-IDF scoring built from scratch using pure Python |
| `02_bm25_retrieval.ipynb` | Okapi BM25 with rank-bm25, compared against TF-IDF |
| `03_sliding_window_rag.ipynb` | Overlapping chunk windows, BM25 retrieval, Groq generation |
| `04_llm_reranker_rag.ipynb` | BM25 candidates scored by an LLM for semantic relevance |
| `05_map_reduce_rag.ipynb` | MAP each chunk with the LLM, REDUCE into a final answer |

**Setup:**
```bash
cd vectorlessRAG
pip install -r requirements.txt
cp .env.example .env   # add GROQ_API_KEY for notebooks 03-05
jupyter notebook
```

> Notebooks 01 and 02 require no API key.

