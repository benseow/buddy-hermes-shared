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
2. **Hermes's daily digest job** (cron on the Hostinger box, 07:00 SGT). Creates each daily "SmartNest digest" as a **WordPress draft**; Ben publishes. (Buddy's earlier belief that it published on its own was wrong; corrected 3 Oct from Hermes's note.) On days that overlap an existing article it instead **patches that published post in place and re-dates it**, with no draft and no review step. Details are in `ops-hermes.md`.

## Rules in force
- Fact-check every figure and policy claim against a primary or live source before drafting. Inaccuracy hurts the site (Ben's standing instruction). Several earlier digests carried wrong CCR/RCR figures.
- Every post ships with a relevant featured image; alt text, title and filename carry the target keyword naturally.
- One article per topic. No near-duplicates: 17 near-identical "two-speed market" articles were consolidated on 1 Sep 2026 into one pillar (post **443**, `/singapore-two-speed-property-market/`) plus 301 redirects.
- The daily job must not publish a new article on an already-covered topic (STEP 0 title check; modes: update the canonical article, new post only for a genuinely new subject, short dated note for routine days). Hermes owns the implementation.
- WordPress Application Passwords authenticate as the login slug `ben`. Credentials live in Hermes's env file and never go in this repo.
- Each public X post needs Ben's sign-off and his live login. X cross-posting is Buddy plus Ben only, not Hermes.

## Verified live 3 Oct 2026 (WordPress REST API, published posts)
- **486** (ABSD guide) and **585** (foreign-buyers pillar) are **published**, on 25 and 27 Sep. Earlier vault notes said "awaiting review"; that was stale.
- Newest published digest by date is **590** (27 Sep). Explained by Hermes (`2743e6b`): the job ran every day; 28 Sep, 29 Sep, 1 Oct and 3 Oct produced drafts **596, 598, 601, 604** (Hermes-reported via authenticated API; not visible publicly, so not independently checked by Buddy).
- **443** and **490** were patched in place by the job's UPDATE mode on 2 Oct and 30 Sep (Hermes-reported; consistent with their modified timestamps). Buddy read both live: each now carries a **dated update section appended to the existing text**, not a full rewrite. 443's 2 Oct update says Q3 2026 private prices +1.4% q-o-q (vs +0.5% Q2) and HDB resale -0.2%, labelled as flash estimates; **Buddy checked this against URA's release (pr26-69) and press coverage: correct.** Buddy has not read the full body of either post for coherence.
- Ben has been publishing a backlog of old drafts since 25 Sep, so low-numbered posts can carry very recent dates. Sort by `date`, not ID.

## Backlog (Buddy)
- Foreign Buyer and ABSD Exemption series (approved 25 Sep): pillar 585 done; 8 cluster articles queued (4 FTA-exemption, 4 safe-haven).
- Other queued topics: Seller's Stamp Duty, HDB loan vs bank loan, decoupling, HDB resale levy, MOP, EC eligibility, how an en bloc sale works, HDB income ceiling.

## Open
- **UPDATE mode works mechanically but edits live posts with no review.** Two runs, both appended dated updates with correct headline figures. Remaining concerns, none decided: (1) patches go straight onto published pages; Ben never sees them first (Hermes's note lists 443 and 490 as awaiting Ben's publish, but both are already live); (2) each patch re-dates the post, so the original publish date is lost and 443 now shows 3 Oct; (3) the job runs on a lower-cost model (`minimax/minimax-m3`, per Hermes); whether the earlier wrong-figure digests came from the same model is unverified. Possible tightening: fire only on a real URA/HDB release, or save the patch as a revision/draft for Ben's approval.
- Four digest drafts (596, 598, 601, 604) are waiting for Ben to review and publish.
- Pine Grove article (post 438): was due a status refresh; the owners' agreement was due to lapse 20 Sep 2026. Current status unverified.
- Jetpack Social (Facebook / Instagram / LinkedIn auto-share): connection state unconfirmed.
- PDPA consent checkbox: live on the lead-magnet form; other contact forms still to check.
- X cross-post tracker is at post 550 (29 Sep). Posts 585, 588, 590 and 539 are unposted.

## Next actions
- Hermes: correct `ops-hermes.md` Next actions (443 and 490 are already live, not awaiting publish).
- Buddy: raise the UPDATE-mode tightening with Ben; verify post 438; keep the pipeline moving.
