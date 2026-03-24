---
name: cli-database
description: Execute SQL queries, manage schemas, and inspect data in MariaDB and Clickhouse databases via the command line. Use this to verify data consistency, run analytical queries on market data, or debug database migrations in the Arbitrage project.
context: database, sql, analytics
compatibility: Uses `mariadb` (or `mysql`) and `clickhouse-client` CLI utilities. Fall back to language-specific scripts (Python/Node) if CLI clients are not installed.
---

# Database Management (MariaDB & Clickhouse)

## Commands & Usage

### MariaDB (Relational / Transactional)
| Command | Use For |
|---------|---------|
| `mariadb -u<user> -p -e "QUERY"` | Execute a non-interactive SQL query (e.g., `SELECT`, `UPDATE`). |
| `mariadb-show -u<user> -p <db>` | List tables in a specific database. |
| `mariadb-dump -u<user> -p <db> > backup.sql` | Create a logical backup of the database. |
| `mariadb -u<user> -p <db> < schema.sql` | Import a schema or data from a SQL file. |

### Clickhouse (OLAP / Analytical)
| Command | Use For |
|---------|---------|
| `clickhouse-client --query "QUERY"` | Execute a query against the Clickhouse server. |
| `clickhouse-client --format PrettyCompact` | Get query results in a human-readable table format. |
| `clickhouse-client --database <name>` | Specify the target database for the query. |
| `clickhouse-client --query "INSERT INTO table FORMAT CSV" < data.csv` | High-speed data ingestion from CSV files. |

## When to Use

- **Data Verification** — "Did the arbitrage bot successfully save the last 10 trades to MariaDB?"
- **Market Analysis** — "Calculate the average spread for BTC/USDT over the last hour in Clickhouse."
- **Schema Debugging** — "Show me the structure of the `orders` table in MariaDB."
- **Performance Tuning** — "Check Clickhouse system logs for slow queries or memory issues."
- **Emergency Fixes** — "Manually update the status of a stuck withdrawal in the database."

## Usage Effectiveness & Best Practices

Working with databases via CLI requires caution to avoid data loss or performance degradation.

```
# ❌ Bad Practice (Dangerous & Messy)
Running `SELECT * FROM ticks` on a Clickhouse table with billions of rows (crashes the context or the client).
Passing passwords directly in the command: `mariadb -pMY_SECRET_PASSWORD` (leaks credentials in process lists).
Running `DELETE` or `UPDATE` without a `WHERE` clause.

# ✅ Good Practice (Safe & Readable)
1. Always use `LIMIT` for `SELECT` queries: `SELECT * FROM trades LIMIT 20`.
2. Use `--format PrettyCompact` for Clickhouse to make results readable for the AI.
3. Use environment variables (e.g., `MYSQL_PWD`) or a `.my.cnf` file for MariaDB credentials.
4. Run a `SELECT COUNT(*)` first if you are unsure about the size of the result set.
```

### Strategy for Arbitrage Data
- **MariaDB** is for **State**: Check this when you need to know "What is happening now?" or "What are the bot's settings?".
- **Clickhouse** is for **History**: Check this when you need to know "What happened in the past?" or "What is the trend?".
- If a query is complex, use `mcp-sequential-thinking` to draft the SQL before executing it.

## Why Use This

- **Speed** — Verifying data via CLI is much faster than writing a temporary Python script and running it.
- **Direct Access** — Allows you to bypass application logic and see exactly what is stored in the "source of truth".
- **Analytical Power** — Leverage Clickhouse's engine for complex aggregations that are too slow to do in Python code.

## CLI Alternative

If the dedicated CLI clients are missing, use a one-liner Python script via `cli-python-env`:

| Target | Python One-liner (Example) |
|--------|----------------------------|
| MariaDB | `python -c "import pymysql; ..."` |
| Clickhouse | `python -c "import clickhouse_connect; ..."` |