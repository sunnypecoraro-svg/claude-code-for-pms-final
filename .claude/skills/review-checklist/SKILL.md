---
name: review-checklist
description: Runs the PM's standard four-point review on a brief before it goes any further. Use when the user asks to review, check, sanity-check or "run the checklist on" a brief, one-pager, proposal or spec, or says "review-checklist". Checks for a named owner, a success measure, scope that matches start to end, and a problem stated before the fix.
---

# Review checklist

Run the same four checks, in this order, on whatever brief the user points at. Do not add checks of your own or skip any. If the user gives no file, ask which brief to review.

## Before you start

Read the whole brief, start to end. Do not edit it. This skill reports findings; the user decides what to change.

## The four checks

### 1. Names who owns it
- Pass: a specific person (or clearly named role) is accountable for delivering or deciding. Quote where it says so.
- Fail: no owner, "the team", or only a list of stakeholders/people consulted. Owner is not the same as "asks of" or "reviewers".
- Note if there are several owners with no single accountable one.

### 2. Says how we'll know it worked
- Pass: a measurable signal of success, ideally with a metric, a baseline or target, and a time window. Quote it.
- Fail: no success measure, or only activity ("ship the feature", "get feedback").
- Weak: a metric is named but has no target, baseline or time to check it.

### 3. Scope at the end matches scope at the start
- Compare what the opening (summary, goal, "what we'll build") promises with what the closing (asks, next steps, deliverables, timeline) actually commits to.
- List anything that appears at the end but not the start (scope creep) and anything promised at the start that is missing from the end (dropped).
- Pass only if both lists are empty. Cite the line from each end for every mismatch.

### 4. Explains the problem before it proposes a fix
- Pass: the problem (who is affected, what happens, evidence) appears before the first proposed solution.
- Fail: the fix comes first, or there is no problem statement, or the "problem" is just the solution restated.
- Say where the first proposal appears and where the problem is first explained.

## Output format

Start with one line: the brief's name and an overall verdict, "Ready to go further" only if all four pass, otherwise "Not ready: N of 4 need work".

Then a table:

| Check | Result | Evidence |
|---|---|---|
| Owner named | Pass / Weak / Fail | short quote or "not found" |
| Success measure | Pass / Weak / Fail | short quote or "not found" |
| Scope start = end | Pass / Fail | mismatches, or "consistent" |
| Problem before fix | Pass / Fail | where each appears |

Then, for each check that is not a Pass, one or two sentences on what is missing and the smallest change that would fix it. Do not rewrite the brief unless asked.

Keep the whole report short enough to read in a minute. Report only what the brief says; if something is unclear, say it is unclear rather than guessing.
