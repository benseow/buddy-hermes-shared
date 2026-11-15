---
owner: hermes
project: smartnest
updated: 2026-11-16
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

Older drafts (596 for 28 Sep, 598 for 29 Sep, 601 for 1 Oct) have different slugs — they are safe. Only today's draft (currently 604, slug `smartnest-news-2026-10-03`) and today's staging drafts are within scope.

## Recent runs (SGT dates) — before pipeline change (old UPDATE mode)

These runs used the old UPDATE mode (direct PATCH of canonical). New runs will use the staging-draft pipeline instead.

| Day | Mode | Result | WP post | Verified |
|-----|------|--------|---------|----------|
| 27 Sep | NEW POST | Draft created | 590 (published by Ben) | state file + WP API |
| 28 Sep | NEW POST | Draft created | 596 (draft) | state file + WP API |
| 29 Sep | NEW POST | Draft created | 598 (draft) | state file + WP API |
| 30 Sep | UPDATE (a) | **Old: patched canonical 490 directly** | post 490 modified `2026-09-30T07:09:49` | archive footer `"UPDATE MODE (a)"` |
| 1 Oct | NEW POST | Draft created | 601 (draft) | state file + WP API |
| 2 Oct | UPDATE (a) | **Old: patched canonical 443 directly** | post 443 modified `2026-10-02T07:08:19` | archive footer `"UPDATE MODE (a)"` |
| 3 Oct | NEW POST | Draft created | 604 (draft) | state file + WP API |

Posts 443 and 490 are **already live** (published by the old UPDATE mode). They do not need publishing.

## Current pipeline behaviour (verified 14 Nov 2026)

- **NEW POST now publishes itself.** `publish_digest.py` creates the post with `status=future` and `date_gmt` set to 05:00 UTC = **1 PM SGT the same day** — it no longer waits for Stage 2. The Stage 2 (16:00 SGT `promote_drafts.py`) job now only applies **staging drafts** for UPDATE MODE canonicals.
- **Archive filename follows the published slug date.** The cron fires at 23:00 UTC; `publish_digest.py` derives `smartnest-news-YYYY-MM-DD` from the SGT date at run time. When the run starts at or just after 07:00 SGT, that date is the *new* day, so the archive file and slug carry the new date even though the digest content covers the previous day's market window. Verified 13→14 Nov 2026: content = 13 Nov edition (market window 6–13 Nov), slug + archive = `2026-11-14`.
- **Canonical IDs** (stable anchors for UPDATE MODE staging): `443` two-speed market, `490` million-dollar HDB, `283` en bloc reforms, `371` NDR 2026 BTO ceiling.
- **UPDATE MODE staging flow**: write the new-data block to `/tmp/update-section.md`, call `stage_update.py <canonical_id> /tmp/update-section.md` → creates staging draft `smartnest-update-<canonicalID>-YYYY-MM-DD` titled `UPDATE STAGED: <canonical title>`. Stage 2 applies it to the live canonical, republishes with a fresh date, and trashes the staging draft. State file records `stage <id> <stagingID> staged` then `... applied`.

### Recent runs

| Day (SGT) | Mode | Result | WP post | Verified |
|-----|------|--------|---------|----------|
| 4 Nov | UPDATE (a) | staged → applied to canonical 490 | 816 (staging) | state file + WP API |
| 7 Nov | UPDATE (a) | staged → applied to canonical 443 | 818 (staging) | state file + WP API |
| 9 Nov | UPDATE (a) | staged → applied to canonical 490 | 820 (staging) | state file + WP API |
| 11 Nov | UPDATE (a) | staged → applied to canonical 371 | 825 (staging) | state file + WP API |
| 12 Nov | UPDATE (a) | staged → applied to canonical 371 | 828 (staging) | state file + WP API |
| 14 Nov | NEW POST | scheduled `future`, 1 PM SGT | **832** `smartnest-news-2026-11-14` | authenticated REST — no markdown leak, 5 `<h2>` |
| 15 Nov | NEW POST | scheduled `future`, 1 PM SGT | **835** `smartnest-news-2026-11-15` | public REST — published on schedule |
| 16 Nov | NEW POST | scheduled `future`, 1 PM SGT | **838** `smartnest-news-2026-11-16` | authenticated REST — body clean (5 `<h2>`, no mode footer); **live title malformed**, needs repair before 1 PM SGT |

### Verified 16 Nov 2026 — post 838

- Marina Collection lead: 36/38 units sold in 100 minutes on 14 Nov, S$103.5M, S$1,342 psf.
- Files: `latest.md` + `archive/2026-11-16.md`, identical (12,014 bytes).
- `status=future`, `date=2026-11-16T13:00:00` site / `date_gmt=2026-11-16T05:00:00` → 1 PM SGT 16 Nov. Hero media_id 837.
- Body: 15,475 chars, 5 `<h2>`, 0 raw `## `, no `_MODE:` leak, mode footer stripped.
- **Known issue:** the script's fallback title extractor consumed the first bold paragraph, so the live title includes the lead paragraph. The source digest now carries a bold one-line title before the first H2 for deterministic extraction on future runs. Live title still needs correction via an allowed script path (direct WP PATCH is not permitted by the cron rules).

### Verified 14 Nov 2026 — post 832 checks

- `status=future`, `date=2026-11-14T13:00:00` site / `date_gmt=2026-11-14T05:00:00` → publishes 1 PM SGT 14 Nov.
- Content length 35,704 chars. **No `_MODE:` leak**, no raw markdown leakage (`## ` count 0, `**Source:` count 0), 5 proper `<h2>` tags. The `_NEW POST.` mode footer is correctly stripped from the published body.
- Hero image: Pexels `Singapore Orchard Road shopping` → `media_id=831`.
- `publish-state.txt` line: `2026-11-14 832 smartnest-news-2026-11-14`.

## Security: PHP2Shell (CVE-2026-109618/109619) — 22 Oct 2026

Critical unauthenticated RCE in PHP core; fixed only in 8.5.12/8.4.27/8.3.36/8.2.35 (released 21 Oct). smartnest.sg was running **PHP 8.3.35 (vulnerable)** on Hostinger as of 22 Oct probes; Hostinger says they are rolling out the patched builds platform-wide (they also turned off session.upload_progress as interim). Hermes to re-verify via `X-Powered-By` header until it shows 8.3.36+. Nothing for Buddy to patch (shared hosting = Hostinger's job). Full note: `/opt/data/memory-vault/08 - Resources/PHP2Shell-security-event.md`

## Known problems (verified)

- All digests are created as **drafts** — they never auto-publish. The Stage 2 job publishes today's draft at 16:00 SGT if Ben hasn't got there first.
- The state file is named `publish-state.txt` but the posts are drafts. Name is historical.
- The old UPDATE mode (direct PATCH) was replaced on 3 Oct 2026. Records 443 and 490 remain as they are — no staging drafts exist for them.

## Next actions

- **Hermes:** none — both stages active. First full pipeline test: today's draft 604 publishes at 16:00 SGT if still draft; older drafts 596/598/601 are untouched.
- **Buddy:** verify the Stage 2 cron fires at 08:00 UTC (16:00 SGT).
- **Ben:** no action needed — if you want 604 promoted earlier, publish it manually in WP admin; otherwise 16:00 SGT handles it.