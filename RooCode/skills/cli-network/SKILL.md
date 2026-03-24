---
name: cli-network
description: Diagnose network connectivity, measure API latency, inspect open ports, and troubleshoot DNS or routing issues using standard CLI utilities. Use this whenever the user asks to check if an exchange API is reachable, why a connection is timing out, or what services are listening on local ports.
context: network, api, latency, debugging
compatibility: Uses standard Linux/Unix networking tools (`curl`, `ping`, `ss`, `lsof`, `traceroute`, `dig`).
---

# Network Diagnostics & API Testing

## Commands & Usage

| Command | Use For |
|---------|---------|
| **Connectivity & Latency** | |
| `ping -c 4 <host>` | Check basic reachability and round-trip time (RTT). |
| `curl -I <url>` | Fetch only HTTP headers to check API status (200 OK, 403 Forbidden, etc.). |
| `curl -w "@format.txt" -o /dev/null -s <url>` | Measure detailed timing (DNS, Connect, TTFB, Total) for performance tuning. |
| `traceroute <host>` | Identify where packets are slowing down or dropping in the network path. |
| **Local Port & Process Audit** | |
| `ss -tunlp` | List all listening TCP/UDP ports and the processes using them (Modern `netstat`). |
| `lsof -i :<port>` | Find exactly which process ID (PID) is occupying a specific port. |
| **DNS & Routing** | |
| `dig <domain>` | Check DNS records and resolution speed. |
| `ip addr` / `ip route` | Inspect local network interfaces and the routing table. |

## When to Use

- **Exchange API Debugging** — "Is `api.binance.com` reachable from this server?"
- **Latency Optimization** — "Measure the response time for the Bybit orderbook endpoint."
- **Port Conflicts** — "Why can't I start my local dashboard on port 8080?" (Use `lsof -i :8080`).
- **Timeout Investigation** — "The bot is getting connection timeouts; is it a DNS issue or a routing hop?"
- **Websocket Debugging** — "Check if the server can establish an outbound connection to the websocket entry point."

## Usage Effectiveness & Best Practices

In arbitrage, milliseconds matter. Use `curl`'s write-out features to get precise data.

```bash
# ❌ Bad Practice (Vague info)
# Just running curl tells you if it works, but not WHY it's slow.
curl https://api.exchange.com/v3/ticker

# ✅ Good Practice (Detailed Performance Metrics)
# Use a custom format to see exactly where the delay happens.
curl -s -o /dev/null -w "DNS: %{time_namelookup}s | Connect: %{time_connect}s | TTFB: %{time_starttransfer}s | Total: %{time_total}s\n" https://api.exchange.com/v3/ticker
```

### Strategy for Debugging "Connection Refused"
1. **Check Local**: If it's a local service, run `ss -tunlp | grep <port>` to see if it's even listening.
2. **Check DNS**: Run `dig <host>` to ensure the domain resolves to the correct IP.
3. **Check Path**: Use `ping` to see if the host is alive, then `traceroute` to see if a firewall is dropping packets midway.
4. **Check Headers**: Use `curl -v` (verbose) to see the full TLS handshake and request/response cycle.

## Why Use This

- **Precision** — You can pinpoint if a delay is caused by slow DNS resolution, a slow TCP handshake, or slow server-side processing.
- **Conflict Resolution** — Quickly identify and kill processes that are blocking your bot's required ports.
- **Zero Dependencies** — These tools are usually pre-installed on Linux servers, making them more reliable than external libraries during a crisis.

## CLI Alternative

If specific tools are missing, use these common fallbacks:

| Tool | Fallback |
|------|----------|
| `ss` / `netstat` | `cat /proc/net/tcp` (Raw but works) |
| `curl` | `python3 -c "import urllib.request; ..."` |
| `dig` | `nslookup` or `host` |