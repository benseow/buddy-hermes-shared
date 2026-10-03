---
owner: hermes
project: smartnest
updated: 2026-10-03
---

# Smartnest: daily publisher, operational state (Hermes's view)

Read-only for Buddy. Buddy's status note is `status-buddy.md`.

## The job

| Field | Value |
|-------|-------|
| Cron job ID | `aeca18e932f4` |
| Name | SG Property Daily Digest + SmartNest Draft |
| Schedule | `0 23 * * *` (23:00 UTC = 07:00 SGT next day) |
| Script | `/opt/data/Hermes/Property-Digest/publish_digest.py` (Python) |
| State file | `/opt/data/Hermes/Property-Digest/publish-state.txt` — records each date, WP post ID and slug for posts created |
| Lock | `/tmp/smartnest-publish.lock` (flock-based run guard) |
| Credentials env | `/opt/data/Hermes/wp-credentials.env` (never in this repo) |
| Skill | none — the cron prompt contains all the agent logic inline |
| Model | `minimax/minimax-m3` |

## Modes

STEP 0 (title overlap) and mode selection are in the **cron prompt logic**, not in `publish_digest.py`. The script only creates a new WP draft post from `latest.md`. The agent decides:

- **NEW POST mode** — if the lead story doesn't overlap an existing canonical → calls publish_digest.py → creates a draft with deterministic slug `smartnest-news-YYYY-MM-DD`
- **UPDATE MODE (a)** — if the story overlaps an existing canonical → PATCH the canonical's content and reschedule to surface at top-of-feed at 15:00 SGT the next day. No new post created, so state file has no entry.
- **ROUTINE NOTE mode** — for slow-news days (hasn't been used on any of the tracked runs)

Exit codes from publish_digest.py: 0=published, 1=locked, 2=state-file skip, 3=WP slug/title match skip, 4=input error, 5+=failure.

## Recent runs (SGT dates)

All runs produced a digest archive file. Verified against: the archive mtime, the state file, and the WP REST API (authenticated, 3 Oct 2026).

| Day (SGT) | Mode | Result | WP post | Evidence |
|-----------|------|--------|---------|----------|
| 27 Sep | NEW POST | Draft created | 590 (published by Ben) | state: `2026-09-27 590 smartnest-news-2026-09-27`; WP API: status=publish |
| 28 Sep | NEW POST | Draft created | 596 (draft) | state: `2026-09-28 596`; WP API: status=draft, slug `smartnest-news-2026-09-28` |
| 29 Sep | NEW POST | Draft created | 598 (draft) | state: `2026-09-29 598`; WP API: status=draft |
| 30 Sep | UPDATE (a) | Patched canonical 490 | — | Archive footer: "UPDATE MODE (a): HDB million-dollar canonical PATCHED"; WP API: post 490 modified `2026-09-30T07:09:49`, re-scheduled to `2026-10-01T07:00:00` |
| 1 Oct | NEW POST | Draft created | 601 (draft) | state: `2026-10-01 601`; WP API: status=draft |
| 2 Oct | UPDATE (a) | Patched canonical 443 | — | Archive footer: "two-speed canonical (ID 443) PATCHED"; WP API: post 443 modified `2026-10-02T07:08:19`, re-scheduled to `2026-10-03T07:00:00` |
| 3 Oct | NEW POST | Draft created | 604 (draft) | state: `2026-10-03 604`; WP API: status=draft |

**Why Buddy's public REST API check showed no digest after 27 Sep:** Posts 596, 598, 601, 604 exist only as **drafts** (not published). Only 590 was published. The UPDATE-mode runs modified existing posts (443, 490) without creating new ones, so no new digest posts appear in the public API.

## What touched posts 443 and 490

Both were modified by the cron's UPDATE MODE (a):

- **Post 490** (`hdb-million-dollar-flats-vs-falling-resale-index`): Modified `2026-09-30T07:09:49` by the 30 Sep cron run. The digest (archive `2026-09-30.md`) had a Pinnacle@Duxton S\$1.72M record that overlapped with 490's thesis, so UPDATE mode patched 490 with the new data points and re-scheduled it to 1 Oct at 07:00 UTC.
- **Post 443** (`singapore-two-speed-property-market`): Modified `2026-10-02T07:08:19` by the 2 Oct cron run. The digest (archive `2026-10-02.md`) had the Q3 URA flash showing a re-ordered two-speed dynamic, so UPDATE mode patched 443 with Q3 data and re-scheduled to 3 Oct at 07:00 UTC.

## Known problems (verified)

- All digests are created as **drafts** — they never auto-publish, so they don't appear on the public site until Ben publishes them. Only post 590 has been published. The other 4 drafts (596, 598, 601, 604) plus 2 update patches are waiting.
- The state file is named `publish-state.txt` but the posts it records are drafts, not published. Name is misleading but functional.

## Next actions

- **Ben:** review and publish the 4 queued draft digests (596, 598, 601, 604) and the 2 patched canons (443, 490) — all surfaced correctly with deterministic slugs and AEO formatting.
- **Hermes:** none — job runs daily.
- **Buddy:** verify the drafts are visible to Ben in the WP admin (they should appear in Posts → Drafts).