# Ingestion Pipeline (Final Version)

This document describes the complete pipeline for ingesting any financial PDF into the
RAG chatbot system. The goal is to convert raw PDFs into clean, indexed chunks stored
inside Chroma DB with correct metadata.

The ingestion flow adjusts automatically based on the type of PDF:
- Public announcements
- Financial results (tables + text)
- Investor presentations (slides)

---

# 1. File Upload

1. User uploads a PDF through the UI or API.
2. The file is saved into:
   /data/uploads/<timestamp>_<filename>.pdf
3. A unique doc_id is assigned to the file.

Example:
doc_id = "public_announcement_2025_01"

---

# 2. PDF Type Detection

The system determines which chunking strategy to use based on:
- Layout analysis
- Percentage of tables vs text
- Slide formatting patterns
- Keyword scanning

PDF Types:
1. text-heavy → public announcement strategy  
2. table-heavy → financial results strategy  
3. slide-style → investor presentation strategy  

---

# 3. Text Extraction

Steps:
1. Extract text page by page.
2. Extract layout (tables, headings, bullet points).
3. Identify tables using PDFMiner / PyMuPDF structure.
4. Store text temporarily in:
   /data/processed/<doc_id>_raw_text.json

---

# 4. Text Cleaning

Apply cleaning rules:
- Remove headers, footers, and repeated elements
- Remove page numbers
- Normalize whitespace
- Fix broken lines
- Remove empty lines
- For tables: keep row structure intact

Output:
Cleaned text per page + table objects.

---

# 5. Chunking

Chunking rules depend on PDF type:

### Public Announcements
- 800–1200 characters
- 150–200 overlap

### Financial Results
- Normal text → 500–800 chars
- Tables → one table row per chunk

### Investor Presentations
- One slide = one chunk
- No overlap

Chunk format stored as:

