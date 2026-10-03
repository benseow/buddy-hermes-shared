---
owner: buddy
project: agenchub
updated: 2026-10-03
---
# AgenCHub: status (Buddy's view)

Read-only for Hermes. Hermes's development view is `dev-hermes.md`. Ben's shorthand: "ahub".

## What it is
SaaS for Singapore housing-estate managing agents (MAs / MCSTs). Modules built and deployed: complaints (audit trail, SLA badge), inspections (due-date grid, vendor assignment), renewal tasks (certificates, licences, vendor contracts), assets and warranties, insurance, carpark and vehicle registration, AI-drafted announcements, Knowledge Vault (RAG), per-estate feature toggles, support view sessions, CM-to-MCST scoping, PWA and web push alerts.

## Stage
**Pre-launch. Zero real paying customers.** "Acumen" and the other agencies in older notes are Ben's own seed and test data. Sales page live at agenchub.com since 18 Sep 2026; demo booking via Cal.com.

## Environments
- **Production:** Hetzner Cloud VPS (Singapore), FastAPI backend + Next.js frontend as native systemd services behind Caddy, behind Cloudflare. Code at `/opt/mcst-ai-ops` on that box.
- **Sandbox:** OVH box, `ma.acumenstream.com` / `api.acumenstream.com`. Reset to match production on 20 Sep 2026.
- **GitHub:** `benseow/mcst-ai-ops-`. `main` = what is live. Backups: encrypted nightly snapshot to private `benseow/mcst-backups`.

## Verified 3 Oct 2026
- `feat/multi-tenant` on origin is at `3ff16ef8` (pushed by Hermes; verified by Buddy with `git ls-remote`). Contains 16 earlier commits plus document library CRUD, admin-login page and logo rebranding.
- **Branch relationship, verified by Buddy against GitHub (bare clone, 3 Oct 2026):** `main` = `05d4c48` (live production code). `feat/multi-tenant` (`3ff16ef8`) shares **no common ancestor with `main`**; it is 26 commits on top of `archive/pre-hetzner-migration` (`7723d09`, the July pre-Hetzner history that `main` was deliberately reset away from on 22 Aug 2026). `git diff main feat/multi-tenant` = 166 files, +2,938 / -16,215. **Do not merge it into `main`**: it would delete large parts of current production, including the Sep 2026 security fixes. Its genuinely new work (document library, admin-login page, logo, seed data, PDPA pages) would have to be ported selectively onto a branch cut from `main`, and reviewed first (e.g. the 22 Aug audit found hardcoded demo credentials on login pages).
- `dev-hermes.md` as pushed in `a507157` says `7723d09` is `main`, that the branches share a merge-base, and that the work is "all additive, meant to merge". Those three statements do not match the repo. Hermes to correct its own note.

## Decisions in force
- Scope: ops-only (no financials, no hardware). Provisional exception: integrate REALTIMME, Tier 1 only, behind a default-off per-estate toggle (3 Sep). Ben's stance 3 Oct: stay tight, flexible if the market demands.
- Telegram integration removed entirely (7 Sep). Alert delivery = in-app + email (SES) + web push.
- Payment take-rate parked. Near-term shape is subscription only. No pricing decision made; working range 1.50-3.00 per unit per month sits inside the competitor band.
- Write the brand as "Agen C Hub" (spaced) in any text-to-speech or video prompt.

## Open / blocked
- Email dispatch: AWS SES identity and inbound routing are live; **the app-side code that sends alerts and announcements by email is not built.**
- Before 15 Nov 2026: Cloudflare Origin CA cert for Caddy (origin cert expires). UFW lock to Cloudflare IPs pending.
- Registrar transfer to Cloudflare blocked by ICANN 60-day lock until 16 Oct 2026.
- Sales page finance wording still overclaims REALTIMME availability.
- Demo and training videos: scripts and voiceovers done; final assembly pending.
- CueVue avatar widget live on agenchub.com; credit cap still open; refund decision due by 4 Oct 2026.
- Exploring a PSG-qualified-company vehicle (PSG ceased 29 Sep 2026, replaced by EDGE). Meeting next week. Exploratory only.

## Next actions
- Buddy: shared-memory build (this repo); host-side disk/log check on the VPS.
- Hermes: correct the branch section of `dev-hermes.md` (see above); list which of the 26 commits hold work worth porting.
- Buddy (needs Ben's go-ahead): read-only review of the 26 commits to decide what to port.
