# Graduation Project – Progress Log

This file tracks the **technical progress** of my graduation project.
It is updated after completing each major step so anyone can clearly see
what is done and what is planned next.

---

## Project Overview

**Title:** RAG-based Question Answering for Computer Systems  
**Approach:** Retrieval-Augmented Generation (RAG)  
**LLM (planned):** Mistral 7B  
**Domain:** Computer Systems (textbooks, PDFs)

---

## Current Status

**Stage:** Data preprocessing & chunking  
**Status:** ✅ Single-PDF pipeline completed

Last update: **Dec 17, 2025**

---

## Repository Structure
```text
graduation_project/
├── data_raw/ # Raw PDFs (local only, not pushed)
├── data_json/ # Generated JSON chunks (local only)
├── notebooks/
│ └── 01_single_pdf_processing.ipynb
├── config.json
├── README.md
└── PROGRESS.md
```

---

## Completed Steps

### ✅ Step 1 — Project Setup
- GitHub repository initialized
- Feature-branch workflow adopted
- Python virtual environment created
- Project folders organized

---

### ✅ Step 2 — Single PDF Processing

**Notebook:**  
`notebooks/01_single_pdf_processing.ipynb`

**Description:**
- Lists available PDFs from `data_raw/`
- User selects a PDF interactively
- Loads PDF using `PyPDFLoader`
- Combines all pages into one text
- Applies **paragraph-aware recursive chunking**
- Saves all chunks into **one JSON file per PDF**

**Output Format (JSON):**
```json
{
  "id": 1,
  "source": "example.pdf",
  "text": "chunk text here",
  "topic": "",
  "subtopic": ""
}
```

Design Notes:

Recursive chunking preferred over fixed splits

Paragraph boundaries prioritized

Generated data is not committed to GitHub

Planned Steps

---

## ⏳ Step 3 — LLM-Assisted Chunk Enrichment (Planned)

**Goal:**  
Enhance existing paragraph-aware chunks using an external LLM (e.g. Gemini API)
to improve retrieval quality before embedding.

**Input:**
- JSON chunks generated from Step 2
- Each chunk contains raw paragraph-level text

**Processing:**
- Send chunks to an LLM (Gemini or similar) with a structured prompt
- Extract and/or generate:
  - Cleaned and normalized text
  - High-level topic labels (e.g. Memory, CPU, Cache, OS)
  - Subtopics (e.g. Virtual Memory, Pipelining, Cache Coherence)
  - Optional short summary per chunk
- Validate and normalize LLM output into a consistent JSON schema

**Output Format (JSON):**
```json
{
  "id": 1,
  "source": "example.pdf",
  "text": "cleaned chunk text",
  "topic": "Memory Systems",
  "subtopic": "Cache Organization",
  "summary": "This chunk explains cache structure and mapping techniques."
}
```

# ⏳ Step 4 — Embeddings

Convert chunks into embeddings

Evaluate suitable embedding models

Prepare data for vector storage

# ⏳ Step 5 — Vector Store

Store embeddings in FAISS or Chroma

Implement efficient retrieval

# ⏳ Step 6 — RAG Pipeline

Retrieve top-k chunks

Generate answers using Mistral 7B

Ground responses in retrieved context

# ⏳ Step 7 — Evaluation

Test with Computer Systems questions

Analyze retrieval and answer quality

Notes

PDFs and generated JSON files are intentionally excluded from version control

Each notebook represents a single, clear pipeline stage

This file will be updated continuously throughout the project


---

### ✅ How to add it to GitHub

```bash
git checkout -b docs_progress
git add PROGRESS.md
git commit -m "Add project progress tracking markdown file"
git push origin docs_progress
```

Then open a PR and merge.
