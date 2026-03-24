---
name: mcp-git
description: Interact with the Git version control system to manage branches, inspect changes, view commit history, and create commits. Use this whenever the user asks to save progress, review what has been modified, revert code, or understand the history of a specific file.
context: version-control
compatibility: Uses the `mcp-server-git` MCP Server strictly bound to the repository at `/home/iot/Documents/PycharmProjects/Arbitrage/.git/`. If unavailable, fall back to standard `git` CLI commands in the terminal.
---

# Git Version Control

## Tools

| Tool | Use For |
|------|---------|
| **Inspection & History** | |
| `git_status` | See which files are modified, staged, or untracked. |
| `git_diff` | View the exact line-by-line changes made to files before committing. |
| `git_log` | View the commit history to understand recent changes or find specific commits. |
| `git_show` | Inspect the details and diff of a specific past commit. |
| **Modification & Committing** | |
| `git_add` | Stage specific files or directories for the next commit. |
| `git_commit` | Create a new commit with a descriptive message containing the staged changes. |
| `git_reset` | Unstage files or revert the working directory to a previous state. |
| **Branching** | |
| `git_branch` | List existing branches or create a new one. |
| `git_checkout` | Switch to a different branch or restore working tree files. |

## Environment Context

Based on the current configuration, this MCP server is strictly scoped to the following repository:
- **Repository Path:** `./.git/`
*(Note: All operations performed by this server will apply to this specific project).*

## When to Use

- **Reviewing Progress** — "What files have we changed so far?" (Use `git_status` and `git_diff`).
- **Saving Work** — "Commit the changes we just made for the database migration."
- **Understanding Context** — "Why was this line of code added?" (Use `git_log` or fallback to `git blame` via CLI).
- **Branch Management** — "Create a new branch for the authentication feature."
- **Undoing Mistakes** — "Revert the changes in `api.py` to the last committed version."

## Usage Effectiveness & Best Practices

Version control requires precision. Never guess what changes you are committing.

```
# ❌ Bad Practice (Blind Committing)
Running `git_add` on all files and immediately running `git_commit` with a generic message like "Update files" without checking the diff.

# ✅ Good Practice (Verified Committing)
1. Run `git_status` to see the list of changed files.
2. Run `git_diff` to review the exact changes and ensure no debug code (e.g., `print()`) is left behind.
3. Run `git_add` only on the relevant files.
4. Run `git_commit` with a clear, conventional commit message (e.g., "feat(auth): add JWT validation").
```

### Strategy for Context Gathering
If you are asked to fix a bug in unfamiliar code, checking the Git history is often faster than reading the whole codebase:
1. Find the file with the bug.
2. Use `git_log` on that specific file to see who changed it last and why.
3. Read the commit message of the last change to understand the original developer's intent.

## Why Use This

- **Safety** — Allows you to confidently make large refactors knowing you can easily revert if something breaks.
- **Autonomy** — Completes the full development loop (Write Code -> Test -> Commit) without requiring the user to switch to their terminal.
- **Traceability** — Helps maintain a clean and professional project history.

## CLI Alternative

If the Git MCP server is unavailable, use standard `git` commands in the terminal. Since the project path is specific, ensure you are in the correct directory or use the `-C` flag:

| MCP Action | CLI Equivalent |
|------------|----------------|
| `git_status` | `git -C ./ status` |
| `git_diff` | `git -C ./ diff` |
| `git_add` | `git -C ./ add <file>` |
| `git_commit` | `git -C ./ commit -m "message"` |
| `git log` | `git -C ./ log -n 5` |
```