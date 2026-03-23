# Pipeline Toolkit

Three Claude Skills that cover the full lifecycle of building a production data pipeline — from gathering requirements to going live.

## The 3 Skills

```
discovery-SKILL.md → architecture-SKILL.md → checklist-SKILL.md
  (what do we need?)     (how do we build it?)    (is it production-ready?)
```

### 1. Pipeline Discovery (`discovery-SKILL.md`)
Runs a structured requirements session in 3 rounds:
- **The Why** — business context, success criteria, criticality
- **The What** — data sources, volume, velocity, consumers
- **The Constraints** — team, budget, compliance + questions people never ask (backfill strategy, PII handling, schema change plans, cost attribution)

Outputs a clean requirements document. Flags risks early.

### 2. Pipeline Architecture Designer (`architecture-SKILL.md`)
Takes requirements and outputs a complete blueprint:
- Architecture pattern (modern data stack, lakehouse, streaming, simple warehouse)
- One recommended tool per layer with reasoning, runner-up, and what to avoid
- Cost estimates with scaling triggers
- Concrete data flow walkthroughs including failure scenarios
- Trade-off analysis for key decisions
- Anti-pattern flags (resume-driven development, premature real-time, tool sprawl)

Covers: ingestion, storage, transformation, orchestration, data quality, BI, catalog, monitoring, CI/CD.

### 3. Pipeline Production Checklist (`checklist-SKILL.md`)
Audits your pipeline across 5 categories:

| Category | What It Catches |
|----------|----------------|
| **Failure Handling** | No retries, silent partial loads, orchestrator monitoring itself |
| **Monitoring & Alerting** | Missing freshness checks, alert fatigue, no escalation path |
| **Backfill & Recovery** | Not idempotent, no raw preservation, no disaster recovery plan |
| **Scalability Traps** | Full refreshes on 500M rows, Snowflake credit bonfires, small file problems |
| **Operational Readiness** | No runbooks, hardcoded credentials, no staging environment |

Ends with "What Will Break First" — the most honest section.

## How to Use

Upload any or all 3 `.SKILL.md` files to Claude:

1. Go to [claude.ai](https://claude.ai) → **Settings** → **Profile** → **Custom Skills**
2. Click **Add Skill** → upload the skill file(s)
3. They activate automatically based on your conversation

**Use all 3 in sequence** for a full pipeline build, or **use individually**:
- Just need requirements? → `discovery-SKILL.md`
- Know what you need, want tool recommendations? → `architecture-SKILL.md`
- Already built something, want an audit? → `checklist-SKILL.md`
