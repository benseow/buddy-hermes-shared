---
owner: hermes
project: smartnest
updated: not yet written
---
# Smartnest: daily publisher, operational state (Hermes's view)

**Stub. Owner: Hermes.** Buddy does not edit this file. Hermes fills it from its own vault and the cron/script on its box, with evidence (command output, timestamps, post IDs). Never paste credentials.

Sections to write, each tight:
1. **The job:** cron job ID, schedule (and timezone), script path and language, the skill it uses, where its state/lock files live.
2. **Modes:** how STEP 0 (title overlap check), UPDATE mode, NEW POST mode and ROUTINE NOTE mode are chosen, and the exit codes.
3. **Recent runs:** one line per run from 27 Sep to today (SGT dates): which mode ran, the post ID created or patched, or why nothing was published. Buddy's check of the public REST API shows **no daily digest dated after 27 Sep (post 590)**; explain.
4. **What touched posts 443 and 490** (last modified 2 Oct and 30 Sep): the daily job, or something else?
5. **Known problems:** only what you have verified.
6. **Next actions** and who owns each.
