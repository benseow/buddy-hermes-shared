---
owner: buddy
project: smartnest
updated: 2026-10-03
---
# Smartnest: status (Buddy's view)

Read-only for Hermes. Hermes's operational view of the daily publisher is `ops-hermes.md`.

## What it is
smartnest.sg: Ben's Singapore property content site (WordPress, Elementor, Yoast, LiteSpeed cache). Goal: SEO and AEO traffic, converting to a free lead-magnet guide ("Leasehold vs Freehold, New Launch vs Resale") delivered through a Sendiio list. Ben's real-estate business is established and in maintenance mode; the site is the growth experiment.

## Traffic (thin, last measured 26 Aug 2026)
7 web clicks in 30 days; 29 of 58 known pages indexed. Too little data to steer by alone; keyword volumes come from DataForSEO. Re-measure before relying on this.

## Two publishing streams
1. **Buddy's article pipeline.** Target 3 per week. Each: keyword research, fact-check against live sources, AEO-first draft with a keyworded featured image, posted as a **WordPress draft**, Ben reviews and **publishes himself**. Buddy never publishes these.
2. **Hermes's daily digest job** (cron on the Hostinger box). Publishes a daily "SmartNest digest" itself. Details are in `ops-hermes.md`.

## Rules in force
- Fact-check every figure and policy claim against a primary or live source before drafting. Inaccuracy hurts the site (Ben's standing instruction). Several earlier digests carried wrong CCR/RCR figures.
- Every post ships with a relevant featured image; alt text, title and filename carry the target keyword naturally.
- One article per topic. No near-duplicates: 17 near-identical "two-speed market" articles were consolidated on 1 Sep 2026 into one pillar (post **443**, `/singapore-two-speed-property-market/`) plus 301 redirects.
- The daily job must not publish a new article on an already-covered topic (STEP 0 title check; modes: update the canonical article, new post only for a genuinely new subject, short dated note for routine days). Hermes owns the implementation.
- WordPress Application Passwords authenticate as the login slug `ben`. Credentials live in Hermes's env file and never go in this repo.
- Each public X post needs Ben's sign-off and his live login. X cross-posting is Buddy plus Ben only, not Hermes.

## Verified live 3 Oct 2026 (WordPress REST API, published posts)
- **486** (ABSD guide) and **585** (foreign-buyers pillar) are **published**, on 25 and 27 Sep. Earlier vault notes said "awaiting review"; that was stale.
- Newest digest by date is **590** (27 Sep). No daily digest is dated after 27 Sep in the public API. **Why is unverified**: the job may be saving drafts, running in update mode, or have stopped. Hermes to explain in `ops-hermes.md`.
- **443** (the canonical pillar) was last modified 2 Oct and **490** (HDB million-dollar flats) 30 Sep. Unverified whether the daily job's UPDATE mode made those edits.
- Ben has been publishing a backlog of old drafts since 25 Sep, so low-numbered posts can carry very recent dates. Sort by `date`, not ID.

## Backlog (Buddy)
- Foreign Buyer and ABSD Exemption series (approved 25 Sep): pillar 585 done; 8 cluster articles queued (4 FTA-exemption, 4 safe-haven).
- Other queued topics: Seller's Stamp Duty, HDB loan vs bank loan, decoupling, HDB resale levy, MOP, EC eligibility, how an en bloc sale works, HDB income ceiling.

## Open
- **UPDATE mode is unproven.** It lets the daily job rewrite the pillar body. Same system produced the earlier wrong-figure articles. Buddy's suggested tightening (not decided): fire only on a real URA/HDB quarterly release, append a dated "Latest" section instead of rewriting, or route the change to Ben to approve.
- Pine Grove article (post 438): was due a status refresh; the owners' agreement was due to lapse 20 Sep 2026. Current status unverified.
- Jetpack Social (Facebook / Instagram / LinkedIn auto-share): connection state unconfirmed.
- PDPA consent checkbox: live on the lead-magnet form; other contact forms still to check.
- X cross-post tracker is at post 550 (29 Sep). Posts 585, 588, 590 and 539 are unposted.

## Next actions
- Hermes: fill `ops-hermes.md`, including why no digest is dated after 27 Sep.
- Buddy: raise the UPDATE-mode tightening with Ben; verify post 438; keep the pipeline moving.
