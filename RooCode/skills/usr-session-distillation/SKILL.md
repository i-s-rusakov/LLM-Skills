---
name: usr-session-distillation
description: Analyze the completed task in `./memory-bank/actions` and extract core insights, failed attempts, and final logic before archiving. Use this to create high-value summaries for the `./RAG/archive` directory.
context: knowledge-extraction, retrospective
compatibility: Uses `mcp-sequential-thinking` for analysis.
---

# Session Distillation (Experience Extraction)

## Responsibilities
When a task is finished, don't just move files. **Think** about what was learned.

### Distillation Template:
Every archive entry in `./RAG/archive/` or `./RAG/incidents/` should capture:
1. **The "Why"**: What was the hidden complexity of the task?
2. **The "Dead Ends"**: What did we try that *didn't* work? (Crucial to prevent future agents from repeating mistakes).
3. **The "Final Logic"**: A concise summary of the implemented solution.
4. **The "Triggers"**: What should make a future agent look at this specific archive entry?

## When to Use
- **End of Day/Task**: "Summarize our work on the Clickhouse integration. Make sure to note why we didn't use the buffer table approach."
- **Incident Post-Mortem**: "Archive the Bybit timeout fix. Focus on the network settings we discovered."

## Why Use This
It builds a "Library of Experience." Future agents (or you in a month) will save hours by not retrying failed approaches documented in the RAG.