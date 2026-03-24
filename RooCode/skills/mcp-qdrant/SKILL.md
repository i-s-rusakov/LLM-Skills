---
name: mcp-qdrant
description: Interact with the local Qdrant vector database to perform semantic search, manage collections, and retrieve contextually similar documents or code snippets. Use this when the user wants to find information based on meaning rather than exact keywords, or when working with RAG (Retrieval-Augmented Generation) features.
context: database, search
compatibility: Uses the `mcp-server-qdrant` MCP Server connected to `http://localhost:6333`. If unavailable, fall back to making direct REST API calls to the Qdrant instance using `curl`.
---

# Qdrant Vector Database

## Tools

| Tool | Use For |
|------|---------|
| **Search & Retrieval** | |
| `search_points` | Perform a semantic similarity search to find documents/code that match the *meaning* of a query. |
| `scroll_points` | Retrieve points (records) sequentially, often used with payload filters for exact metadata matches. |
| **Data Management** | |
| `upsert_points` | Insert new vectors/documents or update existing ones in the collection. |
| `delete_points` | Remove specific points from the database by their IDs or payload filters. |
| **Collection Management** | |
| `list_collections` | See all available vector collections in the database. |
| `get_collection` | Get metadata about a specific collection (e.g., vector size, distance metric, point count). |
| `create_collection` | Initialize a new collection with specific vector dimensions and distance metrics. |

## Environment Context

Based on the current configuration, the server is connected to:
- **URL:** `http://localhost:6333`
- **Target Collection:** `ws-96a4875a4210a4d9`
*(Note: Always default to this collection unless the user specifies otherwise).*

## When to Use

- **Semantic Search** — "Find code related to user authentication" (Even if the code uses words like "login", "session", or "jwt" instead of "authentication").
- **Knowledge Retrieval (RAG)** — "Search the vector database for the company's deployment guidelines."
- **Deduplication** — "Find if we already have a utility function that does deep object merging."
- **Data Ingestion** — "Process this markdown documentation and upsert it into Qdrant for future semantic queries."

## Usage Effectiveness & Best Practices

Vector search works differently from standard text search (`grep`). It relies on embeddings (mathematical representations of text).

```
# ❌ Bad Querying (Keyword focused)
Querying `search_points` with exact syntax like: "function login(req, res)" or "import { useState }". 
Vector DBs might struggle if the embedding model is optimized for natural language.

# ✅ Good Querying (Semantic focused)
Querying `search_points` with natural language descriptions: "Express.js login controller handling user sessions" or "React component managing local state".
```

### Filtering Strategy
When you need both semantic meaning AND exact matches, use **Payload Filters**.
- **Example:** You want to find "database connection logic" (Semantic), but ONLY in "Python" files (Exact metadata). 
- Pass the semantic query for the vector search, and apply a filter on the `language: "python"` payload.

## Why Use This

- **Understands Intent** — Finds relevant context even when the exact terminology differs.
- **Scalability** — Can instantly search through millions of code snippets or documentation pages.
- **Context Enrichment** — Allows Roo Code to pull in highly relevant external knowledge that doesn't fit in the standard context window.

## CLI Alternative

If the Qdrant MCP server is unavailable, you can interact with the Qdrant REST API directly using `curl` from the terminal:

| MCP Action | CLI Equivalent (REST API) |
|------------|---------------------------|
| `get_collection` | `curl http://localhost:6333/collections/ws-96a4875a4210a4d9` |
| `search_points` | `curl -X POST http://localhost:6333/collections/ws-96a4875a4210a4d9/points/search -H "Content-Type: application/json" -d '{"vector": [...], "limit": 3}'` |
```