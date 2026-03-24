---
name: mcp-context7
description: Fetch up-to-date documentation, API references, and context for third-party libraries, frameworks, and tools. Use this whenever the user asks how to use a specific npm package, Python library, or API, needs syntax examples, or wants to resolve deprecation warnings with the latest best practices.
context: coding, documentation
compatibility: Uses the `@upstash/context7-mcp` MCP Server. If unavailable, fall back to searching the web or reading local `node_modules`/`README.md` files.
---

# Context7 (Library Documentation)

## Tools

| Tool | Use For |
|------|---------|
| `resolve-library-id` | Find the exact internal ID or canonical name of a library/framework before querying its docs. |
| `query-docs` | Retrieve specific documentation, API references, or code snippets for a resolved library ID based on a user query. |

## When to Use

- **Learning a New Package** — "How do I set up `@tanstack/react-query` v5?"
- **Checking API Syntax** — "What are the parameters for the `connect` method in the `redis` npm package?"
- **Resolving Deprecations** — "The `useHistory` hook is deprecated in React Router, what should I use instead?"
- **Finding Best Practices** — "What is the recommended way to handle errors in Axios?"
- **Verifying Capabilities** — "Does `date-fns` support timezone conversions?"

## Usage Effectiveness & Best Practices

Using Context7 is a two-step process: you must first identify the library, then ask a specific question about it.

```
# ❌ Bad Querying (Guessing IDs or being vague)
Calling `query-docs` with ID: "react-router" and Query: "how to route" (Without resolving the ID first, it might fail or return the wrong version).

# ✅ Good Querying (Structured & Specific)
1. Call `resolve-library-id` with "react router dom". (Returns ID: e.g., "react-router-v6")
2. Call `query-docs` with ID: "react-router-v6" and Query: "How to use createBrowserRouter and RouterProvider for data fetching"
```

### Querying Strategy
- **Be Specific:** Instead of asking "overview", ask for the specific module or function you need (e.g., "Zod object validation with custom error messages").
- **Mention Versions:** If the user specifies a version (e.g., Next.js 14 App Router vs Pages Router), include that context in your query.
- **Combine with Filesystem:** If you see an unfamiliar import in the user's code (via `mcp-filesystem`), use Context7 to read its docs before attempting to modify the code.

## Why Use This

- **Prevents Hallucinations** — LLMs often invent methods for libraries or mix up syntax between versions (e.g., React Router v5 vs v6). This skill grounds your code in reality.
- **Saves Time** — You don't need to ask the user to copy-paste documentation from their browser into the chat.
- **Always Up-to-Date** — Accesses the latest documentation, which is crucial for fast-moving ecosystems like JavaScript and Python.

## CLI Alternative

If the Context7 MCP server is unavailable, you can try to gather context locally or via standard tools:

| MCP Action | Alternative Action |
|------------|--------------------|
| `query-docs` | Read the local `README.md` in `node_modules/<package-name>/` using `read_file` |
| `query-docs` | Check the package's type definitions (`index.d.ts`) using `read_file` to understand the API surface |
```