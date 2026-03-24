---
name: usr-doc-ingestion
description: Transform raw external data (API dumps, TXT docs, website scrapes) into structured, AI-readable specifications within the `./RAG` or `./memory-bank/specifications` folders.
context: data-processing, research
compatibility: Uses `mcp-puppeteer` or `mcp-playwright` to fetch and `mcp-filesystem` to save.
---

# External Doc Ingestion

## Responsibilities
Raw documentation is often too noisy for a context window. You must "ingest" it by cleaning and structuring it.

### Ingestion Steps:
1. **Cleanup**: Remove HTML tags, redundant headers, and legal disclaimers from raw text.
2. **Structuring**: Convert prose into clear Markdown (Tables for API params, Code blocks for examples).
3. **Context Linking**: Add a header explaining which project components use this external data.
4. **Storage**: Place the cleaned version in `./RAG/` and a high-level summary in `./memory-bank/specifications/`.

## When to Use
- **Adding New API**: "Here is a raw dump of the Gate.io websocket docs. Ingest it into our RAG."
- **Research**: "I've scraped the new Binance tax rules. Process them and update our research folder."

## Why Use This
It ensures that when Roo Code (or Qwen/Gemini) reads the RAG, it gets **pure information** without wasting tokens on "noise".