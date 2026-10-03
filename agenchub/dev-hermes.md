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
| `origin/main` | `05d4c48` | Force-pushed new history. Production — deployed on Hetzner. **No shared history with feat/multi-tenant.** Contains full multi-tenant stack, insurance, PWA/push, mobile responsive, a11y, security, rebranding, carpark module. |
| `origin/archive/pre-hetzner-migration` | `7723d09` | Old main before the force-push reset. feat/multi-tenant was forked from this. |
| `origin/feat/multi-tenant` | `3ff16ef8` | 47 commits not on main. Forked from old main (archive/pre-hetzner-migration). **No merge-base with current main** — unrelated histories (`git merge-base` returns nothing). `git diff --stat`: 166 files changed, +2,938 / −16,215. **Must NOT merge as-is.** |

## Per-commit inventory (bottom to top)

### Part A — Base from old main (d0b1eca..7723d09, 26 commits)

These were on the old main before the force-push reset. Their content may partially overlap with new main:

| # | Commit | What it adds |
|---|--------|-------------|
| 1 | `d0b1eca` | MCST AI Ops MVP with JWT auth |
| 2 | `93c3648` | Correct Hetzner SIN plan CPX22 in docs |
| 3 | `fcea3f1` | Refactor frontend for Cloudflare Pages static export + Neon compat |
| 4 | `f70e69b` | Revert next.config.js to VPS server mode + deploy report |
| 5 | `15ab207` | Nightly backup script for SQLite + ChromaDB + .env |
| 6 | `dafbfa7` | Add .last_deploy marker file |
| 7 | `baad0fa` | VPS git-pull deploy script (idempotent, ff-only) |
| 8 | `3ef02c4` | Bake NEXT_PUBLIC_API_BASE_URL into frontend build |
| 9 | `9482d4c` | Estate + MCST + User tables, tenant FKs on existing tables |
| 10 | `2592b83` | Migration/seed: 3 estates, 6 MCSTs, 6 users |
| 11 | `5bc82d5` | User auth with role + estate_id + mcst_id in JWT |
| 12 | `0f806c1` | Tenancy scope helpers + admin routes + data-route tenancy filtering |
| 13 | `2e59005` | RAG store + ingestion pass estate_id/mcst_id through metadata |
| 14 | `fe76d7f` | mock_data seed: Acumen Stream Demo + Demo MCST #1 |
| 15 | `35bd419` | Frontend login/layout/sidebar — tenancy-reflective views |
| 16 | `2991699` | Cross-tenant isolation smoke tests (19 pass) |
| 17 | `f099b33` | Expand smoke tests to 41 scenarios, fresh-DB isolation |
| 18 | `415b0fc` | git_deploy: self-heal .venv if missing |
| 19 | `9e699d9` | Remove unused admin_username/admin_password_plain env vars |
| 20 | `65b7db8` | Seed Sunrise + Meridian complaints for sales demo |
| 21 | `7723d09` | Replace demo-account text list with one-click persona buttons |

### Part B — Hermes additions on top (94f8a99..3ff16ef, 26 commits)

These contain features that need selective porting. Some already exist in new main (insurance, super_admin, PDPA classification) but may use a different approach:

| # | Commit | What it adds | Port? |
|---|--------|-------------|-------|
| 22 | `94f8a99` | Sync: pull VPS commits into local mirror (housekeeping) | skip |
| 23 | `f3bec3d` | Insurance Register + Alerts Engine + Vendor Licences + PDPA Classification | **verify** — new main already has insurance (bc17120) and alerts |
| 24 | `b095048` | Telegram inbound webhook — @MCAgencyBot RAG chat | **verify** — Telegram was removed from new main (4d49803) |
| 25 | `eec570a` | Multi-tenant Telegram bot scoping + admin UI | skip (Telegram removed) |
| 26 | `ad98246` | RAG prompt overhaul: synthesize, don't quote, prioritize by doc type | **verify** — relevant if RAG differs |
| 27 | `f20a696` | Group types + announcements + council escalation | **verify** — new main has announcements (0bd6099) |
| 28 | `999c456` | Remove Pydantic validation on group_type (fixes None in PATCH) | **verify** — check if new main has same bug |
| 29 | `4b74bb6` | super_admin role with PDPA-compliant aggregate metrics | **verify** — new main has sysadmin (b7060ff) |
| 30 | `b3c815b` | Super-admin frontend — system dashboard, aggregate metrics | **verify** — new main has super-admin UI |
| 31 | `601f3e6` | Public Privacy Policy + Terms pages (SG PDPA compliance) | **likely port** — check if new main has them |
| 32 | `f5f24d2` | Fix stale demo persona password (12345 → changeme) | skip (demo data differs) |
| 33 | `0220f42` | Fix deploy CORS/env-var pitfalls + multi-agency seed script | **verify** — new main has its own deploy setup |
| 34 | `6c24f31` | Rebrand to Agen C Hub + super-admin UI + hide demo personas in prod | **verify** — new main already rebranded (1d02379) |
| 35 | `8b4f38b` | Properties page with MCST management + super_admin oversight | **verify** — new main has different property mgmt |
| 36 | `1db8f08` | Remove accidentally tracked artifacts | skip (housekeeping) |
| 37 | `8463ae9` | Clean up accidentally tracked artifacts | skip |
| 38 | `c3ae77a` | Clean up tracked artifacts | skip |
| 39 | `2b6b2e6` | Seed realistic demo complaints, work orders, RAG docs, insurance | **verify** — new main has demo data differently |
| 40 | `78e8683` | Add .gitignore for worktrees, generated artifacts, scratch scripts | **port** — safe, additive |
| 41 | `cbbe940` | Clean up | skip |
| 42 | `ceafdc2` | Clean up | skip |
| 43 | `1a6a062` | Clean up | skip |
| 44 | `a580eb2` | Cleanup | skip |
| 45 | `c908eff` | Vendor card UI: show PIC/contact always, move delete into ⋮ menu | **verify** — compare with new main vendor UI |
| 46 | `c024339` | Seed realistic vendor contacts (PIC, phone, email) for demo data | **verify** — compare with new main seed |
| 47 | `3ff16ef` | Document Library CRUD (model + routes + frontend) + admin-login page + logo.svg | **likely port** — completely new feature on this branch |

## Where Hermes works

Hostinger Docker box (`/opt/data/mcst-ai-ops`). No running services — dev/ops environment only.

## Known bugs / debt (verified)

- `replicate_video` MCP server: args format causes npm EINVALIDTAGNAME. Commented out in config (disabled 2026-09-30). Gateway reloaded 2026-10-03.

## Next actions

- **Hermes:** branch `feat/port-to-main` from `origin/main`, cherry-pick only the port-marked commits above.
- **Buddy:** verify which Part A commits are already covered by new main content.
- **Ben:** confirm the port list before Hermes starts cherry-picking.