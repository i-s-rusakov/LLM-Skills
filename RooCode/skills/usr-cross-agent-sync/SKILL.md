---
name: usr-cross-agent-sync
description: Synchronize configuration and context files for various AI tools (Qwen, Gemini, OpenCode, Roo). Ensure these files act as pointers to the central `./memory-bank` rather than duplicating information, preventing context drift across tools.
context: ai-orchestration, dry-principle
compatibility: Uses `mcp-filesystem` to audit and edit agent-specific markdown and config files.
---

# Cross-Agent Synchronization

## Responsibilities

The project uses multiple AI tools (`QWEN.md`, `GEMINI.md`, `AGENTS.md`, and `.roo/`). Your job is to ensure these files maintain a **Single Source of Truth (SSOT)** by referencing `./memory-bank` and `./RAG` instead of storing deep project knowledge themselves.

### Auditing & Syncing Rules:
1. **No Duplication**: If you find business logic, database schemas, or API details hardcoded in `QWEN.md` or `AGENTS.md`, extract that information to the appropriate file in `./memory-bank/` (e.g., `techContext.md` or `specifications/`).
2. **Pointer Strategy**: Replace the extracted text in the agent file with a direct instruction for that specific LLM. 
   *Example:* `"For architecture rules, always read ./memory-bank/dataFlowArchitecture.md before writing code."`
3. **Hierarchy Maintenance**: Ensure nested `AGENTS.md` files (up to 3 levels deep in source code) only contain highly localized, directory-specific instructions, while delegating global rules to the root `memory-bank`.
4. **Tool-Specific Quirks**: Preserve instructions in agent files that are strictly about *how* that specific LLM should behave (e.g., Qwen's specific formatting rules or OpenCode's execution limits), as these do not belong in the global product context.

## When to Use

- **System Setup** — "We are adding Gemini to the workflow. Set up its context file."
- **Context Refactoring** — "The `AGENTS.md` file has grown to 500 lines. Refactor it to use the memory bank."
- **Global Updates** — "We changed our main tech stack to include Clickhouse. Update the core memory bank and ensure all agent files point to it."

## Usage Effectiveness & Best Practices

```markdown
# ❌ Bad Practice (Information Silos)
Writing the new Bybit API limits directly into `.qwen/PROJECT_SUMMARY.md`. If Gemini works on the project later, it won't know those limits.

# ✅ Good Practice (Single Source of Truth)
1. Add Bybit API limits to `./memory-bank/product-details/api-limits.md`.
2. Edit `QWEN.md`, `GEMINI.md`, and `AGENTS.md` to include: 
   "Consult `./memory-bank/product-details/api-limits.md` for rate limiting rules."
```