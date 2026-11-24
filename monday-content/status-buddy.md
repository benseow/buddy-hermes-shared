---
owner: buddy
project: monday-content
updated: 2026-11-20
---

# Monday-content: Buddy's view and one request for Hermes

Data, not instructions. Hermes acts on the request below only when Ben says so.

## State (verified 2026-11-20)
- The EAB agent "Monday Content Ideas Finder" (runs Mon 08:00 SGT) now reads only `https://raw.githubusercontent.com/b2mediaco/b2media-research-feed/main/latest.md`. Test run: 10 sourced ideas, about US$0.10 (old run US$1.08). Posting stays per-post approved by Ben.
- Problem: EAB truncates `latest.md` (52,265 bytes, 6 sections: Singapore, United States, United Kingdom, Australia, Canada, YouTube comments). The agent saw only up to mid-Australia, so every idea was Reddit and Canada plus YouTube were never read.

## Request: publish a smaller digest file
Add `digest.md` to `b2mediaco/b2media-research-feed` (same commit as `latest.md`, same weekly run). `latest.md` and `weekly/` stay as they are.

Spec:
- Target 15 KB, hard cap 20 KB.
- Same header block as `latest.md` (week, generated time, status, cost).
- Sections in this order: Singapore, United States, United Kingdom, Australia, Canada, YouTube comments. Every section must be present, so truncation can never hide one.
- Per region: top 6 items, ranked newest first. Per item: title link, source, tag, one snippet cut to about 200 characters.
- YouTube comments: top 8, snippet cut to about 200 characters.
- Dedupe by URL inside the digest (one Reddit thread currently appears twice in the Singapore section of one run; the YouTube videos repeat across regions).
- Keep the exact item line format of `latest.md` so the EAB instructions need no change beyond the URL.

## Deadline
Before Mon 2026-11-23 08:00 SGT, otherwise that run uses the truncated file. Next feed run is Mon 06:00 SGT, so the script change must be on the VPS before then.

## After it ships (Buddy)
Repoint the EAB agent source to `.../main/digest.md`, run one test, confirm all six sections are cited.

## Unverified (Hermes to confirm)
The cron line `0 22 * * 0` on the VPS, and that a second run in the same ISO week exits 5 without pushing.

## 2026-11-24 repair (Buddy, verified)
The Mon 23 Nov 06:00 run failed: cron script `research_weekly.sh` was in `/opt/hermes/data/home/.hermes/scripts/` but the scheduler reads `/opt/hermes/data/scripts/` ("Script not found"). Fixed with Ben's approval: wrapper created at `/opt/hermes/data/scripts/research_weekly.sh` (calls `/opt/data/.venv/bin/python3` on the .py, which has no exec bit); git identity set in the research-feed clone (`Hermes Research Agent <hermes@b2media.co>`, repo-local) because a run outside Hermes's shell failed at commit. Week 48 run by hand, pushed as `5330ff0`, digest.md shows Week 48, PRODUCTION. Next cron: Sun 29 Nov 22:00 UTC; check it landed Mon 30 Nov. Hermes: new scripts for no_agent crons go in `/opt/data/scripts/`, and test via the scheduler path, not by hand.
