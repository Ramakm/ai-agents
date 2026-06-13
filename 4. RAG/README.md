# RAG Projects Collection

Five original, progressively complex RAG (Retrieval-Augmented Generation) projects — each built around a new concept and a real knowledge base.

**Stack:** Python · LangChain · FAISS · ChromaDB · Groq (free LLM) · LangGraph · SentenceTransformers

---

## Project Overview

| # | Notebook | Topic | Key Concept | LLM? |
|---|----------|-------|-------------|------|
| 01 | **RAG Foundations** | Space Facts | Cosine similarity from scratch — no vector DB | No |
| 02 | **Vector Store RAG** | Python Tips | FAISS index — save, load, semantic vs keyword search | No |
| 03 | **Q&A Pipeline** | Nutrition Guide | Full LangChain LCEL chain with prompt + Groq | Yes |
| 04 | **Multi-Document RAG** | Study Assistant | ChromaDB + metadata filtering + source citations | Yes |
| 05 | **Agentic RAG** | Research Assistant | LangGraph — self-correcting retrieval with retry loop | Yes |

---

## Folder Structure

```
RAG/
├── README.md               ← you are here
├── .env.example            ← copy to .env and fill in your Groq key
├── requirements.txt        ← all Python dependencies
│
├── data/                   ← knowledge base text files (used across all notebooks)
│   ├── space_facts.txt
│   ├── nutrition_guide.txt
│   ├── python_tips.txt
│   └── world_history.txt
│
├── 01-rag-foundations/
│   └── rag_foundations.ipynb   ← RAG from scratch, pure numpy
│
├── 02-vector-store-rag/
│   └── vector_store_rag.ipynb  ← FAISS vector store
│
├── 03-qa-pipeline/
│   └── qa_pipeline.ipynb       ← LangChain LCEL chain + Groq
│
├── 04-multi-doc-rag/
│   └── multi_doc_rag.ipynb     ← ChromaDB + metadata + multi-topic
│
└── 05-agentic-rag/
    └── agentic_rag.ipynb       ← LangGraph agentic RAG
```

---

## Setup (one-time)

### 1. Navigate to this folder

```bash
cd ~/ai-agents/RAG
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

> The first run of any notebook will download the `all-MiniLM-L6-v2` embedding model (~90 MB) from HuggingFace — this happens once and is cached automatically.

### 4. Set up API keys

```bash
cp .env.example .env
```

Open `.env` and fill in your Groq key (see API Keys section below).

### 5. Launch Jupyter

```bash
jupyter notebook
# or
jupyter lab
```

---

## Running Each Notebook

Open each notebook in Jupyter and run cells **top to bottom**.

| Notebook | Command to open | Key requirement |
|----------|----------------|-----------------|
| 01-rag-foundations | `jupyter notebook 01-rag-foundations/rag_foundations.ipynb` | None |
| 02-vector-store-rag | `jupyter notebook 02-vector-store-rag/vector_store_rag.ipynb` | None |
| 03-qa-pipeline | `jupyter notebook 03-qa-pipeline/qa_pipeline.ipynb` | `GROQ_API_KEY` |
| 04-multi-doc-rag | `jupyter notebook 04-multi-doc-rag/multi_doc_rag.ipynb` | `GROQ_API_KEY` |
| 05-agentic-rag | `jupyter notebook 05-agentic-rag/agentic_rag.ipynb` | `GROQ_API_KEY` |

---

## API Keys

### Groq — free LLM API (required for notebooks 03, 04, 05)

1. Sign up at **https://console.groq.com/**
2. Go to **API Keys** → **Create API Key**
3. Add to `.env`:
   ```
   GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
   ```

Model used: `llama3-8b-8192` — fast, capable, free tier.

### LangSmith — tracing (optional, any notebook)

Lets you visualise every RAG step, inspect retrieved chunks, and debug failures.

1. Sign up at **https://smith.langchain.com/**
2. Add to `.env`:
   ```
   LANGCHAIN_API_KEY=lsv2_xxxxxxxxxxxxxxxxxxxx
   LANGCHAIN_TRACING_V2=true
   LANGCHAIN_PROJECT=RAG-Projects
   ```

---

## What is RAG?

```
User Question
     │
     ▼
  [Embed question]
     │
     ▼ cosine similarity
  [Vector Store] ──── top-k most relevant chunks ────▶ [Prompt]
                                                            │
                                                       [LLM] ──▶ Grounded Answer
```

RAG (Retrieval-Augmented Generation) solves LLM hallucination by:
1. **Storing** your documents as vectors in a database
2. **Retrieving** the most relevant passages for each question
3. **Generating** an answer grounded in real text — not just model memory

---

## Notebook Details

### 01 — RAG Foundations *(no API key)*

Builds RAG entirely from scratch without any vector database framework.

- Creates `Document` objects manually
- Splits text with `RecursiveCharacterTextSplitter`
- Embeds with `sentence-transformers` (`all-MiniLM-L6-v2`)
- Searches using **numpy cosine similarity** by hand

Great starting point — you will see exactly what FAISS and ChromaDB are doing internally.

---

### 02 — Vector Store RAG *(no API key)*

Replaces manual numpy search with a real **FAISS index**.

- Loads `python_tips.txt` as the knowledge base
- Builds, saves, and loads a FAISS index to/from disk
- Implements semantic search function
- **Compares** semantic search vs simple keyword matching on the same query

---

### 03 — Q&A Pipeline *(Groq required)*

The complete RAG chain using **LangChain LCEL** (pipe operator syntax).

```python
rag_chain = (
    {"context": retriever | format_docs, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)
```

- Uses `nutrition_guide.txt` as the knowledge base
- `HuggingFaceEmbeddings` + `FAISS.from_documents` for the vector store
- `PromptTemplate` injects context and question into the LLM
- Final cell shows answer with source attribution

---

### 04 — Multi-Document RAG *(Groq required)*

Loads all **4 topic files** into a single **ChromaDB** collection with metadata tags.

- Tags each chunk with `topic` and `category` metadata
- Shows **standard retrieval** (across all topics)
- Shows **filtered retrieval** (`filter={"topic": "python"}`)
- `study_qa()` function returns answer + which source files were used

Good for understanding how to build a multi-domain knowledge base.

---

### 05 — Agentic RAG *(Groq required)*

Wraps the RAG pipeline in a **LangGraph** graph with intelligent routing.

**5 nodes:**
| Node | Job |
|------|-----|
| `route_query` | Decides if retrieval is needed (direct vs retrieval path) |
| `retrieve` | Searches the vector store |
| `assess_context` | Scores retrieved context quality (0–1) |
| `rewrite_query` | Rephrases the question if context score is too low |
| `generate_answer` | Produces the final grounded answer |

**Self-correction loop:** if context score < 0.35, the agent rewrites the query and retrieves again (max 2 retries before giving up).

---

## Core Concepts Reference

| Concept | Notebook | What it does |
|---------|----------|-------------|
| `Document` | 01 | Container: `page_content` + `metadata` dict |
| `RecursiveCharacterTextSplitter` | 01–05 | Splits text at paragraph/sentence/word boundaries |
| `SentenceTransformer` | 01–02 | Encodes text → fixed-size float vector |
| Cosine similarity | 01 | Measures direction alignment between two vectors |
| FAISS `IndexFlatL2` | 02–03 | Exact nearest-neighbour search in vector space |
| `HuggingFaceEmbeddings` | 03–04 | LangChain wrapper — unified embed API |
| `FAISS.from_documents` | 03 | One-step: embed + index all chunks |
| LCEL pipe `|` | 03 | Chain: retriever → prompt → LLM → parser |
| ChromaDB | 04 | Persistent vector store with metadata filtering |
| `filter={"topic": ...}` | 04 | Retrieve only from a specific topic subset |
| LangGraph `StateGraph` | 05 | Directed graph of agent nodes |
| Context assessment | 05 | LLM self-scores retrieved context quality |
| Query rewriting | 05 | LLM rephrases query to improve retrieval |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `ModuleNotFoundError` | Run `pip install -r requirements.txt` with venv active |
| `GROQ_API_KEY not set` | Check `.env` file exists with correct key |
| HuggingFace download slow | First run only; model cached at `~/.cache/huggingface/` |
| ChromaDB reset needed | Delete the `chroma_study/` folder and re-run the notebook |
| FAISS file not found | Run the "Build" cell before the "Load" cell |
| `allow_dangerous_deserialization` | Pass `allow_dangerous_deserialization=True` to `FAISS.load_local` |

---

## What to Explore Next

- **Hybrid search** — combine dense (embedding) + sparse (BM25) retrieval
- **Re-ranking** — use a cross-encoder to re-score retrieved chunks
- **Streaming responses** — `rag_chain.stream(question)` for token-by-token output
- **Conversational RAG** — add chat history so follow-up questions work
- **RAG evaluation** — RAGAS framework for measuring faithfulness and relevance
