---
owner: hermes
project: smartnest
updated: 2026-11-28
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
| Model | `deepseek/deepseek-v4-flash` |

### Stage 2: Draft promotion job (16:00 SGT)

| Field | Value |
|-------|-------|
| Cron job ID | `7cab7ec45bbf` |
| Name | SmartNest Draft Promotion (16:00 SGT daily) |
| Schedule | `0 8 * * *` (08:00 UTC = 16:00 SGT) |
| Script | `/opt/data/home/.hermes/scripts/promote_drafts_wrapper.sh` → `promote_drafts.py` |
| Lock | `/tmp/smartnest-promote.lock` |
| Mode | `no_agent=true` — pure script, no LLM tokens |

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
| **27 Nov** | **NEW POST** | **scheduled `future`, 1 PM SGT** | **865** | **verified** |
| **28 Nov** | **UPDATE (a)** | **staged for canonical 490** | **867 (staging)** | **state file + WP API** |

## 27 Nov 2026 run notes

- **Lead story:** URA launches Tanjong Rhu Close and De Souza Avenue GLS sites (26 Nov 2026) — ~915 homes combined. Tanjong Rhu Close (D15, city-fringe, ~505 homes) expected 4-6 bids at S$1,460-1,560 psf ppr. De Souza Avenue (Beauty World, D21, ~410 homes) expected 1-3 bids at S$800-1,050 psf ppr. Tenders close Jan/Feb 2027.
- **Why NEW POST:** GLS site launches (supply-side) don't overlap any existing canonical.
- **Also covered:** 2027 property tax rebate (15% HDB / 10% capped S$100, announced 23 Nov); October developer sales (1,716 units, 12-month high); Savills 2027 outlook (S$35-45B from ~S$55B)
- **Published:** Post ID 865, slug `smartnest-news-2026-11-27`, status=future, scheduled 13:00 SGT 27 Nov
- **Files:** `latest.md` + `archive/2026-11-26.md` written, identical (10.8 KB)

## 28 Nov 2026 run notes

- **Mode:** UPDATE MODE (a) on canonical ID 490 (million-dollar HDB)
- **Lead story:** Huttons Data Analytics 2027 outlook — million-dollar HDB flat transactions forecast at 2,400-2,800 in 2027, up from ~2,100 in 2026. The wider HDB resale market softened 0.5% in 2026 (first decline since 2019), while the million-dollar tail continues growing.
- **Why UPDATE MODE (a):** The million-dollar HDB 2027 forecast directly extends the thesis of ID 490 (million-dollar HDB premium growing against a softening RPI). Checked ID 443 (last applied 7 Nov), ID 283 (last 20 Oct), ID 371 (last 12 Nov) — none overlap.
- **Secondary stories:** Executive flat record S$1.699M (Sin Ming Avenue); Woodlands executive S$1.28M; Private home pipeline 12,613-14,800 units (14-yr high); URA GFA exemption relaxations + Green Mark funding; EdgeProp weekly deal data.
- **Staging draft:** ID 867, slug `smartnest-update-490-2026-11-28`, title `UPDATE STAGED: Why HDB Flats Keep Breaking Million-Dollar Records While the Resale Index Falls`
- **Files:** `latest.md` + `archive/2026-11-28.md` written, identical (12.8 KB)

## Known problems (verified)

- All digests are created as **drafts** — they never auto-publish. The Stage 2 job publishes today's draft at 16:00 SGT if Ben hasn't got there first.
- The old UPDATE mode (direct PATCH) was replaced on 3 Oct 2026. Records 443 and 490 remain as they are — no staging drafts exist for them.
- Duplicate line in publish-state.txt was cleaned up (sort -u).

## Next actions

- **Stage 2 (16:00 SGT):** Promote staging draft 867 to live canonical 490 (applies update, republishes with fresh date, trashes staging draft).
- **Buddy:** verify Stage 2 cron fires correctly for staging-to-canonical application.
- **Ben:** No action needed unless you want to review staging draft 867 before the 16:00 SGT promotion window.