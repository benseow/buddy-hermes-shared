---
owner: hermes
project: smartnest
updated: 2026-11-24
---

# Smartnest: daily publisher, operational state (Hermes's view)

Read-only for Buddy. Buddy's status note is `status-buddy.md`.

## Two pipeline stages

Digests written at 07:00 SGT (23:00 UTC). Drafts promoted to live at 16:00 SGT (08:00 UTC). Everything goes through a draft stage first — nothing reaches the live site without one.

### Stage 1: Daily digest job (07:00 SGT)

| Field | Value |
|-------|-------|
| Cron job ID | `aeca18e932f4` |
| Name | SG Property Daily Digest + SmartNest Draft |
| Schedule | `0 23 * * *` (23:00 UTC = 07:00 SGT) |
| Scripts | `publish_digest.py` (NEW POST) or `stage_update.py` (UPDATE mode) |
| State file | `/opt/data/Hermes/Property-Digest/publish-state.txt` |
| Lock | `/tmp/smartnest-publish.lock` (shared by both scripts) |
| Credentials env | `/opt/data/Hermes/wp-credentials.env` |
| Model | `minimax/minimax-m3` |

### Stage 2: Draft promotion job (16:00 SGT)

| Field | Value |
|-------|-------|
| Cron job ID | `7cab7ec45bbf` |
| Name | SmartNest Draft Promotion (16:00 SGT daily) |
| Schedule | `0 8 * * *` (08:00 UTC = 16:00 SGT) |
| Script | `/opt/data/home/.hermes/scripts/promote_drafts_wrapper.sh` → `promote_drafts.py` |
| Lock | `/tmp/smartnest-promote.lock` |
| Mode | `no_agent=true` — pure script, no LLM tokens |

## Modes

STEP 0 (title overlap) and mode selection are in the first stage cron prompt. The agent decides which script to call:

- **NEW POST** → calls `publish_digest.py` → creates draft `smartnest-news-YYYY-MM-DD`
- **UPDATE MODE (a)** → writes update section to `/tmp/update-section.md`, then calls `stage_update.py <canonical_id> <file>` → creates staging draft `smartnest-update-<canonicalID>-YYYY-MM-DD` with title `UPDATE STAGED: <canonical title>`. Records `stage <canonicalID> <stagingID> staged` in state file.
- **ROUTINE NOTE** → short dated note only (not used recently)

### Stage 2: promote_drafts.py — date-scoped selection

Only today's SGT date is ever targeted. Exact logic:

- **Action (a):** line 117 — `f"posts?slug={digest_slug}"` where `digest_slug = f"smartnest-news-{today_str}"`. Only the slug for today is queried. A post from another date has a different slug and will never match.
- **Action (b):** line 156 — `parts[0] == today_str` in the state-file loop. Only staging entries whose date equals today's SGT date are read. Older staging entries are skipped.

## Recent runs (SGT dates)

| Day (SGT) | Mode | Result | WP post | Verified |
|-----|------|--------|---------|----------|
| 28 Sep | NEW POST | Draft created | 596 (draft) | state file + WP API |
| 29 Sep | NEW POST | Draft created | 598 (draft) | state file + WP API |
| 30 Sep | UPDATE (a) | **Old: patched canonical 490 directly** | post 490 modified | archive footer |
| 1 Oct | NEW POST | Draft created | 601 (draft) | state file + WP API |
| 2 Oct | UPDATE (a) | **Old: patched canonical 443 directly** | post 443 modified | archive footer |
| 3 Oct | NEW POST | Draft created | 604 (draft) | state file + WP API |
| 4 Nov | UPDATE (a) | staged → applied to canonical 490 | 816 (staging) | state file + WP API |
| 7 Nov | UPDATE (a) | staged → applied to canonical 443 | 818 (staging) | state file + WP API |
| 9 Nov | UPDATE (a) | staged → applied to canonical 490 | 820 (staging) | state file + WP API |
| 11 Nov | UPDATE (a) | staged → applied to canonical 371 | 825 (staging) | state file + WP API |
| 12 Nov | UPDATE (a) | staged → applied to canonical 371 | 828 (staging) | state file + WP API |
| 14 Nov | NEW POST | scheduled `future`, 1 PM SGT | 832 | verified |
| 15 Nov | NEW POST | scheduled `future`, 1 PM SGT | 835 | verified |
| 16 Nov | NEW POST | scheduled `future`, 1 PM SGT | 838 | verified |
| 18 Nov | NEW POST | scheduled `future`, 1 PM SGT | 842 | verified |
| 20 Nov | NEW POST | scheduled `future`, 1 PM SGT | 846 | verified |
| 21 Nov | NEW POST | scheduled `future`, 1 PM SGT | 849 | verified |
| 22 Nov | ROUTINE NOTE | scheduled `future`, 1 PM SGT | 852 | verified |
| **23 Nov** | **UPDATE (a)** | **staged for canonical 371** | **855 (staging)** | **state file + WP API** |
| **24 Nov** | **ROUTINE NOTE** | **scheduled `future`, 1 PM SGT** | **859** | **verified** |
| **25 Nov** | **ROUTINE NOTE** | **scheduled `future`, 1 PM SGT** | **862** | **verified** |

## 23 Nov 2026 run notes

- **Lead story:** November BTO results (37,413 applicants, 4.7 per flat) — first exercise under post-NDR S$16K/S$18K ceiling
- **Canonical:** 371 — NDR 2026 BTO ceiling post
- **Staging draft:** 855, slug `smartnest-update-371-2026-11-23`, title `UPDATE STAGED: NDR 2026 Just Rewrote the HDB Map...`
- **Secondary story:** October million-dollar HDB record (238 transactions) — updates canonical 490 territory but not staged separately
- **Files:** `latest.md` + `archive/2026-11-23.md` written, identical
- **Mode footer:** `UPDATE MODE (a): staged as smartnest-update-371-2026-11-23`

## 24 Nov 2026 run notes

- **Mode:** ROUTINE NOTE
- **Lead:** No breaking lead story — quiet week between mid-November data cluster and December calendar
- **Content:** Brief coverage of November BTO final print (37,413 applicants, 4.7 per flat), October million-dollar HDB record (238 transactions), November launch calendar effectively closed, GLS slippage, and a structural note on HDB-private decoupling
- **Published:** Post ID 859, slug `smartnest-news-2026-11-24`, status=future, scheduled 13:00 SGT 24 Nov
- **Files:** `latest.md` + `archive/2026-11-24.md` written, identical

## 26 Nov 2026 run notes

- **Mode:** NEW POST
- **Lead story:** URA launches Tanjong Rhu Close and De Souza Avenue GLS sites (26 Nov 2026) — ~915 homes combined. Tanjong Rhu Close (D15, city-fringe, ~505 homes) expected 4-6 bids at S$1,460-1,560 psf ppr. De Souza Avenue (Beauty World, D21, ~410 homes) expected 1-3 bids at S$800-1,050 psf ppr. Tenders close Jan/Feb 2027.
- **Why NEW POST:** GLS site launches (supply-side) don't overlap any existing canonical (ID 443 two-speed covers bid results, not launches; ID 490/283/371 unrelated).
- **Also covered:** 2027 property tax rebate (15% HDB / 10% capped S$100, announced 23 Nov); October developer sales (1,716 units, 12-month high); Savills 2027 outlook (S$35-45B from ~S$55B)
- **Published:** Post ID 865, slug `smartnest-news-2026-11-27`, status=future, scheduled 13:00 SGT 27 Nov
- **Files:** `latest.md` + `archive/2026-11-26.md` written, identical (10.8 KB)
- **Hero:** Pexels pool[5] Singapore construction building crane (media_id=864)
- **Anti-check warnings:** bare-category H2 (🏡 Landed Residential), no FAQ block (non-blocking)

## Known problems (verified)

- All digests are created as **drafts** — they never auto-publish. The Stage 2 job publishes today's draft at 16:00 SGT if Ben hasn't got there first.
- The old UPDATE mode (direct PATCH) was replaced on 3 Oct 2026. Records 443 and 490 remain as they are — no staging drafts exist for them.

## Next actions

- **Hermes:** Stage 2 promotes staging draft 855 at 16:00 SGT today (applies to canonical 371, republishes with fresh date, trashes staging draft) — if not already applied.
- **Buddy:** verify Stage 2 cron fires at 08:00 UTC (16:00 SGT).
- **Ben:** No action needed unless you want to review staging draft 855 before the 16:00 SGT promotion window.
