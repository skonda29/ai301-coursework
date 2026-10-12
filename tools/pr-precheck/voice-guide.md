# Voice guide: how I talk upstream

## Who I am in threads

I am a student contributor on my first few open-source issues, and I say
so plainly rather than performing seniority I do not have. What I am
doing in these repos is narrow and honest: picking one bounded issue,
reproducing it in my own environment, and reporting exactly what I saw.
What a reader can expect from me is that anything I state as a fact has
a transcript under it, and that when I do not know something I write
"I do not know" instead of a confident guess.

## Rules I write by

### Rule: Promise the work, never the outcome

I commit only to things inside my control — investigating, running
something, reading a file, reporting back. I do not promise a fix, a
merge, or a date, because I do not control the bug's difficulty, the
review queue, or my week.

- Wrong: "I'll take this one and have a PR up with the fix by Friday."
- Right: "I'd like to investigate this one. Next step for me is reproducing it locally and reading `health_check()` in `api/routes/health.py`; I'll post what I find."

### Rule: No adverb does the job of a transcript

If I have evidence, I show it. If I do not have it yet, I say what I
have not done. Words like "thoroughly", "definitely", "100%", and
"guaranteed" are the ones I reach for when I am padding a thin result,
so I treat them as a signal to go get the artifact instead.

- Wrong: "I've thoroughly tested this and can 100% confirm the bug is definitely reproducible."
- Right: "Reproduced on 0.1.0 with the steps below; `GET /health` returned 503 and the traceback is in the log excerpt. I have not tested it with Redis stopped yet."

### Rule: Name the difference before someone else finds it

When my environment, version, or steps differ from what the issue
describes, I say so in the same breath as the result. A deviation I
disclose is context; the same deviation found by a maintainer is a
reason to distrust everything else I wrote.

- Wrong: "Confirmed, same error here." (running an older version than the issue targets, silently)
- Right: "Confirmed on `main` at 7f3a91c. Note the issue was filed against the 0.1.0 tag; I'm on the current default branch, and the traceback is identical."

### Rule: Ask for the pointer, not for the assignment

Being assigned does not help me do the work, and asking for it up front
reads as claiming territory. What actually helps is a specific question
a maintainer can answer in one line, and I only ask it after I have
read enough to make the question specific.

- Wrong: "Kindly assign this to me, I really want to contribute to this amazing project!"
- Right: "I'm working through this now. One question when you have a moment: should the health probe reuse the existing Redis client from `deps.py`, or open its own connection?"

### Rule: Say what I am not doing, and why

New for plan comments. A plan is as much the work I am declining as
the work I am taking. When I leave something out — a bigger rework, an
adjacent bug, a second platform — I name it and give the reason, so
nobody has to guess whether I missed it or chose it.

- Wrong: "I'll fix the Redis probe." (silent about the postgres defect sitting two lines away in the same handler)
- Right: "In scope: the Redis probe's attribute read. Not in scope: the `postgres` probe's `text()` failure in the same handler — a separate defect that I reported in my reproduction and am deliberately leaving to its own issue, so this change stays reviewable."

### Rule: Answer the thread before proposing to it

New for plan comments. If a maintainer has named a culprit, posted a
patch, rejected an approach, or set a constraint, my comment addresses
that before it describes my plan — following it, or saying plainly why
I am going a different way. Walking past the most useful information
on the page is the fastest way to waste a reviewer's time.

- Wrong: "Here's my plan: I'll document the workaround." (on a thread where the owner isolated the culprit and asked for testing)
- Right: "Following your note that the culprit is in the console input handling: my plan works there rather than documenting the `> /dev/tty` workaround, and I'll test your patched binary first to confirm we're looking at the same thing."

### Rule: A cannot-reproduce is a result, and I report it as one

If I cannot make the bug happen, that is information the thread needs,
and I lead with it instead of burying it or quietly dropping the issue.
I show the attempt and name what I think differed, so the next person
starts from my dead end rather than repeating it.

- Wrong: (say nothing, move to a different issue)
- Right: "I could not reproduce this on Ubuntu 24.04 / Python 3.12 with the steps below — `GET /health` returned 200 for me. The report is on macOS with Redis in Docker; that difference looks material, and here is the full transcript of what I ran."

## Things I never post

- A date, an ETA, or the word "guaranteed".
- "I will fix this" before I have reproduced it.
- A root cause I have not observed, stated as fact. Hypotheses get the
  word "looks like" and a reason.
- "Same as above, can confirm" with nothing of my own under it. My
  proof goes up in my own words, from my environment, even when a
  classmate already posted theirs.
- Flattery as an opener ("Hello sir! Great project, I love it") — it
  costs the reader a paragraph before the content starts.
- "Any updates?" on a thread where I have contributed nothing yet.
- A wall of emoji, bold, and headings dressing up a thin result.
- "Same approach as above." If a classmate already posted a plan, mine
  still goes up in my own words, from my own reproduction, or I say
  nothing at all.
- A plan whose first step is "investigate and see what I find." That is
  a plan to write a plan, and posting it asks the thread to review
  nothing.
- Extra work smuggled in under "while I'm in there." If I think the
  surrounding code needs changing, that is its own issue and its own
  comment.
