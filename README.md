# SQL Query Explainer — Claude Skill

A custom Claude Skill that turns Claude into a senior data engineer reviewing your SQL. Paste any query — get a plain English explanation, performance audit, and optimized rewrite.

## What It Does

| Feature | Description |
|---------|-------------|
| **Plain English Breakdown** | Explains the query step-by-step in execution order (FROM → WHERE → GROUP BY → SELECT) |
| **Auto Warehouse Detection** | Detects Snowflake, BigQuery, Postgres, Redshift, SQL Server, Oracle, or Spark SQL from dialect clues |
| **Performance Audit** | Flags hidden cross joins, functions on indexed columns, unnecessary DISTINCT, correlated subqueries |
| **Correctness Check** | Catches NULL traps, integer division, off-by-one date ranges, missing GROUP BY columns |
| **Optimized Rewrite** | Rewrites the query with a clear diff summary of what changed and why |
| **Index Recommendations** | Warehouse-specific suggestions — indexes, clustering keys, partitioning, DISTKEY/SORTKEY |

## How to Use

### 1. Download the Skill

Download the [`SKILL.md`](./SKILL.md) file from this repo.

### 2. Upload to Claude

- Go to [claude.ai](https://claude.ai)
- Open **Settings** (bottom left)
- Go to **Profile** → **Custom Skills**
- Click **Add Skill** → Upload the `SKILL.md` file
- Done!

### 3. Start Using

Just paste any SQL query into Claude. The skill activates automatically when it detects SQL.

**Example prompt:**
```
Explain and optimize this query:

SELECT c.name, COUNT(o.id), SUM(p.amount)
FROM customers c, orders o, payments p
WHERE c.id = o.customer_id
AND o.id = p.order_id
AND YEAR(o.created_at) = 2024
GROUP BY c.name
HAVING SUM(p.amount) > 1000
ORDER BY SUM(p.amount) DESC
```

## Example Output

Claude will return:

1. **🔍 One-Liner** — "This query finds customers who spent over $1000 in 2024, ranked by total spend."
2. **📖 Step-by-Step** — Execution order walkthrough
3. **📊 Query Flow** — Visual diagram of table relationships
4. **⚠️ Issues Found**
   - 🔴 Implicit joins (comma-separated FROM) — should use explicit JOIN
   - 🔴 `YEAR(o.created_at)` prevents index usage — should use range filter
   - 🟡 `SUM(p.amount)` repeated — should use alias
5. **✅ Optimized Version** — Clean rewrite with CTEs and proper JOINs
6. **📈 Index Tips** — Specific to your warehouse

## Supported Warehouses

- Snowflake
- Google BigQuery
- Amazon Redshift
- PostgreSQL
- SQL Server
- Oracle
- Hive / Spark SQL

## What is a Claude Skill?

Claude Skills are markdown files that define how Claude should handle specific tasks. Think of them as reusable prompt templates that activate automatically based on context.

No code. No API. Just a `.md` file.

Learn more: [Claude Custom Skills](https://support.anthropic.com)

## Contributing

Found a SQL pattern that should be covered? Open an issue or submit a PR to improve the `SKILL.md`.

## License

MIT — use it however you want.
