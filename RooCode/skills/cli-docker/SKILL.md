---
name: cli-docker
description: Manage containerized applications, inspect logs, execute commands inside containers, and orchestrate services using Docker and Docker Compose via the command line. Use this whenever the user asks to start the database, check why a container crashed, rebuild an image, or run a command inside an isolated environment.
context: infrastructure, deployment, debugging
compatibility: Uses standard `docker` and `docker compose` (or `docker-compose`) CLI commands in the terminal.
---

# Docker & Container Management

## Commands & Usage

| Command | Use For |
|---------|---------|
| **Inspection & Logs** | |
| `docker ps -a` | List all running and stopped containers to check their status. |
| `docker logs --tail 100 -f <container>` | Read the most recent logs of a container (crucial for debugging crashes). |
| `docker inspect <container>` | Get detailed configuration, IP addresses, and volume mounts of a container. |
| **Execution & Interaction** | |
| `docker exec -it <container> sh` | Open an interactive shell inside a running container. |
| `docker exec <container> <cmd>` | Run a specific command inside a container (e.g., `alembic upgrade head`, `npm run test`). |
| **Orchestration (Compose)** | |
| `docker compose up -d` | Start all services defined in `docker-compose.yml` in the background. |
| `docker compose down` | Stop and remove containers, networks, and volumes created by compose. |
| `docker compose build` | Rebuild images if Dockerfile or dependencies (requirements.txt/package.json) changed. |
| **Resource Management** | |
| `docker system df` | Check disk usage by Docker images, containers, and volumes. |
| `docker image prune` | Clean up unused or dangling images to free up space. |

## When to Use

- **Service Bootstrapping** — "Start the Redis and PostgreSQL databases for local development."
- **Debugging Crashes** — "Why did the `api` container exit with code 1?" (Use `docker logs`).
- **Running Migrations** — "Run the database migrations inside the backend container."
- **Dependency Updates** — "I added a new Python package, rebuild the Docker image."
- **Port Conflicts** — "Check which container is occupying port 8080."

## Usage Effectiveness & Best Practices

Running Docker commands via an AI assistant requires avoiding commands that block the terminal indefinitely or output too much data.

```
# ❌ Bad Practice (Blocking & Flooding)
Running `docker compose up` without `-d` (blocks the terminal, preventing further commands).
Running `docker logs <container>` without `--tail` on a container that has been running for months (floods the context window).
Running interactive commands like `docker exec -it` without piping or specific non-interactive flags.

# ✅ Good Practice (Background & Targeted)
Running `docker compose up -d` to start services in the background.
Running `docker logs --tail 200 <container>` to get just enough context.
Running `docker exec <container> ls -la /app` to inspect internal state non-interactively.
```

### Strategy for Debugging Failing Containers
1. Run `docker ps -a` to find the ID/Name of the exited container and its exit code.
2. Run `docker logs --tail 100 <container>` to read the error stack trace.
3. If the error is missing dependencies, check the `Dockerfile` or `docker-compose.yml` via `mcp-filesystem`.
4. Fix the code/config, then run `docker compose up -d --build <service_name>` to apply changes.

## Why Use This

- **Isolation** — Allows you to manage complex dependencies (like specific database versions) without cluttering the host system.
- **Real-World Environment** — Debugging inside the container ensures you are seeing the exact environment where the code actually runs, not just the local OS.