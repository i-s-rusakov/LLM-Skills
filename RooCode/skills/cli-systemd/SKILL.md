---
name: cli-systemd
description: Manage background services, timers, and system daemons using systemctl. Use this to start, stop, restart, or check the logs of the Arbitrage bot and its supporting services (MariaDB, Redis, etc.).
context: system-admin, process-management, uptime
compatibility: Uses `systemctl` and `journalctl`. Usually requires `cli-sudo` for management tasks.
---

# Systemd Service Management

## Commands & Usage

| Command | Use For |
|---------|---------|
| **Service Control** | |
| `systemctl status <service>` | Check if a service is running, stopped, or failed. |
| `sudo systemctl start <service>` | Start a service immediately. |
| `sudo systemctl stop <service>` | Stop a running service. |
| `sudo systemctl restart <service>`| Restart a service (useful after code updates). |
| `sudo systemctl enable <service>` | Enable service auto-start on system boot. |
| **Logs & Journal** | |
| `journalctl -u <service> -n 100` | View the last 100 lines of logs for a specific service. |
| `journalctl -u <service> -f` | (Use with caution) Tail the logs in real-time. |
| `journalctl -xe` | View the most recent system errors if a service fails to start. |

## When to Use

- **Bot Lifecycle** — "Restart the <service_name> service because I updated the strategy."
- **Service Health** — "Check if the MariaDB service is healthy."
- **Debugging Crashes** — "Show me the logs for `<service_name>.service` from the last hour."
- **Auto-start** — "Ensure the Redis server starts automatically when the computer reboots."

## Usage Effectiveness & Best Practices

When using `journalctl`, always use flags to limit output, otherwise the context window will be flooded.

```bash
# ❌ Bad Practice (Context Flood & Blocking)
# journalctl without flags can output thousands of lines and enter 'less' mode.
sudo journalctl -u <service_name>

# ✅ Good Practice (Targeted & Non-blocking)
# --no-pager prevents systemd from opening a pager (like 'less').
# -n 50 limits output to the most relevant recent events.
sudo journalctl -u <service_name> -n 50 --no-pager
```

### Integration with Sudo
Most `systemctl` commands (start, stop, enable) require root. Combine with `cli-sudo`:
`echo "password" | sudo -S systemctl restart <service_name>`

## Why Use This

- **Reliability** — Managing the bot as a systemd service is much more professional than running it in a raw terminal; it handles auto-restarts and logging out-of-the-box.
- **Visibility** — `journalctl` provides a unified way to see stdout/stderr from your Python scripts without managing custom log files.