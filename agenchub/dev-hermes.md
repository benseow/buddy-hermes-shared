---
owner: hermes
project: agenchub
updated: 2026-10-03
---

# AgenCHub: development state (Hermes's view)

Read-only for Buddy. Buddy's status note is `status-buddy.md`.

## Branches

All in `benseow/mcst-ai-ops-`.

| Branch | HEAD | Notes |
|--------|------|-------|
| `main` (origin) | `7723d09` | Production branch. Currently deployed on Hetzner VPS (Buddy's env). Contains full multi-tenant stack, super-admin UI, insurance, complaints, RAG, Telegram removal, rebranding. |
| `feat/multi-tenant` (origin) | `3ff16ef8` | Forked from main at `7723d09`. **26 commits ahead** of main. Contains: Document Library CRUD (model + routes + frontend page), admin-login page, logo.svg rebranding, collapsed demo personas, realistic seed data (vendors/complaints/work-orders), PDPA compliance pages. **Meant to merge into `main` when ready** — all additive, no destructive changes. |
| `wt/t_*` (local) | — | Git worktree stashes, ignore. |

Merge-base of main and feat/multi-tenant = `7723d09` (current main HEAD, verified `git merge-base`). So `feat/multi-tenant` contains everything in main plus its own 26 commits.

## Where Hermes works

Hermes edits `feat/multi-tenant` on the Hostinger Docker box (this machine, `/opt/data/mcst-ai-ops`). No running backend/frontend services on this box — it's a development/ops environment. Deploy to production (Hetzner VPS) is Buddy's domain.

## In progress

- Document Library (backend + frontend): code written and committed at `3ff16ef8`. Not yet deployed.
- `feat/multi-tenant` needs review and merge to `main` before it hits production.

## Known bugs / debt (verified)

- `replicate_video` MCP server: args format `'[\"-y\", \"mcp-replicate\"]'` causes npm EINVALIDTAGNAME. Commented out in Hermes config (disabled 2026-09-30). Gateway reloaded 2026-10-03.
- mcp-stderr.log has 518 restart loops from `replicate_video` — now quiescent.

## Next actions

- **Hermes:** none pending on development side currently.
- **Buddy:** review `feat/multi-tenant` for merge readiness, or rebase and merge to `main`.
- **Ben:** decide whether `feat/multi-tenant` merges now or waits.