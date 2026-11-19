---
owner: hermes
project: monday-content
updated: 2026-11-20
---

# Monday-content: weekly AI-visibility & reviews research feed (Hermes's view)

Read-only for Buddy. Hermes runs the data collection; the output repo feeds Buddy's content agent.

## What

Plain Python (stdlib only) script that collects public posts about (a) AI visibility for local businesses and (b) online reviews (getting them, fake/unfair, GBP problems). Covers Singapore + US/UK/AU/CA.

No AI model used — cost is ~US$0.10/week in DataForSEO API fees.

## Paths

| Item | Path |
|------|------|
| Script | `/opt/data/Hermes/research/research_weekly.py` |
| Queries | `/opt/data/Hermes/research/queries.json` |
| Credentials env | `/opt/data/Hermes/research/research.env` (Ben manages) |
| Output (local) | `/opt/data/Hermes/research/{latest.md, weekly/YYYY-MM-DD.md}` |
| Published repo | `b2mediaco/b2media-research-feed` (public) |
| State file | `/opt/data/Hermes/research/run-state.txt` (idempotency per ISO week) |

## Schedule

`0 22 * * 0` (Sunday 22:00 UTC = Monday 06:00 SGT). VPS is UTC.

Hermes cron job ID: `e04416431fd5` — no_agent=true, local delivery.

## Exit codes

| Code | Meaning |
|------|---------|
| 0 | Full success — all sources complete |
| 4 | Partial — some DataForSEO tasks abandoned or YouTube queries failed |
| 5 | Idempotency skip — this ISO week already run |
| 6 | Git error — output files exist locally but not pushed |

## Last run

2026-11-20 (Week 47), exit 4 (1 DFS task abandoned at 25-min deadline).
Cost: US$0.1007. 130 items collected. Commit `1fbcf96`.