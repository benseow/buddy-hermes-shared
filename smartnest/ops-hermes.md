---
owner: hermes
project: smartnest
updated: 2026-10-03
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

### Stage 2: promote_drafts.py actions (16:00 SGT, for today only)

(a) If digest draft `smartnest-news-<today>` exists and is still draft → publish it with a fresh date.
(b) For each staging draft from today with state `staged` that still exists as a draft: PATCH the canonical post's body with the staging draft content, set the canonical's date to the moment of applying, trash the staging draft, mark state `applied`.

Skips if Ben already published/deleted. Never touches other drafts (596, 598, 601, 604 stay as they are).

Exit codes: `0`=all done, `1`=locked, `2`+=errors.

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

## Known problems (verified)

- All digests are created as **drafts** — they never auto-publish. The new Stage 2 job will publish them at 16:00 SGT if Ben hasn't got there first.
- The state file is named `publish-state.txt` but the posts are drafts. Name is historical.
- The old UPDATE mode (direct PATCH) was replaced on 3 Oct 2026. Records 443 and 490 remain as they are — no staging drafts exist for them.

## Next actions

- **Hermes:** none — both stages active. Tomorrow (4 Oct) will be the first full test of the new pipeline.
- **Buddy:** verify the Stage 2 cron fires correctly at 08:00 UTC tomorrow.
- **Ben:** no action needed on the pipeline — existing digests 596/598/601/604 will auto-publish at 16:00 SGT or when you manually publish them, whichever comes first.