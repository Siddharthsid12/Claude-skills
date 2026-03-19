# SQL Query Explainer

A Claude Skill that turns Claude into a senior data engineer reviewing your SQL.

## What It Does

Paste any SQL query and get:

- **Plain English breakdown** in execution order (FROM → WHERE → GROUP BY → SELECT)
- **Auto warehouse detection** — Snowflake, BigQuery, Postgres, Redshift, SQL Server, Oracle, Spark SQL
- **Performance audit** — flags hidden cross joins, functions on indexed columns, unnecessary DISTINCT, correlated subqueries
- **Correctness check** — catches NULL traps, integer division, off-by-one date ranges
- **Optimized rewrite** — cleaner version with a diff summary of what changed and why
- **Index recommendations** — warehouse-specific suggestions (clustering keys, partitioning, DISTKEY/SORTKEY)

## Example

**Prompt:**
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

**Claude will flag:**
- 🔴 Implicit joins (comma-separated FROM) → use explicit JOIN
- 🔴 `YEAR(o.created_at)` prevents index usage → use date range filter
- 🟡 `SUM(p.amount)` repeated → use column alias

Then rewrite an optimized version with CTEs and proper JOINs.

## How to Use

1. Download the [`SKILL.md`](./SKILL.md) file
2. Go to [claude.ai](https://claude.ai) → **Settings** → **Profile** → **Custom Skills**
3. Click **Add Skill** → upload `SKILL.md`
4. Paste any SQL query — the skill activates automatically
