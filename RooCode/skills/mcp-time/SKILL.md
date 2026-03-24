---
name: mcp-time
description: Retrieve the exact current time, date, and timezone information. Use this whenever the user asks to debug time-sensitive logic (JWT expirations, cron jobs, cache TTLs), correlate application logs with the present moment, or calculate time differences.
context: utility, debugging
compatibility: Uses the `mcp-server-time` MCP Server. If unavailable, fall back to standard terminal commands (`date`).
---

# Time & Date Utility

## Tools

| Tool | Use For |
|------|---------|
| `get_current_time` | Fetch the current local time, UTC time, and timezone offset. |

*(Note: The exact tool name depends on the server implementation, but it will expose a simple way to get the current timestamp).*

## When to Use

- **Debugging Authentication** — "Why is my JWT token instantly expiring?" (Check current time vs token `exp` claim).
- **Log Correlation** — "Look at the logs from the last 5 minutes." (You need to know what time it is *now* to filter the logs correctly).
- **Cron Jobs & Scheduling** — "Why didn't the midnight cron job run?" (Check the server's current time and timezone).
- **Cache Expiration** — "Is the Redis cache key supposed to be expired by now?"
- **Database Records** — Verifying `created_at` or `updated_at` timestamps in recent database inserts.

## Usage Effectiveness & Best Practices

AI models do not have an internal real-time clock. Never guess the current time if a task depends on it.

```
# ❌ Bad Practice (Hallucinating time)
Assuming the current date is the knowledge cutoff date, or guessing the timezone, leading to incorrect log filtering or cron debugging.

# ✅ Good Practice (Verifying time)
1. Call `get_current_time` to establish the exact current timestamp and timezone.
2. Compare this baseline against the database records, logs, or token claims.
```

### Debugging Strategy for Timezones
If the user reports an issue with dates being "off by a few hours":
1. Check the local time using this MCP server.
2. Check the application's configured timezone (e.g., in `mcp-filesystem` by reading `.env` or `docker-compose.yml`).
3. Check the database's timezone (e.g., via `mcp-playwright` or standard DB queries).
4. Identify the mismatch (usually a UTC vs Local Time issue).

## Why Use This

- **Accuracy** — Eliminates assumptions about "when" something is happening.
- **Contextual Awareness** — Essential for filtering massive log files down to just the "recent" events.

## CLI Alternative

If the Time MCP server is unavailable, use standard Unix commands in the terminal:

| MCP Action | CLI Equivalent |
|------------|----------------|
| `get_current_time` (Local) | `date` |
| `get_current_time` (UTC) | `date -u` |
| `get_current_time` (ISO 8601) | `date --iso-8601=seconds` |
| `get_current_time` (Unix Epoch) | `date +%s` |
```