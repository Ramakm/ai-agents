# VectorlessRAG

Five notebooks that teach retrieval-augmented generation without a vector
database. Instead of embedding documents and running cosine similarity, these
notebooks use classical information retrieval methods: TF-IDF, BM25, sliding
windows, LLM reranking, and map-reduce.

**Stack:** Python - rank-bm25 - LangChain - Groq (llama3-8b-8192) - python-dotenv

---

## Why Vectorless?

Vector databases are powerful, but they require embedding models, index
storage, and tuning. Many practical retrieval problems can be solved with
keyword-based methods that are:

- Faster to set up (no embedding step)
- Fully transparent (every score is explainable)
- Free to run (no GPU, no embedding API calls)
- Accurate enough for structured knowledge bases

This series covers those methods from first principles up to production patterns.

---

## Project Overview

| # | Notebook | Key Concept | LLM? |
|---|----------|-------------|------|
| 01 | TF-IDF Retrieval | Build TF-IDF scorer from scratch with pure Python | No |
| 02 | BM25 Retrieval | Okapi BM25 via rank-bm25, compare vs TF-IDF | No |
| 03 | Sliding Window RAG | Overlapping chunks + BM25 + Groq generation | Yes |
| 04 | LLM Reranker RAG | BM25 candidates + LLM scoring + Groq generation | Yes |
| 05 | Map-Reduce RAG | MAP each chunk with LLM, REDUCE into final answer | Yes |

---

## Folder Structure

```
vectorlessRAG/
|-- README.md
|-- requirements.txt
|-- .env.example
|
|-- data/
|   |-- ancient_civilizations.txt
|   |-- climate_science.txt
|   |-- machine_learning_basics.txt
|   `-- world_geography.txt
|
|-- 01-tfidf-retrieval/
|   `-- tfidf_retrieval.ipynb
|
|-- 02-bm25-retrieval/
|   `-- bm25_retrieval.ipynb
|
|-- 03-sliding-window-rag/
|   `-- sliding_window_rag.ipynb
|
|-- 04-llm-reranker-rag/
|   `-- llm_reranker_rag.ipynb
|
`-- 05-map-reduce-rag/
    `-- map_reduce_rag.ipynb
```

---

## Setup

### 1. Navigate to this folder

```bash
cd ~/ai-agents/vectorlessRAG
```

### 2. Create and activate a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Set up your API key

```bash
cp .env.example .env
```

Open `.env` and paste your Groq key. Notebooks 01 and 02 do not require any
API key. Notebooks 03, 04, and 05 require `GROQ_API_KEY`.

### 5. Launch Jupyter

```bash
jupyter notebook
```

---

## Running Each Notebook

Open each notebook and run cells top to bottom.

| Notebook | API Key Required |
|----------|----------------|
| 01 - TF-IDF Retrieval | None |
| 02 - BM25 Retrieval | None |
| 03 - Sliding Window RAG | GROQ_API_KEY |
| 04 - LLM Reranker RAG | GROQ_API_KEY |
| 05 - Map-Reduce RAG | GROQ_API_KEY |

Start with notebook 01. It builds the mental model that every subsequent
notebook builds on.

---

## API Key

### Groq (required for notebooks 03, 04, 05)

1. Sign up at https://console.groq.com/
2. Go to API Keys and create a new key
3. Add to `.env`:

```
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
```

Model used across all notebooks: `llama3-8b-8192` (fast, capable, free tier).

### LangSmith (optional)

Enables tracing and debugging of LLM calls in a dashboard.

```
LANGCHAIN_API_KEY=lsv2_xxxxxxxxxxxxxxxxxxxx
LANGCHAIN_TRACING_V2=true
LANGCHAIN_PROJECT=VectorlessRAG
```

---

## Notebook Details

### 01 - TF-IDF Retrieval from Scratch

Builds a complete TF-IDF retrieval system using only Python's standard library
and the `math` module. No external packages required.

Steps covered:
- Load and split documents into paragraph chunks
- Tokenize and remove stop words
- Compute Term Frequency (TF) per chunk
- Compute Inverse Document Frequency (IDF) across all chunks
- Score and rank chunks against a query

The formula:

```
TF(word, chunk)   = count(word in chunk) / total words in chunk
IDF(word)         = log( total_chunks / chunks_containing_word )
TF-IDF(query, chunk) = sum of TF * IDF for each query word
```

---

### 02 - BM25 Retrieval

Uses the `rank-bm25` library to build an Okapi BM25 index over the same
chunks from notebook 01. Demonstrates how BM25 improves on TF-IDF by:

- Capping term saturation (repeated words stop adding linear value)
- Normalizing by document length (long chunks do not unfairly dominate)

The notebook ends with a side-by-side comparison of BM25 vs TF-IDF on
the same queries so you can see when each method succeeds.

---

### 03 - Sliding Window RAG

Replaces fixed paragraph splits with overlapping windows:

```
Window 1: words 0 - 150
Window 2: words 75 - 225      (75-word overlap with window 1)
Window 3: words 150 - 300     (75-word overlap with window 2)
```

This prevents important sentences from being cut at chunk boundaries.
After retrieval with BM25, the top chunks are passed to Groq which
generates a grounded, factual answer.

Key parameters:

| Parameter | Meaning | Suggested value |
|-----------|---------|----------------|
| window | Chunk size in words | 100 - 200 |
| stride | Advance per step in words | half the window |

---

### 04 - LLM Reranker RAG

Adds a semantic reranking step between BM25 retrieval and answer generation:

1. BM25 retrieves the top-10 candidates (keyword match)
2. The LLM reads each candidate and scores it 0-10 for relevance
3. The top-3 by LLM score are kept
4. Groq generates the final answer using only those three chunks

The reranker catches cases where BM25 returns chunks that share words with
the query but do not actually answer it.

---

### 05 - Map-Reduce RAG

Scans every chunk in the knowledge base, not just the top-k:

- MAP: for each chunk, the LLM extracts relevant information or returns
  "NO_RELEVANT_INFO" if the chunk is unrelated
- REDUCE: all relevant extracts are combined by the LLM into a single
  coherent answer

Use Map-Reduce when you need comprehensive coverage of a long document
or when you cannot predict which section will contain the answer.

Cost comparison:

| Approach | LLM calls per query |
|----------|---------------------|
| Direct retrieval (notebooks 01-02) | 0 |
| BM25 + generation (notebook 03) | 1 |
| Reranker RAG (notebook 04) | N candidates + 1 |
| Map-Reduce RAG (notebook 05) | N chunks + 1 |

---

## Core Concepts Reference

| Concept | Notebook | What it does |
|---------|----------|-------------|
| TF (Term Frequency) | 01 | How often a word appears in one chunk |
| IDF (Inverse Document Frequency) | 01 | How rare a word is across all chunks |
| TF-IDF score | 01 | TF * IDF summed over query words |
| BM25 | 02 | TF-IDF with length normalization and term saturation cap |
| Sliding window | 03 | Overlapping chunks to avoid boundary gaps |
| Stride | 03 | How many words to advance before starting the next chunk |
| LLM reranking | 04 | LLM scores each candidate chunk 0-10 for relevance |
| MAP step | 05 | LLM extracts relevant info from each chunk independently |
| REDUCE step | 05 | LLM combines all extracts into one final answer |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `ModuleNotFoundError: rank_bm25` | Run `pip install rank-bm25` with the venv active |
| `GROQ_API_KEY not set` | Check that `.env` exists and contains the key |
| Empty answer from Map-Reduce | The query may not match any chunk; try a broader question |
| Reranker always scores 0 | Check that the LLM is returning valid JSON with a `score` key |
| Slow Map-Reduce | Reduce the number of chunks by increasing `chunk_words` |

---

## What to Explore Next

- Hybrid search: combine BM25 scores with dense vector scores for best of both worlds
- Sparse vectors: SPLADE and BM25-based sparse embeddings
- Cross-encoder reranking: dedicated reranking models instead of a general LLM
- Conversational RAG: pass chat history so follow-up questions work correctly
- Evaluation: measure precision and recall of each retrieval method on a test set
