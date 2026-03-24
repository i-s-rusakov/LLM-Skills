---
name: cli-sudo
description: Execute commands with superuser (root) privileges in a non-interactive environment. Use this whenever a command fails due to "Permission denied" or requires root access (e.g., modifying system configs, installing global packages).
context: system-admin, security
compatibility: Uses the `sudo` command with the `-S` (stdin) flag to handle password prompts programmatically.
---

# Sudo Execution (Non-Interactive)

## Tools & Patterns

| Command Pattern | Use For |
|-----------------|---------|
| `echo "PASS" \| sudo -S <cmd>` | Execute a single command as root by piping the password. |
| `sudo -nv` | Check if passwordless sudo is already available (non-interactive check). |
| `echo "PASS" \| sudo -S -v` | Validate/refresh the sudo timestamp to avoid re-entering password soon. |

## Handling Passwords

Since you cannot respond to interactive prompts:
1. **Search Memory**: Before asking the user for a password, use `mcp-memory` to search for an entity like `SystemPassword` or `SudoPassword`.
2. **Standard Input**: Always use the `echo "password" | sudo -S ...` pattern. The `-S` flag tells sudo to read the password from stdin instead of the terminal device.

## When to Use

- **System Updates** — `sudo apt update`.
- **Modifying Protected Files** — Editing `/etc/hosts` or web server configs.
- **Managing Permissions** — Changing ownership of files in `/var/www` or `/opt`.
- **Global Installs** — Installing system-level dependencies for the Arbitrage project.

## Usage Effectiveness & Best Practices

```bash
# ❌ Bad Practice (Will Hang)
# This will wait for a prompt that you can't see or answer.
sudo apt install htop

# ✅ Good Practice (Piped Password)
# Assuming 'PASS' is retrieved from memory or provided by the user.
echo "PASS" | sudo -S apt install -y htop
```

### Safety Note
Be aware that passing passwords via `echo` may leave the password in the shell history (`.bash_history`). If the environment is highly sensitive, warn the user, but for automation in Roo Code, this is often the only viable path.

## Why Use This

- **Autonomy** — Allows you to fix "Permission Denied" errors immediately without asking the user to run the command manually.
- **Completeness** — You can perform end-to-end system setup (e.g., installing MariaDB, Redis, and setting up the project) entirely through the chat.