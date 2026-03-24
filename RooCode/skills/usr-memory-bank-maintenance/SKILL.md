---
name: usr-memory-bank-maintenance
description: Maintain and restructure the project's core documentation (`./memory-bank`) and long-term storage (`./RAG`). Use this to archive completed tasks, update global project context, and preserve valuable findings without ever permanently deleting useful information.
context: knowledge-management, documentation, archiving
compatibility: Uses `mcp-filesystem` tools to read, move, and rewrite markdown files.
---

# Memory Bank & RAG Maintenance

## Responsibilities

Your goal is to ensure the project's knowledge base remains structured, up-to-date, and free of clutter, while **never permanently deleting valuable context**. Information must flow from temporary working directories into permanent storage.

### The Information Lifecycle Rules:
1. **Core Context (`./memory-bank/*.md`)**: Frequently accessed files (`activeContext.md`, `techContext.md`, `progress.md`). Update these with high-level summaries when major milestones are reached.
2. **Component Specs (`./memory-bank/product-details/` & `specifications/`)**: Store detailed, up-to-date architectural decisions and system component documentation here.
3. **Future Work (`./memory-bank/to-research/`)**: Move unresolved issues, business logic ideas, and discovered technical debt here for later discussion.
4. **Archiving (`./RAG/`)**: When a task is completed, summarize the temporary data from `./memory-bank/actions/` and move it into `./RAG/archive/` or `./RAG/incidents/`. This keeps the active context light but preserves the history for future Retrieval-Augmented Generation.
5. **Temporary Data (`./memory-bank/actions/`)**: Clear this directory **ONLY AFTER** its contents have been successfully summarized and archived into `./RAG/` or integrated into core `memory-bank` files.

## When to Use

- **Task Completion** — "The feature is done. Archive the current session and update the progress."
- **Context Overload** — "Our active context is too big; refactor the documentation into the RAG archive."
- **Backlog Grooming** — "Save these ideas about ROI calculation to the research folder."
- **Incident Resolution** — "The production bug is fixed. Write a post-mortem to `./RAG/incidents/`."

## Usage Effectiveness & Best Practices

```markdown
# ❌ Bad Practice (Destructive & Messy)
Deleting `./memory-bank/actions/currentPlan.md` when the task is done without saving the history.
Dumping thousands of lines of raw logs into `activeContext.md`.

# ✅ Good Practice (Preservative & Structured)
1. Read `./memory-bank/actions/currentPlan.md` and `temp-reports/`.
2. Write a summary to `./RAG/archive/2026-03-21-feature-name.md`.
3. Extract architectural changes and update `./memory-bank/product-details/dataFlowArchitecture.md`.
4. Clear the `actions/` folder for the next task.
```

## Tools Required
Rely heavily on `read_multiple_files`, `write_file`, and `edit_file` from the `mcp-filesystem` server. Use `mcp-sequential-thinking` before massive restructures to plan where information belongs.