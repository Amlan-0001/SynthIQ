# SynthIQ — Agentic Multi-Source Research Synthesizer

> **It doesn't just answer your question — it researches it the way a human analyst would.**

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![LangChain](https://img.shields.io/badge/LangChain-0.2+-1C3C3C?style=flat&logo=langchain&logoColor=white)](https://langchain.com)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.111+-009688?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Streamlit](https://img.shields.io/badge/Streamlit-1.35+-FF4B4B?style=flat&logo=streamlit&logoColor=white)](https://streamlit.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> ⚠️ **Status: Under Active Development** — Architecture and documentation are finalized. Core implementation is in progress. Star the repo to follow updates.

---

## What is SynthIQ?

Most RAG (Retrieval-Augmented Generation) systems work like this:

```
User Question → Search → Retrieve Chunks → LLM Answer
```

**SynthIQ works like a human research analyst:**

```
User Question
    ↓
Query Decomposition (breaks into sub-questions)
    ↓
Multi-Source Retrieval (Web + Wikipedia + PDFs) per sub-question
    ↓
Contradiction Detection (flags conflicts across sources)
    ↓
Synthesis + Citation
    ↓
RAGAs Self-Evaluation (faithfulness + relevance scoring)
    ↓
Final Cited, Quality-Scored Answer
```

---

## The Problem with Existing RAG Systems

| Problem | Standard RAG | SynthIQ |
|---|---|---|
| Complex multi-part questions | Answers partially or misses sub-topics | Decomposes into sub-questions, answers each |
| Single source retrieval | One source, potential bias | Web + Wikipedia + PDF simultaneously |
| Conflicting information | Silently picks one source | Explicitly flags contradictions |
| No quality measurement | No way to trust output | RAGAs faithfulness + relevance scoring |
| No source transparency | Vague citations | Every claim linked to exact source |

---

## Key Features

- **Agentic Query Decomposition** — Automatically breaks complex questions into targeted sub-questions using LangChain structured output chains
- **Multi-Source Retrieval** — Simultaneously fetches from web (BeautifulSoup), Wikipedia API, and user-uploaded PDFs
- **Contradiction Detection** — Identifies and surfaces conflicting claims across different sources instead of silently resolving them
- **RAGAs Evaluation** — Self-evaluates output quality using faithfulness and context relevance metrics
- **Source Citations** — Every synthesized claim is traceable back to its original source
- **FAISS Vector Store** — Efficient semantic similarity search across all ingested content
- **FastAPI Backend** — Clean REST API separating retrieval logic from the UI layer
- **Streamlit Frontend** — Interactive UI with real-time retrieval status and quality scores

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Streamlit UI                      │
│         (Query Input + Results Display)             │
└──────────────────────┬──────────────────────────────┘
                       │ HTTP
┌──────────────────────▼──────────────────────────────┐
│                  FastAPI Backend                    │
│                                                     │
│  ┌─────────────────────────────────────────────┐   │
│  │         Query Decomposition Agent           │   │
│  │   (LangChain Structured Output Chain)       │   │
│  └──────────┬──────────────────────────────────┘   │
│             │ Sub-questions                         │
│  ┌──────────▼──────────────────────────────────┐   │
│  │       Multi-Source Retriever                │   │
│  │  ┌──────────┐ ┌───────────┐ ┌───────────┐  │   │
│  │  │   Web    │ │ Wikipedia │ │    PDF    │  │   │
│  │  │Scraper   │ │    API    │ │  Upload   │  │   │
│  │  └────┬─────┘ └─────┬─────┘ └─────┬─────┘  │   │
│  └───────┼─────────────┼─────────────┼─────────┘   │
│          │             │             │              │
│  ┌───────▼─────────────▼─────────────▼─────────┐   │
│  │            FAISS Vector Store               │   │
│  │      (Sentence-Transformers Embeddings)     │   │
│  └──────────────────────┬──────────────────────┘   │
│                         │                           │
│  ┌──────────────────────▼──────────────────────┐   │
│  │        Contradiction Detector               │   │
│  │    (Cross-source claim comparison)          │   │
│  └──────────────────────┬──────────────────────┘   │
│                         │                           │
│  ┌──────────────────────▼──────────────────────┐   │
│  │         Groq LLM (Synthesis Layer)          │   │
│  │       (llama-3.1-70b-versatile)             │   │
│  └──────────────────────┬──────────────────────┘   │
│                         │                           │
│  ┌──────────────────────▼──────────────────────┐   │
│  │          RAGAs Evaluation Layer             │   │
│  │    (Faithfulness + Context Relevance)       │   │
│  └──────────────────────┬──────────────────────┘   │
└──────────────────────────┼──────────────────────────┘
                           │
              Final Cited + Scored Answer
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| LLM | Groq API (llama-3.1-70b-versatile) |
| Orchestration | LangChain 0.2+ |
| Vector Store | FAISS |
| Embeddings | Sentence-Transformers (all-MiniLM-L6-v2) |
| Web Retrieval | BeautifulSoup4, requests |
| Knowledge Base | Wikipedia API |
| Evaluation | RAGAs |
| Backend | FastAPI + Uvicorn |
| Frontend | Streamlit |
| Language | Python 3.10+ |

---

## Project Structure

```
SynthIQ/
│
├── backend/
│   ├── main.py                  # FastAPI app entry point
│   ├── decomposer.py            # Query decomposition agent
│   ├── retriever.py             # Multi-source retrieval logic
│   ├── vector_store.py          # FAISS indexing and search
│   ├── contradiction.py         # Cross-source conflict detection
│   ├── synthesizer.py           # LLM synthesis + citation
│   └── evaluator.py             # RAGAs evaluation pipeline
│
├── frontend/
│   └── app.py                   # Streamlit UI
│
├── utils/
│   ├── web_scraper.py           # BeautifulSoup web fetcher
│   ├── wiki_fetcher.py          # Wikipedia API wrapper
│   └── pdf_parser.py            # PDF ingestion and chunking
│
├── requirements.txt
├── .env.example
└── README.md
```

---

## Installation & Setup

### 1. Clone the repository
```bash
git clone https://github.com/Amlan-0001/SynthIQ.git
cd SynthIQ
```

### 2. Create virtual environment
```bash
python -m venv venv
source venv/bin/activate        # Linux/Mac
venv\Scripts\activate           # Windows
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Set up environment variables
```bash
cp .env.example .env
```
Edit `.env` and add your keys:
```
GROQ_API_KEY=your_groq_api_key_here
```
Get a free Groq API key at [console.groq.com](https://console.groq.com)

### 5. Run the backend
```bash
uvicorn backend.main:app --reload
```

### 6. Run the frontend (new terminal)
```bash
streamlit run frontend/app.py
```

---

## Usage

1. Open the Streamlit UI at `http://localhost:8501`
2. Type any research question (works best with complex, multi-faceted questions)
3. Optionally upload one or more PDF documents as additional knowledge sources
4. Hit **Synthesize** — watch the pipeline run in real time
5. Review the final answer with:
   - Source citations per claim
   - Contradiction flags (if any sources conflict)
   - RAGAs quality scores (faithfulness + relevance)

**Example queries that showcase SynthIQ's strength:**
- *"What are the current limitations and breakthroughs in quantum computing?"*
- *"How does transformer architecture differ from RNN and what are the trade-offs?"*
- *"What is the current regulatory landscape for AI in the EU and US?"*

---

## What Makes SynthIQ Different

### 1. Query Decomposition
Standard RAG treats *"What are the latest breakthroughs in quantum computing and its business applications?"* as one query — often returning generic results.

SynthIQ decomposes it:
- *"What are recent quantum computing breakthroughs 2024-25?"*
- *"What companies are leading quantum computing research?"*
- *"What are real-world business applications of quantum computing?"*

Each sub-question is retrieved independently, then synthesized together.

### 2. Contradiction Detection
If Wikipedia says *"GPT-4 was released in March 2023"* and a web source says *"GPT-4 launched in late 2023"* — standard RAG picks one silently.

SynthIQ surfaces this:
```
⚠️  CONFLICT DETECTED
Source A (Wikipedia): "GPT-4 was released in March 2023"
Source B (TechCrunch): "GPT-4 launched in late 2023"
→ Verify independently before citing.
```

### 3. RAGAs Self-Evaluation
Every response comes with:
- **Faithfulness Score** (0–1): Is the answer grounded in retrieved content?
- **Context Relevance Score** (0–1): Did retrieval surface the right information?

Low scores automatically trigger a re-retrieval attempt.

---

## Novelty Over Existing RAG Systems

| Feature | LangChain Naive RAG | LlamaIndex | SynthIQ |
|---|---|---|---|
| Query decomposition | ❌ | Partial | ✅ |
| Multi-source simultaneous retrieval | ❌ | ❌ | ✅ |
| Contradiction detection | ❌ | ❌ | ✅ |
| Self-evaluation (RAGAs) | ❌ | ❌ | ✅ |
| Source-level citations | Partial | Partial | ✅ |

---

## Requirements

```
langchain>=0.2.0
langchain-community
langchain-groq
faiss-cpu
sentence-transformers
ragas
fastapi
uvicorn
streamlit
beautifulsoup4
requests
wikipedia-api
pypdf
python-dotenv
```

---

## Future Improvements

- [ ] Add memory across sessions for multi-turn research conversations
- [ ] Support ArXiv and PubMed as additional sources for academic queries
- [ ] Add graph-based knowledge representation for entity linking
- [ ] Export research report as PDF with full citations
- [ ] Add support for local LLMs via Ollama (offline mode)

---

## Author

**N M Amlan**
- GitHub: [@Amlan-0001](https://github.com/Amlan-0001)
- LinkedIn: [N M Amlan](https://www.linkedin.com/in/n-m-amlan-a05b3027a)

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

*Built as part of ongoing AI/ML research portfolio. Contributions and feedback welcome.*
