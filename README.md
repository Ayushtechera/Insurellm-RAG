# Insurellm Insight

A **RAG-powered chatbot** that answers questions about Insurellm — an insurance-tech company — using intelligent document chunking, semantic search, reranking, and query rewriting.

## Overview

Insurellm Insight ingests Insurellm's internal knowledge base (employee records, product docs, company info) and lets users ask natural-language questions through a simple chat interface. Instead of naive vector search, it uses an LLM to **semantically chunk documents**, **rewrite user queries** for better retrieval, and **rerank retrieved chunks** before generating a grounded answer — with full source citations shown alongside the response.

## Features

- 🧩 **Smart Chunking** — LLM splits documents into overlapping, headline + summary + original-text chunks for higher-quality retrieval
- 🔍 **Query Rewriting** — user questions are refined into precise search queries before hitting the vector store
- 📊 **Reranking** — retrieved chunks are re-ordered by relevance using an LLM ranker
- 📚 **Source Transparency** — every answer displays the exact knowledge-base files it was grounded in
- ⚡ **Multi-provider LLM support** via LiteLLM (Mistral, OpenAI, and others)

## Tech Stack

| Component        | Tool                          |
|-------------------|-------------------------------|
| LLM Orchestration | [LiteLLM](https://github.com/BerriAI/litellm) |
| Vector Store      | [ChromaDB](https://www.trychroma.com/) |
| Embeddings        | HuggingFace `all-MiniLM-L6-v2` |
| Schema Validation | Pydantic |
| LLM Provider      | Mistral (`ministral-3b-latest`) |

## How It Works

1. **Ingestion** — Markdown documents from the knowledge base are loaded and split into overlapping semantic chunks by an LLM, then embedded and stored in ChromaDB.
2. **Query Rewriting** — the user's question is rewritten into a short, targeted search query.
3. **Retrieval** — both the original and rewritten queries are used to fetch candidate chunks from the vector store.
4. **Reranking** — an LLM reorders the merged chunks by true relevance to the question.
5. **Answer Generation** — the top chunks are passed as context to the LLM, which generates a grounded, cited answer.

## Screenshots

### Asking about a person
<img width="915" height="480" alt="Screenshot 2026-07-20 200027" src="https://github.com/user-attachments/assets/0d6fe32d-1e0a-4cb5-bf64-36f1575e86e0" />

### Follow-up question with retrieved context panel
<img width="915" height="480" alt="Screenshot 2026-07-20 200110" src="https://github.com/user-attachments/assets/1dccf445-0ce0-47f5-ba61-ee39b275e237" />


> The right-hand panel shows the exact source documents and chunks used to generate each answer.

## Setup

```bash
# install dependencies
uv pip install -r requirements.txt

# set your API key
echo "MISTRAL_API_KEY=your_key_here" > .env

# run ingestion
python ingest.py

# start the app
python app.py
```

## Project Structure

```
├── knowledge-base/       # Source markdown documents
├── preprocessed_db/      # ChromaDB persistent store
├── ingest.py             # Document chunking + embedding pipeline
├── rag.py                # Query rewriting, retrieval, reranking, answering
└── app.py                # Chat UI
```

## Notes

- If you hit rate limits during ingestion, reduce `WORKERS` to `1` in `ingest.py`.
- Model string uses `ministral-3b-latest`; prefixing with `mistral/` avoids LiteLLM provider-detection warnings.
