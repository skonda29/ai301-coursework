# Evidence guide: where proof lives in a reproduction package

This is the map the rubric's checks read with. For each proof family:
where to look, and what good looks like when you get there.

A note that applies everywhere: in eval mode the bundle is the whole
world, so every location below is a section of the package file and you
never fetch anything. In live mode the issue side is on GitHub and the
candidate side is the student's draft file(s).

## Environment

**Where it lives.**

- Eval bundle: the `## Candidate repro report` section, usually its
  first line or block — an `Environment:` line, a bulleted list, or a
  table of component/version rows. It can also be scattered into the
  steps (an install command, a `--version` invocation in a code
  fence); read those as part of the record.
- The *target* environment to compare against is in two other places:
  the `## Issue` section (the reporter's own version/OS lines, and any
  sentence saying the behavior differs by OS, shell, driver, or build
  profile) and `## Thread highlights` (maintainers narrowing it to a
  channel or build). The `## Repo facts` block's `bug reports:` line
  names which environment facts this project asks reporters for, which
  is a good list of what counts as material here.
- Live mode: the issue body and its template fields for the target;
  the student's draft report for the record.

**What good looks like.** The record names a version of the software
under test that someone could install, and the platform the run
happened on, specifically enough that a reader could stand the run up
again: `yq 4.53.3 (Homebrew), macOS 15.5 (arm64)` is enough, and so is
`fd 10.4.2 (pacman), Arch Linux (x86_64), kernel 6.15, getconf ARG_MAX
= 2097152` when the bug is about argument size. When the issue says the
behavior turns on one axis, that axis appears with a value: a
Windows-only tunnel bug needs the OS *and* the driver; a bug that
panics in debug builds and wraps in release builds needs the build
profile; a locale-dependent bug needs the language order. Hardware
specs are not an environment record. "Latest version, my machine" is
not an environment record. The test is whether a reader could tell
which of the issue's conditions this run satisfied.

## Steps

**Where it lives.**

- Eval bundle: the `## Candidate repro report` section's steps — a
  numbered list, a `Steps:` block, or a shell transcript in a code
  fence where the commands *are* the steps. Read the inputs too: the
  file contents the steps write, the config they cat, the link they
  open.
- Compare against the issue's own `Steps to reproduce` / `Minimal
  reproduction` in the `## Issue` section, and against any correction
  in `## Thread highlights` (a maintainer saying "it also requires
  `--replace`" moves what a complete step list has to contain).
- Live mode: the student's draft report; the issue's reproduction
  section on GitHub.

**What good looks like.** A stranger on a clean machine can go from
nothing to the trigger without asking the author a question. Inputs are
in the package or fetchable: file contents printed inline or created by
a quoted `printf`, a public playground link, "the exact 12 lines from
the issue". Commands appear as commands, with the flags that matter
present — the ones the issue's own repro used, and the ones a
maintainer said are required. A transcript of four commands with no
prose is followable; three prose paragraphs that say "configured it as
usual" are not. The failure modes to watch for are the reproduction
that lives somewhere the reader cannot go (a private monorepo, an
internal `.golangci.yml`, "our pre-commit hook"), the summary that
replaces the action, and the silently dropped flag that sends the
reader down the default path instead of the issue's.

## Behavior shown

**Where it lives.**

- Eval bundle: the code fences and quoted output inside `## Candidate
  repro report` — command output, log excerpts, tracebacks, rendered
  CSS or prompt text, exit statuses, and the report's own `Expected:` /
  `Actual:` lines. A screenshot that is only *described* in prose is
  not an artifact here; there is nothing to read.
- The behavior to match is in the `## Issue` section: the exact error
  text, panic message, exit code, or wrong output the reporter shows,
  plus anything in `## Thread highlights` that sharpens it (a maintainer
  identifying the call stack, another reporter confirming a variant).
- Live mode: the draft report's artifacts; the issue thread for the
  target symptom.

**What good looks like.** The artifact *is* the reported symptom, not
its neighbourhood. Read the two side by side and ask whether the same
thing happened: the issue reports a capacity-overflow crash with exit
101 and the artifact shows `error: Invalid value for '--line-range'`
with exit 1 — that is a graceful argument rejection, a different event
wearing the same word "error". The issue reports `Invalid path
expression` at runtime and the artifact shows `$b is not defined ... 1
compile error` — the expression was changed until it failed a different
way. The issue reports the terminal process dying and the artifact
shows escape-sequence text scrolling with the prompt returning
afterwards — the process lived. Artifacts that show only that the tool
exists (`zellij --version`, a session list, tabs rendering) show the
setup, not the bug.

An honest cannot-reproduce is *also* behavior shown, and it is good
evidence: the report says the symptom did not occur and shows the
output of the attempt, so a reader can see the non-occurrence instead
of being told about it. Treat that as proof, not as a missing artifact.

## Honesty

**Where it lives.** At the seam between two things already read: every
assertion in the `## Candidate claim comment` and the prose of the
`## Candidate repro report` (its summary, its "Conclusion", its
confidence adverbs) on one side, and the artifacts and environment
record on the other. Live mode: the same seam inside the draft(s).

**What good looks like.** Every claim in the prose is covered by
something shown. "Reproduced on 4.53.3 on macOS (report below)" is
covered when the report shows that run. "I verified this race
condition is the cause" is covered by nothing when no transcript
exists. Specific tells that the prose has outrun the evidence:
intensifiers doing the work of artifacts ("thoroughly", "100%
reproducible", "guaranteed", "conclusively demonstrates", "full
end-to-end reproduction"), a root cause asserted without the run that
would show it, a count of machines or repetitions offered instead of
the symptom, and a result generalized to a build, channel, or platform
the run never touched.

The honest report is easy to recognize once you look for it: it names
what it did not do ("I did not test scenario 1; this report is about
scenario 2 only"), it names what differed from the issue's conditions,
and when it failed to reproduce it says so in the first line instead of
the last. A cannot-reproduce that shows its attempt and reasons about
why the attempt might have missed is stronger evidence than a
confident confirmation of the wrong symptom.

## Comms

**Where it lives.**

- The `## Candidate claim comment` section, read against the `## Issue`
  section and `## Thread highlights` (what is specific to this issue,
  and which pointers already exist to anchor a next step).
- The `## Repo facts` block's two rule lines: `bug reports:` (what this
  project asks reporters to provide) and `contribution policy:` (the
  contribution guide, and any AI policy — its exact wording decides
  what is owed).
- Live mode: the repo's CONTRIBUTING, AI policy file, and issue
  template; the course scope file's house rules for Path Review; and
  the student's draft comments.

**What good looks like.** The claim reads as one person who looked at
*this* issue: it names the symptom, the error text, the file, or a
pointer from the thread, and the next step is something the author can
actually do — read a named code path, test a draft patch, report back.
It promises no fix, no merge, and no date. Boilerplate is recognizable
by substitution: if the comment would read identically on any other
issue in any other repo once you swap the issue title out, it is
boilerplate, however warm its tone.

On policy, read the wording rather than the vibe, because these clauses
differ in what they oblige:

- **No AI policy stated** → nothing is owed on disclosure. Silence is
  not a duty.
- **Responsibility-style clause** ("generative AI tools welcome, you
  are responsible for all contributions", "only submit code you fully
  understand") → conditions on the work, still no disclosure ask.
- **Own-words clause** ("comments to maintainers must be written by
  humans in their own words; AI-generated comments may be hidden",
  sometimes with "no disclosure ask for issue comments" said outright)
  → the comment must read as the author's own specific account. A
  disclosure sentence is not required and not a substitute.
- **Disclosure clause** ("all AI usage in any form must be disclosed,
  stating the tool used and the extent of the assistance") → a comment
  that never mentions AI assistance is out of compliance, no matter how
  good its proof is. This is the case where a flawless reproduction is
  still not ready to post, and the only fix is a sentence naming the
  tool and what it did. Work graded by this skill is AI-assisted, so
  the duty is live whenever the policy states it.

Disclosure done right is short and factual, in the comment itself:
"I used an AI assistant to help organize this report; I ran and
verified every step myself and I understand what I am reporting."
