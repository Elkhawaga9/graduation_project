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

**Stage:** Data preprocessing & chunking  
**Status:** ✅ Qwen 7B LLM Inference (Without RAG)

Last update: **Dec 24, 2025**

---

## Folder Structure

### `data_raw/`
Original raw data.
- `Computer Systems A Programmers Perspective by Randal E. Bryant, David R. OHallaron (z-lib.org).pdf` – Source PDF.  
- `data_khawaga_4_10.csv` – Additional dataset.

### `data_json/`
JSON representations of text chunks from PDFs.
- `*_chunks.json` – Raw text chunks extracted from PDFs.

### `embeddings/`
Vector embeddings for enriched chunks.
- `*_chunks_enriched_embeddings.npy` – Embeddings generated from enriched JSON chunks.

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
Stores vector databases for retrieval (e.g., FAISS, Milvus, Chroma).

### `notebooks/`
Jupyter notebooks for preprocessing, embedding, and analysis.
- `01_preprocessing.ipynb` – Initial data cleaning.  
- `01_single_pdf_processing.ipynb` – Chunking PDFs into JSON.  
- `02_llm_deepseekr1t2_chunk_enrichment.ipynb` – LLM-assisted enrichment.  
- `03_generate_embeddings.ipynb` – Create embeddings from enriched JSON.  
- `04_store_embeddings.ipynb` – Save embeddings for vector storage.

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

### ✅ Step 3 — LLM-Assisted Chunk Enrichment

**Notebook:**  
`notebooks/02_llm_deepseekr1t2_chunk_enrichment.ipynb`

**Goal:**  
Enhance paragraph-aware chunks using DeepSeek R1T2 Chimera for RAG preprocessing, improving retrieval quality before embeddings.

**Input:**
- JSON chunks from Step 2
- Each chunk contains raw paragraph-level text

**Processing:**
- Send chunks to DeepSeek R1T2 Chimera (free via OpenRouter) using structured prompts
- Extract and/or generate:
  - Cleaned and normalized text
  - High-level topic labels (e.g., Memory, CPU, Cache, OS)
  - Subtopics (e.g., Virtual Memory, Pipelining, Cache Coherence)
- Validate and normalize LLM output into a consistent JSON schema

**Outputs:**
- **Raw LLM responses:** `raw_llm_responses/<pdf_name>_raw.json`  
  - Stores full LLM outputs per chunk
- **Parsed/enriched JSON:** `json_llm_responses/<pdf_name>_chunks_enriched.json`  
  - Structured JSON with cleaned text, topic, subtopic

**Example Output JSON:**
```json
{
  "id": 1,
  "source": "example.pdf",
  "original_text": "chunk text here",
  "llm_response": "cleaned text + topic/subtopic"
}
```
---
## ✅ Step 4 — Embeddings (Completed)

**Goal:**  
Convert enriched JSON chunks into embeddings for RAG retrieval.

**Input:**  
- JSON chunks from `json_llm_responses/`

**Processing:**  
- Select which JSON file to embed
- Generate embeddings using a sentensetransformer
- Save embeddings locally in `embeddings/` folder
- Each JSON file can have a separate embedding file

**Output:**  
- `.pkl` or `.parquet` files in `grad_embeddings/`
- Embeddings ready for vector storage in FAISS or Chroma

**Notes:**  
- PDFs and JSON files remain untracked in Git
- Supports free-tier embedding models to minimize cost

---
### ✅ Step 5 — Vector Store (Updated)

**Goal:** Store embeddings in a vector database for efficient retrieval.

**Implementation:**  
- Using **Chroma** as the vector store (persistent).  
- All enriched chunks from PDFs are embedded and stored in Chroma.  
- Metadata (`source`, `topic`, `subtopic`) has been normalized to primitive types (strings) for Chroma compatibility.  
- Supports **incremental updates** — new PDFs can be added safely without overwriting existing embeddings.  
- Retrieval tested and ready for the RAG pipeline.

**Next Actions:**  
- Integrate with RAG pipeline to retrieve top-k chunks per query.  
- Use stored embeddings with **Qwen** or other LLMs to generate grounded answers.  
- Optionally add metadata filtering to improve retrieval relevance.

---

## ✅ Step 6 — Qwen 7B LLM Inference (Without RAG)

**Goal:**  
Establish a baseline question-answering system using Qwen 7B without external knowledge augmentation.

**Current Status:**  
- ✅ Qwen 7B model successfully launched locally  
- ✅ Inference notebook implemented and tested  
- ✅ Model generates answers using only internal knowledge

**Completed Work:**  
- Loaded Qwen 7B model and tokenizer  
- Implemented prompt-based question answering  
- Evaluated responses on textbook-style questions  
- Recorded baseline performance (accuracy, verbosity, hallucination tendency)

**Outcome:**  
- Working standalone LLM pipeline  
- Baseline results for later comparison with RAG-enhanced inference

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
* **Threshold Tuning:** Monitor the `0.85` similarity threshold. If too many correct answers are being sent to the LLM (wasting time), lower it to `0.80`.
* **Edge Case Analysis:** Review the `graded_results.csv` specifically for grades of `0.5` and `0.75` to ensure the RAG context is providing enough detail for partial credit.



# ⏳ Step 8 — Evaluation

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
