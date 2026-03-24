---
name: mcp-memory
description: Maintain a persistent Knowledge Graph of the project's architecture, key entities, relationships, design decisions, and user preferences. Use this to "remember" context across different sessions, document how parts of the system interact, and store important "gotchas" or rules.
context: workspace
compatibility: Uses the `@modelcontextprotocol/server-memory` MCP Server. If unavailable, fall back to reading/writing a local `.roo-memory.md` or `ARCHITECTURE.md` file via terminal commands.
---

# Memory & Knowledge Graph

## Tools

| Tool | Use For |
|------|---------|
| **Writing / Memorizing** | |
| `create_entities` | Define new core concepts, components, services, or rules (e.g., `AuthService`, `DatabaseConfig`) |
| `create_relations` | Link entities together to show architecture (e.g., `AuthService` -> `DEPENDS_ON` -> `RedisCache`) |
| `add_observations` | Add specific details, facts, or constraints to an existing entity (e.g., "Uses JWT with 1h expiration") |
| **Reading / Recalling** | |
| `search_nodes` | Find specific entities by a text query (best for targeted recall) |
| `open_nodes` | Retrieve specific nodes by name and see their direct relationships and observations |
| `read_graph` | Retrieve the entire knowledge graph (use with caution on large projects) |

## When to Use

- **Project Onboarding** — "What do we know about the authentication flow in this project?"
- **Saving Architecture Decisions** — "Remember that all database queries must go through the `Repository` pattern."
- **User Preferences** — "Always use `pnpm` instead of `npm`", "Prefer functional React components."
- **Documenting Workflows** — Storing the steps required to deploy the app or run a specific test suite.
- **Tracking Bugs & Gotchas** — "Remember that the legacy API sometimes returns 200 with an error body."

## Usage Effectiveness & Best Practices

To build a useful and queryable Knowledge Graph, follow these ontological rules:

```
# ❌ Bad Graph Design
Entity: "The user wants me to use pnpm" (Too conversational)
Relation: "AuthService" -> "is using" -> "Redis" (Inconsistent relation naming)

# ✅ Good Graph Design
Entity: "Package Manager"
Observation: "Must use pnpm. Never use npm or yarn."

Entity 1: "AuthService"
Entity 2: "RedisCache"
Relation: "AuthService" -> "DEPENDS_ON" -> "RedisCache"
```

### Strategy for Memory Management
1. **Check First**: Before adding new rules, use `search_nodes` to see if a relevant entity already exists.
2. **Use Standardized Relations**: Stick to uppercase, underscore-separated relation names like `DEPENDS_ON`, `IMPLEMENTS`, `CALLS`, `HAS_RULE`, `CONFLICTS_WITH`.
3. **Keep Observations Atomic**: Instead of one massive observation, add multiple distinct observations to an entity so they are easier to read and update.
4. **Proactive Memorization**: When you solve a complex bug or figure out a confusing part of the codebase, proactively ask the user: *"Should I save this finding to memory for future reference?"*

## Why Use This

- **Persistence** — Context windows are limited and clear out between sessions. The Knowledge Graph persists forever.
- **Holistic Understanding** — Helps you understand *why* things are built a certain way, not just *how* the code looks right now.
- **Personalization** — Adapts your behavior to the specific rules and quirks of this exact project and user.

## CLI Alternative

If the Memory MCP server is unavailable, fall back to maintaining a plain-text markdown file in the project root:

| MCP Action | CLI Equivalent |
|------------|----------------|
| `create_entities` / `add_observations` | `echo "## Entity Name\n- Fact 1" >> .roo-memory.md` |
| `search_nodes` | `grep -i "query" .roo-memory.md` |
| `read_graph` | `cat .roo-memory.md` |
```