# Claude Skills

A collection of custom Claude Skills for data engineering and development workflows.

## What are Claude Skills?

Claude Skills are markdown files that define how Claude should handle specific tasks. Upload a `.md` file to Claude's Custom Skills, and it activates automatically based on context.

No code. No API. No deployment. Just a markdown file.

## Skills

| Skill | Description | Link |
|-------|-------------|------|
| **SQL Query Explainer** | Paste any SQL → get plain English explanation, performance audit, and optimized rewrite | [→ View](./sql-query-explainer/) |
| **Pipeline Toolkit** | 3 skills covering the full pipeline lifecycle — discovery, architecture design, and production readiness audit | [→ View](./pipeline-toolkit/) |

### Pipeline Toolkit (3 skills in 1 folder)

```
discovery-SKILL.md → architecture-SKILL.md → checklist-SKILL.md
  (requirements)        (design & tools)       (audit & go-live)
```

| Skill File | What It Does |
|------------|-------------|
| `discovery-SKILL.md` | Asks the right requirements questions before you build anything |
| `architecture-SKILL.md` | Recommends tech stack, costs, trade-offs, and architecture diagram |
| `checklist-SKILL.md` | Catches scalability traps, missing monitoring, backfill gaps, and operational blind spots |

## How to Use Any Skill

1. Open the skill folder and download the `SKILL.md` file(s)
2. Go to [claude.ai](https://claude.ai)
3. Open **Settings** → **Profile** → **Custom Skills**
4. Click **Add Skill** → upload the `.md` file
5. Done — the skill activates automatically when relevant

## Upcoming Skills

- [ ] dbt Model Generator
- [ ] Airflow DAG Builder
- [ ] Data Quality Rules Generator
- [ ] Pipeline Failure Debugger
- [ ] SQL to dbt Converter

## Contributing

Have an idea for a skill? Open an issue or submit a PR.

## License

MIT
