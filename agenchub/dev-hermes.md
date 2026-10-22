---
owner: hermes
project: agenchub
updated: 2026-10-22
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

## Security: Node.js vulnerabilities (22 Oct 2026)

Node.js released security patches on 21 Oct 2026 fixing several high-severity CVEs. Most serious allows remote code execution.

| Environment | Node.js version | Status | Action taken / needed |
|---|---|---|---|
| Hermes dev box (Hostinger Docker) | v22.22.3 → **v22.23.4** | **FIXED** | Upgraded standalone binary at `~/.local/node/bin/`. PATH updated in `~/.bashrc`. |
| Hetzner VPS (production — 187.77.137.202) | **v18.19.1** (EOL) | **UNFIXED — CRITICAL** | Installed from Ubuntu 24.04 apt defaults. Node 18 has been EOL since Apr 2024 — no security patches. Powers 2 Next.js frontends: `mcst-frontend` (port 3000) and `mcst-finance-frontend` (port 3001). **Buddy** to handle — needs NodeSource repo or nvm to install Node 22+. |
| Hostinger shared hosting (smartnest.sg etc.) | N/A (PHP) | Not affected | — |

Relevant advisory: Node.js 22 needs 22.23.4+; 24 needs 24.22.1+; 26 needs 26.12.1+. Node 20 and older are EOL — no fix available, must upgrade major version.

## Known bugs / debt (verified)

- `replicate_video` MCP server: args format causes npm EINVALIDTAGNAME. Commented out in config (disabled 2026-09-30). Gateway reloaded 2026-10-03.

## Next actions

- **Buddy:** upgrade Node.js on Hetzner VPS from EOL v18.19.1 → v22+ (NodeSource or nvm). Restart both `mcst-frontend` and `mcst-finance-frontend` after.
- **Hermes:** none — checkout clean on main. Node.js dev box upgraded.
- **Ben:** close out the feat/multi-tenant branch as superseded.