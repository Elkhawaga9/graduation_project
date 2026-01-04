# Graduation Project – Progress Log

This file tracks the **technical progress** of my graduation project.
It is updated after completing each major step so anyone can clearly see
what is done and what is planned next.

---

## Project Overview

**Title:** RAG-based Question Answering for Computer Systems  
**Approach:** Retrieval-Augmented Generation (RAG)  
**LLM (planned):** Qwen 7B  
**Domain:** Computer Systems (textbooks, PDFs)

---

## Current Status

**Stage:** RAG Pipeline Implementation  
**Status:** ✅ Vector Database Ready & Retrieval Verified

Last update: **Jan 05, 2026**

---

## Folder Structure

### `data_raw/`
Original raw data.
- `Computer Systems A Programmers Perspective by Randal E. Bryant, David R. OHallaron (z-lib.org).pdf` – Source PDF.  
- `data_khawaga_4_10.csv` – Additional dataset.

### `data_json/`
JSON representations of text chunks from PDFs.
- `*_chunks.json` – Raw text chunks extracted from PDFs.

### `graduation_project/`
Main project folder for scripts, models, or core project code.

### `gradproj_env/`
Virtual environment folder (Python dependencies, configurations).

### `json_llm_responses/`
Structured LLM outputs from chunk enrichment.
- `*_chunks_enriched.json` – Enriched JSON with cleaned text, topics, and subtopics.

### `raw_llm_responses/`
Raw LLM outputs before parsing.
- `*_chunks_raw.json` – Direct LLM responses.

### `vector_store/`
Stores the persistent vector database.
- `chroma_db/` – The ChromaDB collection containing embeddings and metadata.

### `embeddings/`
(Optional) Intermediate folder if saving raw embedding files before ingestion.

### `notebooks/`
Jupyter notebooks for preprocessing, embedding, and analysis.
- `01_single_pdf_processing.ipynb` – Chunking PDFs into JSON.  
- `02_llm_deepseekr1t2_chunk_enrichment.ipynb` – LLM-assisted enrichment.  
- `03_ingest_chroma.ipynb` – Generate embeddings and save to Vector Store.  
- `04_test_retrieval.ipynb` – Diagnostics and query testing.

---

## Completed Steps

### ✅ Step 1 — Project Setup
- GitHub repository initialized
- Feature-branch workflow adopted
- Python virtual environment created
- Project folders organized

---

### ✅ Step 2 — Single PDF Processing

**Notebook:** `notebooks/01_single_pdf_processing.ipynb`

**Description:**
- Lists available PDFs from `data_raw/`
- User selects a PDF interactively
- Loads PDF using `PyPDFLoader`
- Combines all pages into one text to fix broken sentences
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

**Design Notes:**

- Recursive chunking preferred over fixed splits

- Paragraph boundaries prioritized

- Generated data is not committed to GitHub

- Planned Steps

---

### ✅ Step 3 — LLM-Assisted Chunk Enrichment

**Notebook:** `notebooks/02_llm_deepseekr1t2_chunk_enrichment.ipynb`

**Goal:** Enhance paragraph-aware chunks using DeepSeek/Gemini for RAG preprocessing, improving retrieval quality before embeddings.

**Input:**
- JSON chunks from Step 2

**Processing:**
- Cleaned text (removed OCR artifacts like soft hyphens and broken headers)
- Generated high-level **Topics** (e.g., "Chapter 1: Tour of Systems")
- Generated **Subtopics** (e.g., "Amdahl's Law")
- Validated and normalized LLM output into a consistent JSON schema

**Outputs:**
- **Parsed/enriched JSON:** `json_llm_responses/<pdf_name>_chunks_enriched.json`

---

### ✅ Step 4 — Embeddings & Vector Store Ingestion

**Notebook:** `notebooks/03_ingest_chroma.ipynb`

**Goal:** Convert enriched JSON chunks into embeddings and store them directly in the Vector Database in a single pass.

**Input:**
- Enriched JSON chunks from `json_llm_responses/`

**Implementation:**
- **Model:** Used `all-MiniLM-L6-v2` (Hugging Face) for efficient CPU-based embedding.
- **Context Injection:** Combined `Topic > Subtopic` with the `Text` before embedding. This ensures the model understands the semantic context of each paragraph.
- **Storage:** Persisted directly into **ChromaDB** located in `vector_store/chroma_db`.
- **Filtering:** Automatically discarded chunks marked as garbage (Copyright pages, TOC) during ingestion.

**Output:**
- A queryable `chroma_db` folder containing ~3,000 indexed vectors.

---

### ✅ Step 5 — Retrieval Testing & Diagnostics

**Notebook:** `notebooks/04_test_retrieval.ipynb`

**Goal:** Verify that the Vector Database accurately retrieves relevant information before connecting the LLM.

**Implementation:**
- Created an automated health check script.
- Ran specific technical queries (e.g., "What is Amdahl's Law?", "Virtual Memory").
- **Metrics:** Analyzed "Relevance Scores" (L2 Distance).
    - Scores between **0.3 - 0.6** achieved for key concepts (High Accuracy).
- Verified that metadata (`source`, `topic`) is correctly returned with the text.

**Outcome:**
- The "Brain" is verified. The system retrieves the correct textbook chapters for specific technical questions.

---

## ✅ Step 6 — Qwen 7B LLM Inference (Without RAG)

**Goal:** Establish a baseline question-answering system using Qwen 7B without external knowledge augmentation.

**Current Status:**
- ✅ Qwen 7B model successfully launched locally
- ✅ Inference notebook implemented and tested
- ✅ Model generates answers using only internal knowledge

**Completed Work:**
- Loaded Qwen 7B model and tokenizer
- Implemented prompt-based question answering
- Evaluated responses on textbook-style questions

---

## ✅ Step 7 — Hybrid Cascading Grading System (Similarity + RAG)

**Goal:**
Build a cost-efficient, high-accuracy automatic grading pipeline that prioritizes direct matches (Fast Lane) and falls back to RAG-grounded LLM judgment (Slow Lane) for complex answers.

**Current Status:**
* ✅ **Models Loaded:** Qwen 2.5 7B (4-bit) & HuggingFace Embeddings (`all-MiniLM-L6-v2`) active.
* ✅ **Vector Store:** ChromaDB connected and ready for context retrieval.
* ✅ **Logic Implemented:** "Cascading" architecture is coded:
    * *Tier 1:* Hybrid Similarity (Semantic + Lexical) for instant high-confidence grading.
    * *Tier 2:* RAG (MMR Diversity Search) + Qwen 7B for grading nuanced/partial answers.
* ✅ **Input/Output:** Batch CSV processing with JSON-enforced output format is ready.

**Planned Actions:**
* **Run Batch Inference:** Execute the pipeline on the full `students.csv` dataset.
* **Threshold Tuning:** Monitor the `0.85` similarity threshold.
* **Edge Case Analysis:** Review partial credit grades.

---

# ⏳ Step 8 — Evaluation

- Test with Computer Systems questions
- Analyze retrieval and answer quality
- Compare RAG performance against baseline Qwen 7B
### ✅ How to add it to GitHub

```bash
git checkout -b docs_progress
git add PROGRESS.md
git commit -m "Add project progress tracking markdown file"
git push origin docs_progress
```

Then open a PR and merge.
