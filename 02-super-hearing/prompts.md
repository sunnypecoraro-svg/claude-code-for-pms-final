# 02 · Super Hearing — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. Last session you built a context file and met the
company.

You still do not know what actually went wrong.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.
Is there any additional context anyone mentioned that is worth noting that changes what we should prioritize next?

### 2.
What are the most concerning issues?

### 3.
Use the rook-database connector to read the support_tickets table. Same treatment as before: group them, tell me how many are in each group, and quote one line from each.

### 4.
Is there any comments in tickets to explain why so many are still open and not addressed? How are these tickets typically prioritized?

### 5.
Do any metrics or targets exist on callout response time before and after release of 4.2?

### 6.
You've now read both folders (interviews and tickets). Where do they disagree? What's loud in the interviews but rare in the tickets, and what's all over the tickets that nobody brought up in the interviews?

### 7.
Does this analysis hold up if you remove tickets from before 4.2 release and only look at tickets from post release?
