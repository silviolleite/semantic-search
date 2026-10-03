# Assignment 3 — Semantic Search RAG vs Moment RAG

Comparison of two RAG pipelines built on a YouTube video transcript.

- **Part 1:** Baseline semantic search (fixed-size chunks, no timestamps)
- **Part 2:** Moment RAG (timestamp-aware segments, answers cite `[M:SS]`)

**Slide deck:** [slides.html](slides.html) — open in any browser.

---

## Setup

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

cp .env.example .env
# Edit .env and set: OPENROUTER_API_KEY=sk-or-...
```

## Run

```bash
jupyter notebook notebook.ipynb
# Kernel → Restart & Run All
```

## Video

[youtube.com/watch?v=U8YI5S_WAY4](https://www.youtube.com/watch?v=U8YI5S_WAY4) — "Tools of the Trade" (~8 min, English captions)

## Stack

| Layer | Library |
|---|---|
| Transcript | `youtube-transcript-api` |
| Embeddings | OpenRouter → `text-embedding-3-small` |
| LLM | OpenRouter → `gpt-4o-mini` |
| Vector index | `faiss-cpu` (cosine via L2 norm) |
| Tokenizer | `tiktoken` (cl100k_base) |
