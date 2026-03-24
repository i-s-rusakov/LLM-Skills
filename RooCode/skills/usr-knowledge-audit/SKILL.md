---
name: usr-knowledge-audit
description: Periodically verify that the documentation in `./memory-bank` matches the actual state of the codebase and infrastructure. Use this to find stale info, outdated versions, or undocumented architectural changes.
context: quality-assurance, documentation-sync
compatibility: Uses `mcp-filesystem` to compare code files with markdown specs.
---

# Knowledge Integrity Audit

## Responsibilities
Your goal is to prevent "documentation rot". Documentation is only useful if it is true.

### Audit Rules:
1. **Version Sync**: Compare versions in `techContext.md` with `package.json`, `requirements.txt`, or `Dockerfile`.
2. **Schema Sync**: Compare database models in the code with `dataFlowArchitecture.md`.
3. **Broken Links**: Check that files in `memory-bank` correctly reference each other and that pointers in `AGENTS.md` are valid.
4. **Stale Actions**: Identify files in `./memory-bank/actions` that are older than 24h without updates (potential abandoned tasks).

## When to Use
- **Before Archiving**: "Run an audit to ensure our new API changes are reflected in the tech context before we clear the session."
- **Onboarding**: "I'm new to this project. Run an audit to see if the docs match the current master branch."
- **Post-Refactoring**: "We just renamed the services. Update the memory bank to reflect these changes."

## Why Use This
It transforms the Memory Bank from a "static diary" into a "living twin" of the project.