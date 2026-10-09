# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch** (inherited from Priya Raghunathan, who left 21 Aug 2026). Sources (read 6 Oct 2026): `00-rook/company/notes/handoff-from-priya.docx`, `00-rook/code/dispatch-routing/`, the Rook wiki (Company, Research, Product briefs) and the rook-database (pings, tickets; data ends 6–7 Sep). `00-rook/feedback/` is empty.

### Company and products
- Rook Industries (founded 2014, 241 staff, HQ Site Aleph; offices in Berlin, Singapore, Cornwall) sells coordination and provisioning software to independent masked responders and their handlers/quartermasters. Rook employs no responders. Subscription, priced per active responder.
- **Rook Dispatch** (mine): ranks available responders for an incident, pings the top one's phone, moves on if turned down or missed. Handlers use the web console; responders use the phone app. Routing config ships in the release, not as a handler setting.
- **Rook Supply**: gear requisitions (handler → quartermaster approval), fulfilment, maintenance schedules, field failure reports. Reads Dispatch's Responder Availability Record to book maintenance into low-callout windows. Any change to how Dispatch writes that record silently changes Supply scheduling.
- Release train is described as monthly, but 4.0/4.1/4.2 shipped about two months apart and nothing is recorded since 12 Aug. 4.x numbering. Support has three tiers; tickets filed mid-callout bypass the queue.
- **Hard rule:** responder cover identities are never stored or mappable to legal identities (Security Policy 4.1). Never design for, or try to infer, who anyone is.

### People (Dispatch)
- **Helen Achebe**, Director of Product (Site Aleph): my director; owns roadmap and commitments.
- **Marcus Oyelaran**, Eng Manager (Site Aleph): runs Dispatch engineering; can usually pull numbers; first call when unsure.
- **Wen Li**, Staff Engineer (Berlin): built the ping-ranking logic. No written doc exists, so talk to Wen Li. Was away 14–24 Aug, right after 4.2 shipped.
- **Nadia Hoffmann**, Support Lead (Berlin): owns tickets; hears handler complaints first. Worth a standing 15 minutes.
- **Sofia Marino**, Product Designer (Site Aleph): console and phone app; ran the September interviews.
- **Ravi Menon**, Data Analyst (Singapore): weekly acceptance-rate reporting.

### Vocabulary
- **Callout**: request for a responder to attend an incident. **Ping**: a callout offered to one responder. Outcomes: **taken**, **turned down**, or **missed** (ping wait ran out; tracked separately, both pass it on).
- **Ping wait**: how long a ping stays live. Same for everyone, set in the release.
- **Acceptance rate**: pings taken ÷ pings sent. Headline metric, reported weekly in aggregate. **Time-to-accept**: median seconds, ping to taken. **Coverage gap**: no available responder had the needed tags (nobody *could* go, which differs from nobody *would*).
- **Routing priority**: score ranking responders: proximity (travel-time estimate), availability, capability match, recent acceptance history. Turning down or missing pings lowers future rank.
- **Capability tags**: flight, structural-entry, hazmat-tolerant, cold-weather, aquatic, crowd-management, de-escalation.
- **Responder Availability Record**: shared availability record, written by Dispatch, read by Supply.
- **Mutual aid**: responders covering for each other across areas. Not supported; Q4 exploration.
- **Handler / quartermaster / responder / cover identity**: see Glossary in wiki.

### Where things stand (as of 6 Oct 2026)
- Releases: 4.0 (7 Apr: new nav, profile redesign, routing-override audit log); 4.1 (16 Jun: travel-time proximity, bulk callout, push reliability); **4.2 (12 Aug)**: proximity weighted up vs. recent acceptance, ping wait cut 90s → 60s, console filters persist, three fixes.
- **Open problem:** since 4.2, fewer pings are taken and handlers complain. Priya's read was "mostly seasonal, back in September". **The data doesn't support that:** taken-rate was flat at 75–78% through 8 Aug, then fell to 54% the week of 10 Aug (4.2). Ping volume stayed flat (~170/wk). The drop is concentrated in four responders (Vesper, Meteor Mite, The Undertow, Farlight): pings fell from 75–86 to 11–17 and 53–64% were missed; the other twelve slipped only a few points. The weekly aggregate (72.7% by 31 Aug) hides this. September can't be checked (data ends 6–7 Sep).
  - **Suspected mechanism (from code, untested):** the 90s → 60s ping wait causes more misses; `history.py` treats a miss like a turn-down (penalty 0.12 vs credit 0.08) with no decay (2019 TODO), so a responder who slips gets fewer pings and can't recover. Two changes shipped together (weights and timeout), so attribute carefully. Priya advises against framing this as reverting 4.2; the proximity change was a long-requested ask.
- **Q3 roadmap** (last reviewed 30 Jun, all owned by Priya, so stale and needs a new owner):
  - Committed for 4.2: change to who gets pinged (shipped); ping timeout tuning (the 90 → 60 cut shipped; confirm that's all it meant); **Availability Confidence (not in 4.2 release notes, so apparently deferred).**
  - Committed for 4.3: requisition approval chains (Supply).
  - Exploring for Q4: handler phone app; shared cover between responders.
- **To do early:** (1) with Helen, decide which squeezed-out 4.2 items are still Q3 commitments (hasn't happened); (2) write down how routing decides who gets pinged (nothing exists); (3) don't let console filter-persistence tickets absorb the first month; (4) review the less-examined parts of the product, since Priya made calls quickly as sole PM for 14 months.
- Open question: is Availability Confidence the deferred item, and does it affect the Availability Record Supply depends on? (No brief, definition, or code mentions it.)

### Known contradictions and gaps
- Docs say ranking "only sets the order", yet low-ranked responders are almost never reached. Glossary lists availability as a routing input; code uses it only as a filter. Glossary says missed and turned-down are "separate"; scoring treats them the same.
- One thing, three names: *ping wait* (Glossary), *offer timeout* (code), *ping timeout* (handover, roadmap).
- Priya says Marcus pulls numbers; the directory says Ravi owns them.
- Requisition Approval Chains (committed 4.3) adds a second approval step, but Halloran's complaint is that the one queue is too slow and the priority field is ignored. The brief's scope has also ballooned.
- Handler Phone App brief (8 Sep) cites Aunt Dot, whose real complaint was losing callouts. Roadmap still lists Priya as owner.
- Filter persistence isn't just noise: it resets unprompted (Ambrose), ~15 tickets since 12 Aug mention filters.
- Session 1 outcome (6 Oct): the seasonal explanation is refuted by data through 6 Sep; the real question is whether the four starved responders recovered in September. Next-step order agreed: (1) ask Ravi and Marcus for per-responder weekly ping outcomes since 10 Aug, recent-acceptance scores and any post-4.2 config changes; (2) test the miss-penalty/no-decay mechanism with Wen Li and get the routing logic written down; (3) brief Marcus and Helen, and ask Marcus about a stopgap (restore 90s wait or add score decay) before any change to `config.py`/`history.py`; (4) roadmap triage with Helen; (5) Nadia and Sofia, including follow-ups with the Undertow and Farlight handlers.
- The database is a snapshot ending 6–7 Sep, and no other connected source has later data. Hold off on a 4.2 revert, filter tickets, and the phone app.
- Missing: no Supply PM in the directory; "Shared cover between responders" has no brief and may touch the cover-identity rule; interviews covered 4 of 15 handlers and never asked why callouts vanish; code files are stubs. Possible knock-on (speculative): Supply may read artificially quiet responders as free for servicing.
- Session 2 (6 Oct, interviews + 147 tickets): missed pings rose from 2.3% to 18.0% of pings after 4.2 while turn-downs barely moved (taken 76.6% → 64.0%), the clearest sign so far for the 60s wait. No response-time target exists anywhere, and `pings` has no answer timestamp, so true time-to-accept needs Ravi.
- Tickets: 45 "quiet" (32) and "vanished before answer" (13) tickets, all filed after 12 Aug and all still open; before 10 Aug every ticket was closed (about 6 a week). Quiet tickets come from only 4 filers (Farlight, The Undertow, Corporal Ashgrove, Halfmoon), so volume overstates breadth; vanished has 9 filers. Overlap with the data's four starved responders is only Farlight and The Undertow.
- Tickets have no comments, owner, tier or priority field and the wiki has nothing on triage; ask Nadia how tickets are prioritised and whether these reached engineering. Nadia's 18 Aug comment on the 4.2 page flagged tickets at 3x normal, and Marcus's 14 Aug question to Wen Li (does the weighting apply to responders who turn jobs down?) was never answered.
- Interviews vs tickets: interviews stress vanishing, tickets stress quiet; Halloran's "maintenance scheduling is smarter" conflicts with 4 post-4.2 maintenance tickets; duplicate-ping and push tickets all pre-date 4.2 and look fixed. Ambrose's "one boot on" story is a possible safety angle on the 60s wait for Helen and Marcus.
- Session 3 (7 Oct, ELT metric): proposed headline = share of callouts where no responder took any ping: 5.1% (43 of 837) before 10 Aug vs 11.2% (54 of 482) from 10 Aug, peak 14.0% week of 17 Aug, 5.5% week of 31 Aug (one week, not a recovery). Missed pings run alongside as the likely-cause line (about 2% to 13–22%, turn-downs flat). The step change is on 12 Aug itself (taken 68–83% a day to 40–53%; missed 0–1 a day to 4–12). The aggregate "recovery" is partly mix: the four starved responders went from 28% of pings to 2%, and the other twelve went 58% to 74% (before: 77–79%).
- Unanswered callouts cluster by place, not person: Southport, Riverside and Kingsbridge hold 22 of 54 post-4.2 (about 22% unanswered vs 5% before); areas with a starved responder were not hit harder (7.6% vs 13.2%); no clear time-of-day or weekday pattern. Callouts fell from about 139 to 120 a week after 4.2, nights roughly halved (13.8 to 6.5 a week); cause unknown, so ask Marcus whether 4.2 changed how callouts are created or logged. Small samples throughout.
- Tickets are files in `00-rook/feedback/tickets/` (147): "vanished" tickets start 12 Aug (release day), "quiet" tickets start 17 Aug and peak the week of 24 Aug; Vesper's and Meteor Mite's handlers never filed one; Ashgrove and Halfmoon are only about 25% quieter. The Undertow (handler Desmond Okafor) is the worked example: last ping taken 17 Aug, 14 pings since 12 Aug with 9 missed, 11 open tickets and no reply.
- Code read 7 Oct (not stubs): `history.py` gives +0.08 for a take and -0.12 for a miss or turn-down, with no decay (the 2019 TODO); five misses reach the floor and about seven takes get back to neutral; scores sit in an in-memory dict, so a restart may reset everyone (unverified). 4.2 changed `config.py`: wait 90 to 60s, proximity weight 0.45 to 0.60, acceptance weight 0.40 to 0.25. Possible stopgap package for Marcus and Wen Li: 90s wait, decay, one-off reset of the four scores.
- Still open: September data (does anyone get pinged again); true time-to-accept (ping gaps could proxy for misses and turn-downs, not run); why Southport, Riverside and Kingsbridge struggle; how Nadia triages the open tickets.
- Session 4 (7 Oct, routing code walk-through): the flow is availability filter, score (nearness, yes-rate, skills), sort, offer one at a time, record outcome. Files: `availability.py`, `routing.py`, `offer.py`, `history.py`, `config.py`. Changelog for 4.2 lists only the weights, the 90 to 60s wait and filter persistence (not in this service); Availability Confidence is absent. The folder starts after the callout exists, and the phone and availability functions are empty placeholders, so ask Wen Li about those.
- `offer.py` treats anything other than "taken" the same, so a miss and a turn-down get the same -0.12. Only `record_accepted` adds points (+0.08); nothing fades, resets or lets a handler adjust a score. Break-even is a 60% take rate: above it a score drifts up, below it sinks to the floor. Before 4.2 everyone took 69-84%; the four starved responders now take 29-35%, the other twelve 64-72% (only 4-12 points above break-even).
- Marcus's 14 Aug question (does the new weighting hit people who already turn jobs down?): the code applies to everyone equally, and the weekly data (`00-rook/data/callout-history.csv`, 10 weeks from 29 Jun, pings sent and taken only) shows the four were not the worst before 4.2 (Vesper 82%, Meteor Mite 69%). So the data does not support "targeted"; it fits "missed under 60s, then could not recover". Still unanswered by Wen Li, and release-day scores are unknown (in-memory, restart may reset them).
- The 4.2 reweighting alone makes recovery by being close easier (a score of 0 now costs about 9 minutes of travel vs about 20 before); the wait and the penalty are the blockers. A score reset alone would likely slide back. Cleaner test to propose to Marcus and Wen Li: restore 90s, keep the new weights, reset the four scores, add decay, then watch the four for two weeks. Not proven that 90s alone is enough.
- No "Lab A" exists in this folder; last session's write-up lives in this file and the callout-level and missed-ping figures came from the database, not a file.
- Session 5 (9 Oct, brief + prototype for Helen): Helen's request is `05-super-speed/director-request.txt`. Engineering says the code fix could ship the same afternoon; Helen wants a one-pager on what we'd build instead, seen from the handler's and the quiet responder's side, not a quiet number change. She wants something clickable.
- Key insight: a starved responder can't earn their way back by taking pings, because only takes add points and they get few pings. So recovery must be automatic (decay, misses scored apart from turn-downs, one-off reset of the four), and the phone message explains and asks nothing of them. Proposal = engine fixes plus visibility, together. The 4.2 weights stay (not a revert). Restoring 90s is pending Marcus and Wen Li and not proven. The decay rate (the "X days") is undecided. Probation pings are unexplored.
- Asks of Helen in the brief: agree the direction; OK to take it to Marcus, Wen Li and Sofia; whether handlers may request a fresh start (via the routing-override audit log) or recovery should be automatic only; a slot for the Q3 triage (Availability Confidence).
- Files in `05-super-speed/`: `brief.md` (executive one-pager, same as `one-pager-for-helen.docx` and `.md`), `prototype.html` (dark superhero-styled handler console: Coverage, Live callouts with a 60s countdown and phone view, audit log; all data illustrative). `brief-for-helen.html` is an older clickable version with some older wording.
- Session 6 (9 Oct, sidekicks): saved a `review-checklist` skill at `.claude/skills/review-checklist/SKILL.md`. It runs four checks on any brief: named owner, success measure, scope start matches end, problem before fix. It reports only and never edits. Point it at a brief by path or URL.
- Ran it on `05-super-speed/brief.md`: it failed owner, was weak on success measure and failed scope (Q3 triage and the 90s restore were inconsistent between start and asks). Fixed in the brief: owner line, a "How we'll know it worked" section (unanswered callouts toward 5.1%, the four above 60% taken, within two weeks) and a top scope line. `one-pager-for-helen.md` and `.docx` are copies and are now out of date.
- A peer's `brief-v2.md` (pourriel2 repo, read only) is stronger: five measures with baselines, a measurement plan, a decision rule (ship 90s alone first, scoring fix in 4.3, scope decision 16 Oct). Its owner is still a placeholder. Worth borrowing its measurement plan.
- Scheduled tasks write outside this folder, which the scope block forbids, so a Monday review runs only as a session-only repeat (job 4360ecdb, Mondays about 9:07, dies when the session closes and expires after 7 days). A durable schedule needs a session outside this folder.
