---
name: cli-python-env
description: Manage Python virtual environments, install dependencies, execute scripts, and run tests via the command line. Use this whenever the user asks to add a new Python package, fix a `ModuleNotFoundError`, run the application, or execute the test suite.
context: python, dependencies, execution
compatibility: Uses standard CLI tools (`python`, `pip`, `venv`) and modern fast alternatives (`uv`, `pytest`) in the terminal.
---

# Python Environment & Execution

## Commands & Usage

| Command | Use For |
|---------|---------|
| **Environment Management** | |
| `uv venv` or `python -m venv venv` | Create a new isolated Python virtual environment. |
| `source venv/bin/activate` | Activate the virtual environment (in interactive shells). |
| `venv/bin/python` | Run Python directly from the venv (best for non-interactive AI execution). |
| **Dependency Management** | |
| `uv pip install <pkg>` or `pip install <pkg>` | Install a new package into the current environment. |
| `uv pip freeze > requirements.txt` | Save the current environment state to a requirements file. |
| `uv pip install -r requirements.txt` | Install all dependencies from a requirements file. |
| `uv pip tree` or `pip show <pkg>` | Inspect installed packages and their dependency trees. |
| **Execution & Testing** | |
| `venv/bin/python main.py` | Execute a Python script using the project's isolated environment. |
| `venv/bin/pytest tests/` | Run the test suite using pytest. |
| `venv/bin/alembic upgrade head` | Run database migrations (if using SQLAlchemy/Alembic). |

## When to Use

- **Project Setup** — "Install the dependencies from `requirements.txt`."
- **Fixing Import Errors** — "Why am I getting `ModuleNotFoundError: No module named 'requests'`?" (Check if the package is installed in the venv).
- **Adding Features** — "Install `beautifulsoup4` and add it to our requirements."
- **Running Code** — "Run the arbitrage bot script."
- **Testing** — "Run the unit tests for the calculation module."

## Usage Effectiveness & Best Practices

When executing terminal commands as an AI, environment activation can be tricky because each command often runs in a fresh subshell.

```
# ❌ Bad Practice (Global pollution & Context loss)
Running `pip install requests` (might install globally instead of in the venv).
Running `python main.py` without ensuring the venv is activated.
Running interactive scripts (e.g., scripts expecting `input()`) without mocking or piping.

# ✅ Good Practice (Explicit paths & Modern tools)
Running `venv/bin/pip install requests` to guarantee it goes into the venv.
Running `venv/bin/python main.py` to ensure the correct interpreter is used.
Using `uv pip install` instead of `pip install` for 10-100x faster installations (since `uv` is available on this system).
```

### Strategy for Debugging Python Issues
1. **Verify the Interpreter:** If a script fails with missing modules, run `venv/bin/python -c "import sys; print(sys.executable)"` to confirm you are using the right environment.
2. **Check Dependencies:** Run `venv/bin/pip list` to see what is actually installed versus what is in `requirements.txt`.
3. **Handle Sync Issues:** If you add an `import` statement to a file using `mcp-filesystem`, immediately run `venv/bin/pip install <package>` and update `requirements.txt` before trying to run the code.

## Why Use This

- **Reproducibility** — Keeping dependencies strictly within the `venv` ensures the project works on other machines (or inside Docker).
- **Autonomy** — Allows you to test the code you just wrote immediately, catching syntax errors or runtime exceptions before presenting the solution to the user.
- **Speed** — Leveraging `uv` (as configured in the system) drastically reduces waiting time during dependency installation.