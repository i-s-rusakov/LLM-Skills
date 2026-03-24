---
name: mcp-filesystem
description: Navigate, read, write, and manage files and directories within the allowed project workspace. Use this whenever the user asks to explore the codebase, read specific files, search for implementations, or modify/create code files.
context: workspace
compatibility: Uses the `@modelcontextprotocol/server-filesystem` MCP Server. If unavailable, fall back to standard Unix terminal commands (`ls`, `cat`, `grep`, `mkdir`, etc.).
---

# Filesystem & Workspace Management

## Tools

| Tool | Use For |
|------|---------|
| **Navigation & Discovery** | |
| `list_allowed_directories` | See which root directories are accessible to the MCP server |
| `directory_tree` | Get a visual tree representation of project structure (best for initial exploration) |
| `list_directory` | View the immediate contents of a specific folder |
| `list_directory_with_sizes`| View folder contents along with file sizes |
| **Reading Files** | |
| `read_file` / `read_text_file` | Read the content of a single file |
| `read_multiple_files` | Fetch contents of several files at once to gather broader context |
| `read_media_file` | Read metadata or base64 representation of media files |
| **Searching & Info** | |
| `search_files` | Find files matching a specific pattern or containing specific text |
| `get_file_info` | Get metadata (size, creation date, permissions) of a specific file |
| **Modification** | |
| `write_file` | Create a new file or completely overwrite an existing one |
| `edit_file` | Make targeted line-by-line modifications to an existing file |
| `create_directory` | Create a new folder structure |
| `move_file` | Rename or move files/directories |

## When to Use

- **Exploring a new codebase** — "What is the structure of this project?", "Where are the React components located?"
- **Understanding context** — "Read the `package.json` to see what dependencies we have."
- **Finding implementations** — "Find where the `authenticateUser` function is defined."
- **Writing & Refactoring code** — "Create a new utility file for date formatting", "Update the API endpoint in `api.ts`."
- **File management** — "Move all images to the `public/assets` folder."

## Usage Effectiveness & Best Practices

To avoid token limits and work efficiently, follow these patterns:

```
# ❌ Inefficient / Blind guessing
Trying to `read_file` on a path that might not exist.
Using `write_file` to rewrite a 1000-line file just to change 1 line.

# ✅ Efficient
1. Use `directory_tree` or `list_directory` first to understand the structure.
2. Use `search_files` to locate the exact file path before reading.
3. Use `edit_file` for small, targeted changes instead of `write_file`.
```

### Context Gathering Strategy
1. **Discover**: Start with `directory_tree` (up to depth 2 or 3).
2. **Search**: Use `search_files` if looking for a specific component or function.
3. **Analyze**: Use `read_multiple_files` if you need to understand how a component and its CSS/tests relate to each other.
4. **Act**: Use `edit_file` or `write_file` to implement the user's request.

## Why Use This

- **Autonomy** — Allows you to directly inspect the codebase without asking the user to copy-paste code snippets.
- **Accuracy** — You see the exact, current state of the files, reducing hallucinations.
- **Actionability** — You can apply fixes directly to the files instead of just outputting code blocks in the chat.

## CLI Alternative

If the Filesystem MCP server is unresponsive or restricted, use standard terminal commands via the command-line tool:

| MCP Tool | CLI Equivalent |
|----------|----------------|
| `list_directory` | `ls -la <path>` |
| `directory_tree` | `tree -L 2 <path>` |
| `read_file` | `cat <path>` |
| `search_files` | `grep -rnw <path> -e "pattern"` or `find . -name "pattern"` |
| `write_file` | `echo "content" > <path>` |
| `create_directory` | `mkdir -p <path>` |
```