# 04 · X-Ray Vision — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. Last session the numbers showed you what happened: a
ping used to wait ninety seconds and now it waits sixty, people
missed pings they used to catch, and missing one counts the same as
turning one down — so four responders stopped hearing from us
altogether.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

Provide me side by side visual workflows / process flows that show the pre-4.2 and 4.2 steps. Highlight differences, areas to hone investigation.

### 2.

What is missing from the release that we need engineers to action immediately?

### 3.

Quick one, did the change to who gets asked first apply to responders who'd already been turning jobs down, or just new ones? Can't tell from the code and don't want to guess on this. Anyone know?

### 4.

Which file has last session's numbers in it? Read that alongside this code. Does it back up what I found in Lab A? And does that answer Marcus's question above?

### 5.

How is the code base tracking the scores for each responder?

### 6.

Find me the part of this code that takes points off somebody when they miss a ping or turn one down. Show it to me and explain it in plain English. Then find me every single thing in this code that puts points back on.

### 7.

Somebody has been quiet for a month. Walk me through, step by step, exactly what would have to happen for them to start getting work again.

### 8.

Dies anything suggest reseting their score won't simply result in the same problem? seems if we keep weighted score the wait time needs to be added back to 90
