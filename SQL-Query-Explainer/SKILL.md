---
name: sql-query-explainer
description: "Use this skill when the user pastes a SQL query and wants to understand it, debug it, or optimize it. Triggers include: any SQL code block (SELECT, INSERT, UPDATE, DELETE, MERGE, CTE, window functions, subqueries), or phrases like 'explain this query', 'what does this SQL do', 'optimize this query', 'why is this slow', 'rewrite this SQL', 'break down this query', 'SQL review', or 'query performance'. Also triggers when the user shares a slow query, an EXPLAIN plan, or asks about indexing strategy for a specific query. Do NOT use for writing SQL from scratch (unless rewriting an existing query), database admin tasks, or schema design."
---

# SQL Query Explainer Skill

## Overview

When a user pastes a SQL query, you become a senior data engineer doing a thorough code review. You explain what the query does, flag problems, and suggest concrete optimizations — all in plain English that a junior dev could follow.

## Step 1: Detect the Warehouse (if not specified)

Look for dialect clues in the SQL:
- `QUALIFY`, `FLATTEN`, `TRY_CAST`, `ILIKE` → **Snowflake**
- Backtick table refs, `UNNEST`, `SAFE_CAST`, `STRUCT` → **BigQuery**
- `DISTKEY`, `SORTKEY`, `ENCODE`, `UNLOAD` → **Redshift**
- `::` casting, `LATERAL`, `GENERATE_SERIES` → **PostgreSQL**
- `TOP`, `NOLOCK`, `CROSS APPLY` → **SQL Server**
- `CONNECT BY`, `ROWNUM`, `NVL` → **Oracle**
- `COLLECT_SET`, `LATERAL VIEW`, `DISTRIBUTE BY` → **Hive/Spark SQL**

If unclear, assume PostgreSQL but mention it. If the user specifies a warehouse, use that.

## Step 2: Plain English Breakdown

Structure your explanation as follows:

### One-Liner Summary
Start with a single sentence: "This query [does X] by [doing Y]."

### Step-by-Step Walkthrough
Walk through the query in **execution order** (not reading order). This is critical — explain in the order the database actually processes it:

1. **FROM / JOIN** — which tables are accessed, how they're joined
2. **WHERE** — what rows are filtered out
3. **GROUP BY** — how rows are aggregated
4. **HAVING** — what groups are filtered out
5. **SELECT** — what columns/expressions are computed
6. **WINDOW functions** — what calculations happen over partitions
7. **ORDER BY** — how results are sorted
8. **LIMIT** — how results are truncated

For each step, explain in plain English what it does and WHY it matters for the result.

### Key Concepts Used
Call out any non-trivial SQL features with a brief explanation:
- CTEs (Common Table Expressions)
- Window functions (ROW_NUMBER, RANK, LAG, LEAD, etc.)
- Self-joins
- Correlated subqueries
- CASE expressions
- COALESCE / NULL handling
- UNION vs UNION ALL
- EXISTS vs IN
- Recursive CTEs
- PIVOT / UNPIVOT
- MERGE / UPSERT patterns

## Step 3: Visual Query Flow

After the explanation, generate a simple visual showing the data flow using a Mermaid diagram or ASCII art:

```
source_table_a ──┐
                  ├──→ JOIN ──→ WHERE filter ──→ GROUP BY ──→ Final Result
source_table_b ──┘
```

Keep it simple — this is for quick comprehension, not a formal DAG.

## Step 4: Problem Detection

Scan the query for these common issues and flag any you find:

### Performance Red Flags
- `SELECT *` in production queries — suggest explicit columns
- Missing JOIN predicates (accidental cross joins)
- Functions on indexed columns in WHERE (e.g., `WHERE YEAR(date_col) = 2024`) — prevents index usage
- `DISTINCT` hiding a bad join — suggest fixing the join instead
- `ORDER BY` inside subqueries (pointless in most databases)
- Correlated subqueries that could be JOINs
- `NOT IN` with nullable columns — suggest `NOT EXISTS` instead
- `UNION` when `UNION ALL` would work — unnecessary dedup cost
- Multiple scans of the same large table — suggest CTEs or temp tables
- `LIKE '%value%'` — leading wildcard prevents index usage
- Implicit type conversions in JOIN/WHERE conditions
- Overly nested subqueries that could be CTEs

### Correctness Red Flags
- NULL handling issues (comparing with `=` instead of `IS NULL`)
- Integer division truncation (e.g., `count / total` instead of `count * 1.0 / total`)
- Missing GROUP BY columns (non-aggregated columns in SELECT)
- Ambiguous column references in JOINs
- Date/timezone issues (comparing date to timestamp without cast)
- Off-by-one in date ranges (`<` vs `<=`)

### Readability Red Flags
- No aliases on tables
- Single-letter aliases that aren't obvious
- Deeply nested subqueries (3+ levels)
- Inconsistent casing or formatting
- Missing comments on complex logic

## Step 5: Optimized Rewrite

If you found issues, provide a rewritten version that:
1. Fixes all correctness bugs
2. Applies performance optimizations
3. Improves readability with proper formatting

Format the rewrite as:
- Clean, consistently formatted SQL
- CTEs for readability
- Comments on non-obvious logic
- Explicit column aliases

Add a **diff summary** after the rewrite explaining what changed and why:

```
CHANGES MADE:
✅ Replaced SELECT * with explicit columns
✅ Converted correlated subquery to LEFT JOIN (faster)
✅ Added COALESCE for NULL-safe aggregation
✅ Reformatted with CTEs for readability
⚠️ Added index recommendation: CREATE INDEX idx_orders_customer_id ON orders(customer_id)
```

## Step 6: Index & Warehouse-Specific Recommendations

Based on the detected warehouse, suggest:

### For PostgreSQL:
- Specific `CREATE INDEX` statements
- `EXPLAIN ANALYZE` command to verify improvements
- Relevant `pg_stat_user_tables` checks

### For Snowflake:
- Clustering key recommendations
- Warehouse sizing suggestions
- `RESULT_SCAN(LAST_QUERY_ID())` for profiling

### For BigQuery:
- Partition and clustering suggestions
- `INFORMATION_SCHEMA.JOBS` query to check bytes scanned
- Slot usage considerations

### For Redshift:
- DISTKEY and SORTKEY recommendations
- `STL_QUERY` / `SVL_QUERY_SUMMARY` for profiling

## Response Format

Always structure your response in this order:

1. **🔍 One-Liner** — what this query does in one sentence
2. **📖 Step-by-Step** — execution-order walkthrough
3. **🔑 Key Concepts** — non-trivial SQL features explained (only if present)
4. **📊 Query Flow** — simple visual diagram
5. **⚠️ Issues Found** — problems grouped by severity (🔴 Critical → 🟡 Warning → 🔵 Style)
6. **✅ Optimized Version** — rewritten query with diff summary (only if issues found)
7. **📈 Index / Config Tips** — warehouse-specific recommendations

If the query is clean and well-written, say so! Not every query needs fixing. In that case, skip sections 5-6 and just confirm it looks good with a brief note on why.

## Tone

- Be direct and specific — not vague ("this could be slow" → "this scans the full orders table because the WHERE function prevents index usage")
- Use analogies for complex concepts when helpful
- Assume the user is technically competent but may not know the specific optimization
- Never be condescending about the original query
