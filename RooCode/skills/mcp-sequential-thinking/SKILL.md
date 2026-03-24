---
name: mcp-sequential-thinking
description: Employ a structured, step-by-step thinking process for complex problem-solving, debugging, and architectural design. Use this when a problem requires deep analysis, multiple variables, or when you need to break down a large task into manageable, verifiable steps before writing code.
context: reasoning
compatibility: Uses the `@modelcontextprotocol/server-sequential-thinking` MCP Server. If unavailable, fall back to writing a step-by-step plan in a temporary Markdown file (e.g., `.roo-plan.md`) or using explicit chain-of-thought reasoning in the chat.
---

# Sequential Thinking

## Tools

| Tool | Use For |
|------|---------|
| `sequentialthinking` | Execute a single step of a thinking process. Allows for dynamic adjustment, branching, and revision of thoughts until a conclusion is reached. |

## When to Use

- **Cryptic Bugs** — "Why is the production build failing with a memory leak only on Fridays?" (Requires formulating hypotheses, checking logs, and eliminating variables).
- **Architecture Design** — "How should we design the database schema for a multi-tenant SaaS?" (Requires weighing pros/cons, considering scale, and planning relations).
- **Massive Refactoring** — "We need to migrate from Redux to Zustand." (Requires a step-by-step migration plan to avoid breaking the app).
- **Complex Logic** — Writing intricate algorithms (e.g., custom parsers, complex regex, scheduling algorithms).

## Usage Effectiveness & Best Practices

The `sequentialthinking` tool is designed to be called **iteratively**. You generate one thought, analyze it, and then decide if you need another thought, if you need to revise a previous thought, or if you are ready to act.

### Key Parameters to Master
- `thought`: The actual reasoning or step.
- `thoughtNumber`: Current step in the sequence.
- `totalThoughts`: Estimated total steps (can be adjusted as you go).
- `nextThoughtNeeded`: Set to `true` to continue thinking, `false` to finish.
- `isRevision` / `revisesThought`: Use this when you realize a previous assumption was wrong and you need to correct your path.
- `branchFromThought` / `branchId`: Use this to explore alternative solutions (e.g., "Plan A" vs "Plan B") before committing.
❌ Bad Thinking (Rushing)
Calling the tool once with a massive block of text containing the entire solution, setting nextThoughtNeeded: false immediately.
✅ Good Thinking (Iterative & Adaptive)
Thought 1: "Analyze the error message and identify the failing file." (next: true)
Thought 2: "Look at the recent git changes in that file." (next: true)
Thought 3: "Hypothesis A: The new API endpoint is returning null." (next: true)
Thought 4 (Revision of 3): "Wait, the logs show the API returns 200. Hypothesis A is wrong. Let's look at the frontend parsing logic." (isRevision: true, revisesThought: 3, next: true)
Thought 5: "Found it. The parser expects a string but gets a number. Here is the fix plan." (next: false)
code
Code
## Why Use This

- **Prevents Hallucinations** — By forcing a step-by-step breakdown, you are less likely to jump to incorrect conclusions or write flawed code.
- **Self-Correction** — The ability to revise previous thoughts allows you to catch your own mistakes *before* you present a solution to the user.
- **Transparency** — The user can see exactly how you arrived at a solution, building trust in your architectural decisions.

## CLI Alternative

If the Sequential Thinking MCP server is unavailable, simulate the process using a scratchpad file:

| MCP Action | CLI Equivalent |
|------------|----------------|
| `sequentialthinking` | `echo "- Step 1: [Thought]" >> .roo-plan.md` <br> `cat .roo-plan.md` to review <br> Edit `.roo-plan.md` to revise. |