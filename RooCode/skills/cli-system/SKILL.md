---
name: cli-system
description: Monitor system resources, manage running processes, and troubleshoot disk or memory issues using standard Linux utilities. Use this whenever the user asks to check CPU/RAM usage, kill a frozen bot process, or clean up disk space when logs grow too large.
context: system-admin, monitoring, troubleshooting
compatibility: Uses standard Linux/Unix system tools (`ps`, `top`, `kill`, `df`, `free`, `du`, `uptime`).
---

# System Administration & Monitoring

## Commands & Usage

| Command | Use For |
|---------|---------|
| **Process Management** | |
| `ps aux | grep <name>` | Find the Process ID (PID) of a specific script or service. |
| `top -b -n 1` | Get a snapshot of the most CPU/RAM intensive processes (Batch mode). |
| `kill -15 <pid>` | Gracefully terminate a process (allows for cleanup). |
| `kill -9 <pid>` | Forcefully kill a process if it is frozen or unresponsive. |
| **Resource Monitoring** | |
| `free -h` | Check available and used RAM in a human-readable format. |
| `df -h` | Check disk space usage across all partitions. |
| `uptime` | See how long the system has been running and the Load Average. |
| **Storage Analysis** | |
| `du -sh <path>` | Calculate the total size of a specific directory (e.g., `/logs`). |
| `du -ah <path> | sort -rh | head -n 10` | Find the 10 largest files/folders in a path. |

## When to Use

- **Bot Unresponsiveness** — "The arbitrage bot isn't responding, is it still running?" (Use `ps aux`).
- **Performance Lag** — "The server is slow; what's eating the CPU?" (Use `top -b -n 1`).
- **Memory Leaks** — "Check if we are running out of RAM during data processing." (Use `free -h`).
- **Disk Space Issues** — "The database stopped working; check if the disk is full." (Use `df -h`).
- **Cleaning Logs** — "Find which log files are taking up too much space and clear them."

## Usage Effectiveness & Best Practices

As an AI, you cannot interact with real-time UI like `htop`. You must use "batch" or "snapshot" versions of commands.

```bash
# ❌ Bad Practice (Interactive & Blocking)
# This will hang the terminal because htop expects a human to press 'q'.
htop

# ✅ Good Practice (Snapshot & Parsable)
# This gives a one-time report of processes, which the AI can read.
ps aux --sort=-%cpu | head -n 10
# Or for RAM:
ps aux --sort=-%mem | head -n 10
```

### Strategy for Troubleshooting a Stuck Bot
1. **Identify**: Use `ps aux | grep main.py` to see if the process exists and what its PID is.
2. **Check Status**: Use `top -p <pid> -b -n 1` to see if it's using 100% CPU (infinite loop) or 0% (deadlock).
3. **Check Logs**: Use `tail -n 50` on the bot's log file to see the last recorded action.
4. **Action**: If stuck, try `kill -15 <pid>`. If that fails after 10 seconds, use `kill -9 <pid>`.
5. **Restart**: Use `cli-python-env` or `cli-docker` to start the bot again.

## Why Use This

- **Health Control** — You can proactively warn the user if the server is running out of resources before a crash occurs.
- **Cleanup** — Helps maintain the server by finding and removing orphaned processes or massive temporary files.
- **Root Cause Analysis** — Distinguish between a code bug (exception) and a system issue (Out of Memory).

## CLI Alternative

If specific monitoring tools are restricted, you can often find raw data in the `/proc` filesystem:

| Info Type | Command |
|-----------|---------|
| Detailed RAM | `cat /proc/meminfo` |
| CPU Model/Specs | `cat /proc/cpuinfo` |
| Load Average | `cat /proc/loadavg` |