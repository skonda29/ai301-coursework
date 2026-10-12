---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this
ready to submit?**

A PR package is a candidate pull request — its title, description,
commit list, diff, and test evidence — read against the plan it claims
to implement and the issue that plan belongs to. Those three layers are
the whole job: the issue says what is wrong, the plan says what will be
done about it, and the PR is the claim that the plan was carried out.
Your work is to check that claim against the artifacts.

Do not answer any other question. You are not reviewing the code's
design, not deciding whether the maintainers will like it, not
predicting whether CI will pass, and not judging whether the fix is the
one you would have chosen. A PR can be ready to submit and still be
rejected upstream; those are different questions and only the first is
yours. Grade one package per run, and never from gut feel: execute the
components in this directory.

## Inputs and modes

Determine the mode from the inputs before doing anything else, and say
which mode you are in.

**Live mode** — the student's own submission, checked before it goes
out. Gather these inputs:

1. **The plan**: `plan.md` in the working copy, including its
   `## Deviations` section. Read the deviation notes as part of the
   plan, not as an afterthought: a change the plan records as a
   deviation is planned work, and the same change unrecorded is drift.
2. **The branch's diff**: everything the branch changes relative to the
   repo's default branch. Run `git diff main...HEAD` (three dots) from
   the working copy to produce it. Use `git diff --stat main...HEAD`
   first for the file list, and read the full diff after. Do not
   substitute the latest commit's diff (`git show`) or a two-dot diff;
   both answer a different question and will miss earlier commits on
   the branch.
3. **The draft PR title and description**: the file or text the student
   names. This is outgoing text and the voice seam gates it.
4. **The test evidence**: the before/after transcripts the student
   captured, plus the output of the repo's own checks. Take it from
   where the student points; if they point at nothing, the evidence is
   absent and the relevant check grades that absence.
5. **The issue**, by URL: its body, its thread, the repo's PR template,
   and the repo's stated contribution and AI policies. Gather these
   from the live repo via `gh`, the GitHub API, or the web, at the
   locations `references/evidence-guide.md` names.

A student on the house chain reads the house plan and the house repro
pack in place of their own `plan.md` and evidence; the checks are
unchanged and grade the same things against those documents.

Read the drafts the way a reviewer opening the pull request will read
them: the package is the diff, the title, the description, and the
evidence the student actually presents. Other files in the working
directory are not part of it, and neither is anything the student
explains to you in conversation but has not written down. If it is not
in the package, a reviewer will not see it, and neither do you.

**Eval mode** — a package bundle is the whole world. Every fact comes
from the bundle text: the issue context, the repo-facts block, the
plan-context block, and the candidate PR's title, description, commits,
diff, and test evidence. Fetch nothing. Read nothing else. Do not
consult the real repository the `source:` line names, even if you
recognize it: the bundle is a frozen snapshot and the live repo has
moved on, so when the two disagree the bundle wins. Eval mode always
grades a complete package: every check in the rubric, the full verdict
rule, no claim-only or partial branch.

## The scope seam (live mode only)

In live mode, read `scope.md` first, before the plan, the diff, or
anything else.

It names the repository the student's PR must live in and the house
rules of that environment. Apply those rules as written when they
change how evidence is read; a house rule is not advice, it is a local
amendment to how you grade.

Then check the PR's target. If the pull request is against any
repository other than the scoped one, **refuse to grade it**: say which
repo the scope names, which one the package targets, and stop. Do not
grade it anyway with a caveat.

If the scope's `Repo:` line still carries an unfilled bracketed
placeholder, **stop without grading** and tell the student to get their
cohort's scope file from the instructor. Never guess a scope, never
infer it from the issue URL, and never proceed on the assumption that
any repo is probably fine.

In eval mode, ignore `scope.md` entirely. It is not part of a bundle's
world.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` as well. It holds the student's own
rules for how they write upstream, with wrong/right pairs.

It gates exactly two pieces of outgoing text: the **PR title** and the
**PR description**. Hold both against every rule in the guide. When a
draft breaks a rule, report it in the readable summary: quote the rule,
quote the line that breaks it, and say what would satisfy it. Check the
"things I never post" list the same way.

The voice guide never changes the verdict on its own. Report a broken
rule as a note, not as a check result, unless `rubric.md` contains a
check whose stated evidence is the voice guide — in which case that
check, and only that check, carries it into the verdict. The reason for
the separation is that voice is the student's own standard: your job is
to hold their draft against it out loud, not to overrule the rubric
with it.

In eval mode, ignore `voice-guide.md` entirely. Voice is personal and
carries no gold labels; the communication-quality checks that apply to
everyone live in `rubric.md`.

## Component reads

Three components do the work, and they divide as follows.

**`rubric.md` is the brain.** It defines a table of checks — each with
a name, the evidence to gather, the pass condition, and a weight — and
below the table a verdict rule. `required` checks gate the verdict;
`preferred` checks never change it in either direction. Execute every
check in the table.

**`references/evidence-guide.md` is the map.** For each evidence family
a check names, it says where that evidence lives in a PR package (and,
in live mode, where on GitHub or in the working copy) and what good
looks like when you get there. When a check names evidence, go to the
location this guide specifies rather than searching wherever seems
likely.

**`procedure.md` is the hands.** Execute it as written. It decides the
read order, how each evidence family is gathered, how a check runs
against gathered evidence, and how the per-check grades assemble into
the verdict. Follow it exactly, in the order it gives, the way an
executor follows a rubric — without improvising around gaps and without
reordering steps because another order seems more natural.

When the procedure is silent on a step you needed, do not invent one
silently. Take the most literal available reading of the rubric, and
**report the gap in your summary**, naming what the procedure did not
tell you. A procedure gap is feedback the student needs in order to fix
the tool; papering over it hides the defect and makes the next run
unrepeatable.

**The refusal rule.** If `rubric.md` contains no checks, or
`procedure.md` contains no steps, **stop and say so. Do not grade.** Do
not substitute your own checks, do not fall back on general judgment
about pull requests, and do not produce a verdict. The templates ship
empty on purpose; a tool that invents its criteria at runtime produces
output that looks like judgment and is noise, which is worse than no
output at all. Instruction comments inside a template file are guides,
not content: a file carrying only comments is still empty.

## Verdict and output

The verdict space is binary. `accept` means the package is ready to
submit. `reject` means hold it and fix what the failing checks name.
There is no third verdict, no "accept with reservations", and no score.
A reservation belongs in the evidence line of the check that raised it,
never in the verdict.

You may print a short readable summary first: one line per check with
its grade and the fact that decided it, any voice-guide notes in live
mode, any procedure gap you hit, and on a reject the deciding check.

End your reply with the fenced JSON block below. It must be present,
valid JSON, and the last thing in your output, with nothing after it —
not a closing remark, not a suggestion, not a blank-line comment. The
harness parses the last fenced JSON block in your output, so anything
after it either breaks the run or gets mistaken for the result. One
entry per check in the rubric, in the order the procedure executed
them. The schema is fixed and may not be altered, extended, or
reordered:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

These rules bind every run.

**Evidence first.** Never grade a check without naming the fact or
quote that decided it. Every `evidence` line must point at something in
the package: a diff hunk, a file path, a sentence from the description,
a line of test output, a clause of the repo's policy. "Looks fine",
"seems complete", and "no issues found" are not evidence. If you cannot
name the deciding fact, you have not finished grading the check.

**Grade the thing, not the polish.** Read the artifact itself against
the plan, the issue, and the repo's stated standards — never the
formatting. A terse three-line description over a clean diff with a
before/after transcript can be ready. A long, confident, beautifully
sectioned description with a filled checklist can be hiding a diff that
touches a file the plan never named. Presentation is not evidence, and
a polished package deserves more scrutiny of its diff, not less.

**The rubric decides, not the run.** If a check passes by its stated
condition but feels wrong, it passes. If it fails by its stated
condition on a package you like, it fails. Note the tension in your
summary if you want to; the fix belongs in the rubric, not in this run.
Do not add a requirement mid-run to catch something the rubric missed,
and do not soften a stated fail condition because the rest of the
package is strong.

**The procedure decides how, not the run.** Gather and execute in the
order `procedure.md` gives. Where it is silent, report the gap rather
than filling it quietly.

**Unclear defaults to fail.** Use `unclear` when the evidence a check
needs is genuinely absent from the package — not when it was merely
hard to find, and not as a way to avoid a hard call. Then treat it as
the rubric's verdict rule directs, and where that rule is silent, treat
it as a fail: a PR whose readiness you cannot verify from the package
is a PR that is not ready to submit. Keep the `unclear` grade in the
JSON even when it counts as a fail, so the student can see that the
cause was absence rather than a failed condition.
