# Assignment 3 — Semantic Search RAG vs Moment RAG

## Assignment

Build a RAG system in two stages using a YouTube video transcript:

- **Part 1:** Baseline Semantic Search RAG (fixed-size chunks, no timestamps)
- **Part 2:** Moment RAG (timestamp-aware segments, cites video timestamps in answers)

Reference repo: https://github.com/hamzafarooq/multi-agent-course/blob/main/FDE-01-assignments/Assignment_3_Moment_Search_Scaled/README.md

---

## Video

**YouTube:** https://www.youtube.com/watch?v=U8YI5S_WAY4  
**Video ID:** `U8YI5S_WAY4`  
**Language:** English  
**Transcript segments:** 107 raw segments fetched via `youtube-transcript-api`

---

## Stack

| Layer | Library / Service |
|---|---|
| Transcript | `youtube-transcript-api>=1.2.4` |
| Embeddings | OpenRouter → `text-embedding-3-small` (OpenAI) |
| LLM (chat) | OpenRouter → `openai/gpt-4o-mini` |
| Vector index | `faiss-cpu==1.8.0` (IndexFlatIP, cosine via L2 normalization) |
| Tokenizer | `tiktoken==0.7.0` (cl100k_base) |
| Notebook | `jupyter==1.1.1` |

All code lives in [`notebook.ipynb`](notebook.ipynb).

---

## Environment Setup

```bash
# 1. Create virtualenv
python -m venv .venv
source .venv/bin/activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Configure API key
cp .env.example .env
# Edit .env and set OPENROUTER_API_KEY=sk-or-...

# 4. Run
jupyter notebook notebook.ipynb
```

`.env` is gitignored. `.env.example` shows the required keys.

---

## Architecture

### Part 1 — Baseline RAG

```
raw transcript → join all text → fixed chunks (300 tokens, 50 overlap)
→ embed chunks → FAISS index → cosine search → LLM answer
```

- **4 chunks** from 107 segments
- No timestamps preserved
- Chunks may cut mid-sentence at token boundaries

### Part 2 — Moment RAG

```
raw transcript → group segments into moments (10 segs OR gap > 3s)
→ each moment carries {text, start_sec, end_sec, timestamp}
→ embed moments → FAISS index → cosine search
→ LLM answer citing [M:SS] timestamps
```

- **11 moments** from 107 segments
- Natural speech pauses define boundaries
- Retrieved context includes `[4:23]`-style timestamps
- User can jump directly to the cited video position

---

## Test Queries

```
1. "What is a transformer and how does attention work?"
2. "How are LLMs trained and what data do they use?"
3. "What are the security risks of LLMs like prompt injection?"
```

---

## Comparison Summary

| Dimension | Baseline RAG | Moment RAG |
|---|---|---|
| Chunking | Fixed (~300 tokens) | Temporal coherence (~10 segs or silence gap) |
| Metadata | Plain text only | Text + start/end timestamps |
| Answer cites timestamps | No | Yes — e.g. `[4:23]` |
| Traceability | None | High — user can seek to exact moment |
| Fragmentation risk | Cuts mid-sentence | Respects natural speech pauses |
| Index size | 4 vectors | 11 vectors |
| API cost | Equal | Equal |

### Limitations

- Long moments dilute embedding specificity
- No cross-encoder reranker (unlike original Moment RAG)
- Text-only — no visual branch (CLIP over frames)

### Possible Improvements

- Add reranker: `cross-encoder/ms-marco-MiniLM-L-6-v2`
- Use Whisper for videos without auto-captions
- Expand to multi-source (PDF + slides) per ARGUS architecture

---

## Rules

- All code, comments, and code print must be in **English**
- Do not assume — verify before stating
- Responses must be short and objective
