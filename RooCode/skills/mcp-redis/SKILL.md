---
name: mcp-redis
description: Interact directly with the local Redis database to inspect cache, manage keys, debug session data, and verify background job queues. Use this whenever the user asks to check what is in the cache, clear specific cached data, or debug state that is stored in Redis.
context: database, caching, debugging
compatibility: Uses the `mcp-server-redis` MCP Server connected via Unix socket (`unix:///var/run/redis/redis.sock`). If unavailable, fall back to using `redis-cli` in the terminal.
---

# Redis Database Management

## Tools

| Tool | Use For |
|------|---------|
| `redis_command` / `execute` | Execute arbitrary Redis commands (e.g., `GET`, `SET`, `HGETALL`, `LRANGE`) |
| `get` | Retrieve the value of a specific string key |
| `set` | Set a string key to a specific value (with optional TTL) |
| `del` | Delete one or more keys |
| `keys` / `scan` | Find keys matching a specific pattern |
| `info` | Get information and statistics about the Redis server |

*(Note: The exact tool names may vary slightly depending on the MCP server version, but they generally map to standard Redis commands or provide a unified command execution tool).*

## Environment Context

Based on the current configuration, the server is connected via a Unix socket:
- **Connection:** `unix:///var/run/redis/redis.sock`
*(Note: Keep this in mind if you need to fall back to CLI commands).*

## When to Use

- **Cache Debugging** — "Why is the user still seeing old data?" (Check if the key exists and its TTL).
- **Session Management** — "Check if my session token is stored in Redis."
- **Queue Inspection** — "Why are background jobs stuck?" (Use `LRANGE` or `HGETALL` to inspect BullMQ/Celery queues).
- **State Verification** — "Did the rate limiter register my IP?"
- **Maintenance** — "Clear the entire product cache." (Use `DEL` or `FLUSHDB` if explicitly requested).

## Usage Effectiveness & Best Practices

Interacting with Redis requires care, especially regarding performance and data types.

```
# ❌ Bad Querying (Dangerous & Inefficient)
Executing `KEYS *` on a large database (blocks the Redis thread).
Trying to `GET` a Hash or List key (returns an error).

# ✅ Good Querying (Safe & Targeted)
Using `SCAN 0 MATCH user:* COUNT 100` to find keys safely.
Using `TYPE key_name` first if you are unsure of the data structure.
Using `HGETALL key_name` for hashes, or `LRANGE key_name 0 -1` for lists.
```

### Debugging Strategy
1. **Identify the Key Pattern:** If the user says "check the cache for product 123", guess or ask for the key prefix (e.g., `cache:product:123`).
2. **Check Existence & Type:** Run `EXISTS` and `TYPE` to ensure you are using the right command to read it.
3. **Read Data:** Use `GET`, `HGETALL`, `SMEMBERS`, etc., based on the type.
4. **Check Expiration:** Run `TTL key_name` to see if the cache is about to expire, which is a common source of elusive bugs.

## Why Use This

- **Instant Verification** — No need to write `console.log` in the application code just to see what's being saved to Redis.
- **State Manipulation** — You can manually alter the state (e.g., reset a rate limit counter) to test different application behaviors.

## CLI Alternative

If the Redis MCP server is unavailable, use the `redis-cli` command-line tool. **Crucially, you must use the socket path defined in the environment:**

| MCP Action | CLI Equivalent |
|------------|----------------|
| `get mykey` | `redis-cli -s /var/run/redis/redis.sock GET mykey` |
| `scan pattern` | `redis-cli -s /var/run/redis/redis.sock --scan --pattern "user:*"` |
| `info` | `redis-cli -s /var/run/redis/redis.sock INFO` |
```