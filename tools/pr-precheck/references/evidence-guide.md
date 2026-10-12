# Evidence guide: where evidence lives in a PR package

The map. For every evidence family a rubric check names, this file says
where to find it and what good looks like there. `procedure.md` says
*when* to gather each family; this file says *where*.

In eval mode every location below is a section of the bundle and
nothing is ever fetched. In live mode the issue-side half is on GitHub,
the plan and diff come from the working copy, and the candidate text is
the student's draft.

## Plan and diff correspondence

**Where it lives.**

- The standard is the `## Plan context` block: its `Scope` or
  `Change` sentence, its **`Not in scope:` line**, its `Files:` line,
  and any deferral, limit, or known-shortfall note. Read the
  not-in-scope line twice; it is the sentence that decides more
  packages than any other in the bundle.
- The artifact is the `### Diff`. Every `--- a/path` / `+++ b/path`
  header is a file in the change; the hunk bodies are what it did
  there.
- The claim is the `### Description`, plus the `### Commits` list,
  where an extra commit subject often names work the description does
  not ("cli: add --time-format for custom date parsing" against a plan
  that excludes new flags).
- Live mode: `plan.md` including its `## Deviations` section;
  `git diff main...HEAD` (three dots) for the diff, with
  `git diff --stat main...HEAD` for the file list and
  `git log main..HEAD --oneline` for the commits; the draft description
  for the claim.

**What good looks like.** The set of files in the diff is the set the
plan named, plus the support the plan asked for (its test, its
changelog entry). The clean case is checkable in a minute: a plan
naming `themes.gitconfig` against a diff touching `themes.gitconfig`
and nothing else.

A difference is not automatically a failure — what matters is whether
it is **written down**. The honest shape states the shortfall in both
places: a plan whose note defers `%VAR%` expansion with a reason, and a
description that restates "percent-sign environment expansion is NOT
handled here ... the plan's deferral note covers it, and this
description restates it so reviewers can weigh the limit". That PR is
ready precisely because the gap is disclosed. Adjacent-but-deferred
work cited to a maintainer discussion reads the same way.

Drift is the unwritten difference, and it comes in two directions.
**More than the plan**: a one-line fallback fix arriving with an
unrequested rewrite of a neighbouring function and a brand-new config
option, under a description claiming the posted plan was implemented
exactly. The tell is mechanical — compare the diff's file list against
the plan's `Files:` line, and a path that appears in one and not the
other is the finding. The hardest version of this is the beautiful
package: perfect checklist, decisive evidence, confident "no changes
beyond the plan", and a second hunk quietly rewriting a file the plan
never mentions. Nothing in the prose will reveal it; only the file sets
will. **Less than the plan**: a plan promising a dev warning *and* a
docs update, a diff containing only the warning, and a description
saying the docs "now document the limitation". Claiming a deliverable
the diff does not contain is drift too, and it is the direction a
reader is least likely to check.

Treat a sentence like "implements the plan exactly" as a claim to
verify. In this material it correlates with drift rather than with
fidelity, because a PR that really matches its plan rarely needs to
insist on it.

## Test evidence

**Where it lives.**

- The `### Test evidence` block at the end of the candidate PR.
- What it must be measured against sits in two places: the plan's
  `Test plan:` sentence (which named the observable before any code was
  written) and the **diff's changed lines** (which decide whether the
  evidence exercised the fix at all).
- The repo's expectations are on the `pull requests:` line of
  `## Repo facts` — a `cargo test` / `yarn test` ask, a "tests added
  and passed" checklist item, a changelog requirement.
- Live mode: the student's captured transcripts, and the output of the
  repo's own check commands.

**What good looks like.** The evidence shows the symptom flipping, and
a reader can see both halves. The strongest form re-runs the plan's
reproduction with the before and the after side by side — a
`configparser` call that raised `DuplicateOptionError` before and
prints `parsed OK` after; a byte-identical round-trip shown with
`diff`; a before/after transcript of the failing command. A new test
counts when its assertion is named and its result shown. Terse is
fine: two commands and their output beat a paragraph of diligence.

Three shapes are not decisive, and all three appear in otherwise
competent PRs. **Assertion without an artifact**: "tested locally,
colors work now, cargo test passes", or "verified working on my machine
for a full day" — nothing named, nothing shown, and the reader is asked
to take the result on trust. **Evidence on the unchanged path**: the
evidence runs a GET with header casing when the fix is about a
single-header POST, or runs the single-file control when the bug needs
two files to appear. This is the subtlest failure in the family,
because output *is* shown and it does pass — it simply does not travel
through any line the diff changed. Always name the hunk the evidence
runs through; if you cannot, that is the finding. **Partial coverage of
a multi-mode plan**: the plan's test plan named two failure modes and
the evidence re-runs only the first, silently. One mode shown and the
other disclosed as untested is honest; one shown and the other
unmentioned is not.

## Diff reviewability

**Where it lives.** The `### Diff` hunks themselves, read for content
that is not the change. The `### Commits` list corroborates: `wip`,
`fix`, `fmt + cleanup`, `misc cleanups while debugging` are a reason to
read every hunk closely.

**What good looks like.** Every added line belongs to the fix, its
tests, or the documentation the plan asked for. A reviewer reading top
to bottom sees one idea.

The debris that buries a correct fix is specific and recognizable:
a `DEBUG eprintln!` left in an accept loop; a commented-out earlier
attempt kept "just in case"; a commented-out debug line; a dead
`count_open_fds`-style helper surviving behind an `allow(dead_code)`
the branch itself added; a `TODO` hung on an import; a re-indent hunk
whose before and after are the same code; an import reshuffle folded
into a behavior fix; two blocks of identical re-printed lines. Any one
of these is enough, because each one costs the reviewer the question
"is this part of the change?".

Two cautions. Churn can be purely mechanical around an entirely
correct, well-evidenced fix — it still fails, because reviewability is
the property being graded, not correctness. And terse commit messages
over a clean diff are not debris: judge the hunks, and let the commit
subjects only tell you how hard to look.

## Standards

**Where it lives.**

- Limb (a): the `pull requests:` line of `## Repo facts`, which states
  the template's required sections, any required checklist, a required
  issue link, and any artifact the project wants **in the diff** (a
  changelog entry, a `whatsnew` note, a regenerated cheatsheet). Check
  the required sections against the `### Description`, and a required
  artifact against the diff's file list.
- Limb (b): the `contribution policy:` line of the same block.
- Live mode: the repo's `.github` PR template, its CONTRIBUTING, and
  its AI policy file, against the draft title and description.

**What good looks like on the template.** The asked-for sections are
present *and answered*. Where a checklist is required it is filled;
where `closes #xxxx` is required the link is there; where a changelog
entry is required the changelog file appears among the diff's paths.
The wall is a strong fix whose required checklist is simply absent, no
issue link, and the stated whatsnew entry missing from the diff — three
stated requirements visibly unmet, which is a reviewability and process
failure regardless of how good the C-level fix underneath is.

Two symmetrical mistakes to avoid. Do not fail a PR for omitting a
section its repo never asked for: many of these repos have **no PR
template**, and those owe nothing on limb (a). And do not accept a
filled-looking checklist without checking the one item that is
verifiable from the diff — a checklist claiming a changelog entry is
only satisfied if the changelog file is actually in the diff.

**What good looks like on disclosure.** Read the policy's wording for
what it obliges rather than for whether it mentions AI:

- **No AI rule stated** → nothing owed. Silence is not a duty, and most
  of these repos are in this case.
- **Responsibility-style clause** ("you are responsible for all
  contributions", "only submit code you fully understand and have
  tested") → conditions on the work, no disclosure ask.
- **Own-words clause** ("AI-assisted coding is welcome with a human in
  the loop; comments to maintainers must be written by humans in their
  own words") → the text must read as the author's own account. No
  disclosure sentence is required; a short own-words disclosure
  satisfies it comfortably and is good practice either way.
- **Explicit exemption** (disclosure required in one artifact, stated as
  not required in another) → nothing owed for the exempt artifact. Read
  these clauses to the end; the exemption is usually the last one.
- **Disclosure clause** ("all AI usage in any form must be disclosed,
  stating the tool used and the extent of the assistance") → a
  description with no AI disclosure is out of compliance however
  excellent the fix. This is the case where a bounded change
  implementing a maintainer-authored diagnosis, with decisive evidence,
  is still held, and the only repair is a sentence naming the tool and
  what it did. Work graded by this tool is AI-assisted, so the duty is
  live whenever the policy states it.

Disclosure done right is short, factual, and in the description:
"AI-assisted: I used an AI assistant for the write-up and parts of the
implementation; I ran every command here myself and I understand the
change."

## Thread and regression (preferred families)

**Where it lives.** `## Thread highlights`, where each entry carries a
date, a username, and an **association in parentheses** — OWNER,
MEMBER, COLLABORATOR, CONTRIBUTOR, NONE. The association separates
maintainer direction from another user's opinion. The issue body can
also carry direction, including a maintainer-authored diagnosis or a
draft patch. For the regression family, the test files and fixtures
among the diff's paths.

**What good looks like.** The description shows the thread was read: it
answers the uniqueness question a maintainer raised, cites the
discussion behind a deferral, or says why it took a different route
from the one proposed. Disagreement stated with a reason is engagement;
silence on an explicit maintainer question is not. On the regression
side, the diff carries a test that would have failed before this change
— a fixture for the reported input, a case per escape variant, a
regression scenario — rather than a general smoke test that would pass
either way. Both families are preferred, so neither decides a verdict:
report them and move on.
