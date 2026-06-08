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
| 3 | Memory & State | 🔜 | — |
| 4 | Tool Use & Function Calling | 🔜 | — |
| 5 | Multi-Agent Systems | 🔜 | — |
| 6 | RAG (Retrieval-Augmented Generation) | ✅ Done | [`RAG/`](./RAG/) |
| 7 | Agent Evaluation & Observability | 🔜 | — |

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

