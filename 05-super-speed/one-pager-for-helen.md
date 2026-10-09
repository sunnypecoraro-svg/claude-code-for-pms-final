# Dispatch: fixing the quiet-responder problem properly

For Helen Achebe · Dispatch PM · 9 Oct 2026 · Draft

## The problem
Since release 4.2 (12 Aug), callouts that nobody takes have roughly doubled: **5.1% before, 11.2% since** (43 of 837 callouts, then 54 of 482). Small samples, but the step happens on release day.

It is not seasonal, and it is not spread evenly. Four responders went from receiving **28% of all pings to 2%**. The Undertow's last accepted ping was 17 Aug, and their handler has 11 open tickets with no reply. Other responders get the opposite: their phone buzzes, and by the time they respond the callout has gone to someone else.

## Why it happens
4.2 made two changes together: it favoured nearby responders (a long-requested change), and it shortened the time a responder has to answer from 90 to 60 seconds. Missed pings rose from 2% to 18% of pings while turn-downs barely moved.

The ranking treats a missed ping like a turn-down and never forgives it. Once a responder misses a few, they are offered fewer pings, so they have fewer chances to recover. **They can't earn their way back, and nobody tells them why.** Handlers see one responder busy and another silent, with no explanation to give.

## What we'd build
1. **Automatic recovery.** Missed pings stop counting as heavily as turn-downs, and past misses fade over time. A responder who slipped drifts back without having to do anything.
2. **A one-off fresh start** for the four affected responders.
3. **Restore the 90-second answer time**, if engineering agrees. It looks like the main cause, but it isn't proven yet.
4. **Responders are told what is happening:** "You've missed a few recent pings, so we're offering you fewer for now. This eases off on its own."
5. **Handlers can see why a responder is quiet**, and can request a fresh start. Each request is recorded in the existing audit log.

**Not changing:** the nearby-first ranking from 4.2. This is not a revert.

## What we don't know yet
- Whether the four recovered in September (our data ends 6–7 Sep).
- Whether 90 seconds alone is enough. Proposed test: restore 90 seconds, add the recovery, reset the four, then watch them for two weeks.
- How fast penalties should fade. This needs Marcus and Wen Li.

## What I need from you
- **Agreement on direction:** fix recovery and visibility together, rather than quietly changing one number.
- **OK to take it to Marcus, Wen Li and Sofia** this week for a test plan and design.
- **A view on whether handlers can request a fresh start,** or whether recovery should be automatic only.
- **A slot for the Q3 triage** of items squeezed out of 4.2 (e.g. Availability Confidence).
