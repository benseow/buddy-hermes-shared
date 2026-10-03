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
| `origin/main` | `05d4c48` | Force-pushed new history (22 Aug 2026). Production — deployed on Hetzner. Hermes local checkout reset to this. |
| `origin/archive/pre-hetzner-migration` | `7723d09` | Old main before the force-push. feat/multi-tenant was forked from this. |
| `origin/feat/multi-tenant` | `3ff16ef8` | 47 commits (26 old-base + 21 Hermes additions) on top of archive branch. **No merge-base with main** — unrelated histories. Kept on origin as backup only. **Nothing to port.** |

## Port outcome

Buddy compared every feat/multi-tenant commit against main and found that all genuinely new work (documents.py, Document model, documents/page.tsx, admin-login/page.tsx, logo.svg, privacy/page.tsx, terms/page.tsx) already exists on main **in newer form**. The branch versions are older, lack security hardening (e.g. missing `scope_for_new_row` tenancy check in documents.py, hardcoded demo credentials in admin-login). All 47 commits are superseded by what's already on main.

Hermes checkout: `git fetch origin`, `git checkout main`, `git reset --hard origin/main` → HEAD `05d4c48`, clean.

## Where Hermes works

Hostinger Docker box (`/opt/data/mcst-ai-ops`). No running services — dev/ops environment.

## Known bugs / debt (verified)

- `replicate_video` MCP server: args format causes npm EINVALIDTAGNAME. Commented out in config (disabled 2026-09-30). Gateway reloaded 2026-10-03.

## Next actions

- **Hermes:** none — checkout clean on main, feat branch preserved as archive only.
- **Buddy:** continue host-side maintainence on Hetzner VPS.
- **Ben:** close out the feat/multi-tenant branch as superseded.