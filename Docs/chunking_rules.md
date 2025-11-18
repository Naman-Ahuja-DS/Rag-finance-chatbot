# Chunking Rules (MVP)

- Chunk size: ~1000 characters
- Chunk overlap: 200 characters

## Why these values:
- Helps preserve context
- Avoids cutting sentences and tables
- Improves embedding + retrieval performance

## Pre-chunk cleaning:
- remove repeated section titles
- remove page numbers
- remove headers/footers
- normalize whitespace

## Chunk format:
[Source: <filename>, Page: <page_number>]

<cleaned text>

## Reject chunks:
- chunks under 20 characters
- chunks that contain only numbers
- duplicate chunks

