# Chunking Strategy for Different PDF Types (MVP)

This document defines how to chunk different types of financial PDFs for the RAG chatbot.  
Not all PDFs follow the same structure, so using one single chunking size will result in
poor retrieval accuracy.  
Below are the optimized rules for each type.

---

# 1. Public Announcements (Text-Heavy PDFs)

Examples:
- Public-Announcement.pdf

Characteristics:
- Long paragraphs
- Continuous regulatory text
- Minimal tables
- Mostly text without visuals

Chunking Rules:
- Chunk size: 800–1200 characters
- Overlap: 150–200 characters
- Method: Simple character-based chunking
- Pre-cleaning:
  - Remove headers/footers
  - Merge broken lines
  - Normalize spaces
  - Remove page numbers

Reason:
Text is continuous → large chunks preserve context and prevent fragmentation.

---

# 2. Financial Results (Text + Tables)

Examples:
- Quarterly financial results
- Audited reports
- Earnings summaries

Characteristics:
- Many tables with numeric data
- Rows represent key metrics (e.g., Net Profit, EBITDA)
- Small text paragraphs
- High risk of mixing unrelated numbers

Chunking Rules:
- Chunk size (text): 500–800 characters
- Overlap: 80–150 characters
- For tables:
  - Each row becomes **one chunk**
  - Preserve column-wise structure
  - Store row name in metadata

Table Metadata Example:
{
"section": "Financial Results",
"row_name": "Net Profit / (Loss)",
"unit": "Rs in lakhs"
}
Reason:
Tables must not be split randomly.  
Row-level chunking keeps numerical accuracy and improves retrieval.

---

# 3. Investor Presentations (Slide-Based PDFs)

Examples:
- investorpresentation.pdf

Characteristics:
- Slides with short bullet points
- Titles + subpoints
- Very small text blocks
- Many visuals but little dense text

Chunking Rules:
- **Do not use character-based chunking**
- **One slide = one chunk**
- Overlap: 0
- Extract:
  - Slide title
  - Bullet points
  - Small text blocks

Slide Metadata Example:
{
"page_number": 5,
"slide_title": "Q2FY25 Performance Summary",
"document_type": "investor_presentation"
}


Reason:
Slides already contain small, meaningful information units.
Splitting them further destroys context.

---

# Summary Table

| PDF Type | Chunk Size | Overlap | Chunking Method |
|----------|------------|---------|------------------|
| Public announcements | 800–1200 chars | 150–200 | Character-based |
| Financial results | 500–800 chars | 80–150 | Hybrid (text + table rows) |
| Investor presentations | Entire slide | 0 | Slide-based |

---

# Future Enhancements (Optional)
- Automatic PDF type detection (layout analysis)
- Detect tables and extract using OCR if needed
- Chunk based on semantic similarity (advanced)
- Add chunk quality scoring

---

This file ensures each PDF type is chunked optimally, improving retrieval accuracy and
reducing hallucinations during Q&A.
