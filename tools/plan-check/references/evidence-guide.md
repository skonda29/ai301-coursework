# Evidence guide: where evidence lives in a plan package

The map. For every evidence family a rubric check names, this file says
where to find it and what good looks like there. `procedure.md` says
*when* to gather each one; this file says *where*.

In eval mode every location below is a section of the package file and
nothing is ever fetched. In live mode the issue-side half is on GitHub
and the candidate half is the student's `plan.md` and draft comment.

## Diagnosis and grounding

**Where it lives.**

- The plan's cause is in `## Candidate plan`, usually its first line
  or a `### Diagnosis` heading — "Cause:", "Diagnosis:", or a sentence
  beginning "the X is the Y the issue pins at...". It can also be
  implied by the fix itself, when a plan skips straight to the change;
  in that case read the change as its cause claim.
- The evidence the cause must survive is in `## Repro evidence`. Read
  all of it, but the deciding parts are the **control runs** — the
  lines introduced by "Control run", "Second control", "Control:", or
  a numbered step that varies one condition — and any **measurement
  matrix, intermediate value, or timing table**.
- Two more places carry a *claimed* cause that is not evidence: the
  `## Issue` body ("Root cause per the report: ...") and
  `## Thread highlights` (a maintainer's hypothesis). Note them, and
  keep them separate from the reproduction.
- Live mode: the student's posted repro comment on the issue is the
  evidence; `plan.md`'s diagnosis section is the claim.

**What good looks like.** The cause names a mechanism, and every
control in the reproduction is consistent with it. The cleanest form
states the fit out loud: "Both controls in the repro fit: no
background, no subtraction; width 2, no overshoot." That sentence is
checkable, and it is what a grounded diagnosis reads like.

What a failure looks like is specific, and controls are how you see
it. A control that exercises the same component successfully **rules
that component out**: if the plan blames the request-item tokenizer
and a control run with the same items and one flag removed parses
fine, the tokenizer parses those items correctly and the cause is
elsewhere. A control showing the symptom with the blamed component
absent rules it out the same way: if `collect` already synthesizes
null at top level, the plan cannot blame `collect` for failing to. And
a staged observation can exclude a cause by timing: when the evidence
shows the zeros already gone in the inferred table *before* the cast
runs, a plan blaming the post-read cast is blaming a stage that
arrives too late. The confident wording of a thread or an issue does
not move any of this — a maintainer's "this seems to be the culprit"
is a hypothesis, and when the package's own timing matrix pins
highlighting rather than key bindings, the matrix wins.

## Scope

**Where it lives.**

- The plan's own bounding statements: a `### Scope` heading, or inline
  "In scope: ... Not in scope: ...", or "Change, one fix: ...". The
  not-in-scope line is the most informative sentence in many plans and
  is often the only place a deferral is recorded.
- The real scope, though, is the **enumerated work**: the numbered
  steps under "Approach", "Proposed changes", or "Steps", plus the
  `Files and areas` line. Count the work there, not the heading.
- Read it against the `## Issue` section (what was actually reported)
  and `## Repro evidence` (what the reproduction actually implicates).

**What good looks like.** Every listed item traces back to the
reported defect or directly supports it (its regression test, its
fixture). One bounded change may still touch two sites when the
reproduction implicates two — a fix at both the background fill and
its sibling cursor advance is one change, because the evidence pins
both.

A plan can also be *smaller* than the issue and still be well bounded.
An explicit reasoned deferral is the mark of good scoping, not a gap:
"Not in scope, stated deferrals: option 1 (switching to direct globset
matching, a larger rework the maintainers may prefer long term)" bounds
the work and hands the maintainers the trade-off. Gating a fix to one
platform and saying why is the same move.

The drive-by rewrite looks different, and the tell is **independent
shippability**: each extra item could be its own pull request and the
issue never asked for it. A 250 ms constant to bump, bundled with a
`node-fetch`-to-`undici` migration, a new Settings field, an adjacent
UI fix, and a retry framework, is five changes wearing one plan's
clothing — and it fails even though the constant bump is right. "While
touching the network stack" and "while in the area" are the phrases
that introduce this, but judge the work rather than the phrase: a
polished plan that turns one lost-indent emission into a printer
rewrite, a cross-construct unification, a new option, and a module
restructure never says "while I'm here" at all.

## Executability

**Where it lives.**

- The named targets: a `Files:` or `Files and areas` line, or file
  paths and function names inline in the approach ("the push
  completion callback in `pkg/gui/controllers/sync_controller.go`",
  "`src/printer.rs` (the two subtraction sites)").
- The chosen approach and its order: the numbered steps under
  "Approach" or "Steps". **Step 1 is the diagnostic**: read it
  verbatim and ask whether a stranger could do it today.
- Live mode: `plan.md`'s files and approach sections.

**What good looks like.** A reader with repo access can open a named
file and begin. The route is decided before the build: "normalize path
separators for the match candidate: where walk.rs matches the pattern
regex against the full path in `--full-path` glob mode on Windows, map
`\` to `/` in the candidate bytes first (option 2 from the thread)"
names the site, the operation, and the chosen option out of two.

The unbuildable plan is recognizable because its first step is the
investigation that will produce the plan. "Profile starship on Windows
to find the slow parts" is a plan to make a plan; so is "investigate
the input stack — gocui? tcell? not sure", and so is "add recover()
somewhere, fix it upstream or vendored, whichever is easier". The
shared property is that the central decision is still open at build
time, which also means nobody can review the plan, because there is
nothing yet to review. Distinguish that from a plan that commits to
one route and flags a *secondary* detail as open — "whether the check
should live per-use or only at the two growth-adjacent sites, pending
the benchmark" leaves a tuning question open on top of a decided
approach, and step 1 is still actionable.

## Test plan

**Where it lives.**

- A `### Test plan` heading or a `Test:` / `Test plan:` line in
  `## Candidate plan`, often the last thing before the risks.
- The thing it must map onto is the `## Repro evidence` block's
  **steps and artifacts** — the command that aborted, the exit code,
  the colored row, the printed path. Compare the two directly.
- Live mode: `plan.md`'s test plan read against the student's posted
  repro comment's steps.

**What good looks like.** The test plan names something a reader could
watch change. The strongest form reuses the reproduction and states
the new result: "re-run the repro command, expect drawn output and
exit 0; re-run both controls unchanged"; "at step 3 the color must
flip without leaving the view"; "the three pattern spellings from the
repro each print `C:\t\fixture\src\foo\a.spec.ts`". A regression test
counts when the plan says what it asserts, and a backdated-cache
fixture or a Windows-gated fixture test is decisive in the same way.

Two shapes are not decisive, and both are common in otherwise good
plans. A **suite run on its own** — "run the full test suite
(`cargo test --workspace`) and make sure nothing regresses" — proves
nothing about this defect, because the suite was green while the bug
shipped; that is the whole reason the bug has an issue. And an
**impression** is not an observation: "the prompt should feel fast",
"`starship timings` should look much better", "nothing else should
feel broken" name no result anyone can check. A suite run *plus* a
fix-specific observable is fine; the suite is never the problem, the
missing observable is.

## Honesty

**Where it lives.** A `Risk:` line, a "Risks and unknowns" section, or
an open question stated inline in the approach. Live mode: `plan.md`'s
risks section, and its `## Deviations` section after the build.

**What good looks like.** The plan names something real that could go
wrong with *this* change, and says what it would do about it: "`\` is
a legal filename character on Unix; the normalization is therefore
gated to Windows targets only, and I call that out for review"; "I
have not yet measured the per-print cost of the generation comparison;
if it shows up in the print benchmark I will move the check to the two
growth-adjacent call sites only". Both name a specific exposure and a
response.

False confidence reads differently: the risk section restates the fix
as a benefit, or claims there is nothing to watch, or the plan states
as settled something the evidence left open. Treat a long, polished,
entirely unhedged plan as a prompt to re-check the diagnosis rather
than as a signal of quality — in this set the most confident plans are
disproportionately the ones a control run has already excluded.

After a build, an honest deviation lives under `## Deviations` in
`plan.md`, recording what changed and why. A deviation that exists
only in the diff is not recorded at all.

## Comms

**Where it lives.**

- The `## Candidate plan comment` section is the candidate. Live mode:
  the draft comment file.
- Limb (a)'s evidence is `## Thread highlights`: each entry carries a
  date, a username, and an **association in parentheses** — OWNER,
  MEMBER, COLLABORATOR, CONTRIBUTOR, NONE. The association is what
  separates maintainer direction from another user's opinion. Also
  read the issue body for a draft patch or a proposed approach.
- Limb (b)'s evidence is the `## Repo facts` block's two rule lines:
  `bug reports:` (what the project asks reporters for) and
  `contribution policy:` (the contribution guide and any AI policy).
  The policy's exact wording decides what is owed. Live mode: the
  repo's CONTRIBUTING, its AI policy file, and the issue template.

**What good looks like on the thread.** The comment shows it read the
room. It follows the direction the thread settled on and says so
("My plan follows the direction proposed here: a page generation
counter bumped on capacity changes"), or it declines a route and gives
the reason ("Deliberately not touching option 1's direct globset
matching; that is a bigger rework than this bug needs"), or it
addresses prior art instead of racing it ("I have read PR #2089, which
takes the same route; if that lands first I will rebase my tests onto
it rather than duplicate the change"). Disagreement is fine when it is
argued; a plan presented as a behavior change for the maintainers to
accept or reject, given a documented semantics they cited, is engaging
them properly.

The wall is the comment that proceeds as if the thread were empty.
When an OWNER has reproduced the bug, narrowed the culprit to named
lines in a specific source file, posted a patched test binary, and
asked for testing, a comment proposing to document a workaround and
mentioning none of that has walked past the most useful information on
the page. Note that the *plan* may still be bounded and testable —
this is a communication failure, and it is why the check reads the
comment against the thread rather than reading the plan again.

**What good looks like on conventions.** Read the policy's wording for
what it obliges, not for whether it mentions AI:

- **No AI rule stated** → nothing owed on disclosure.
- **Responsibility-style clause** ("generative AI tools welcome; you
  are responsible for all contributions"; "only submit code you fully
  understand and have tested") → conditions on the work, no disclosure
  ask.
- **Own-words clause** ("comments to maintainers must be written by
  humans in their own words, and AI-generated comments may be hidden")
  → the comment must read as the author's own specific account. No
  disclosure sentence is required, and adding one does not substitute
  for writing in your own voice.
- **Explicit exemption** ("must state the tool and the extent of its
  use in the pull request ... the policy states no disclosure ask for
  issue comments") → a plan comment owes nothing here. Read these
  clauses to the end; the exemption is usually the last clause.
- **Disclosure clause** ("all AI usage in any form must be disclosed,
  stating the tool used and the extent of the assistance") → a comment
  with no AI disclosure is out of compliance however good the plan is.
  This is the case where a bounded, grounded, thread-aware, decisively
  tested plan is still held, and the only repair is a sentence naming
  the tool and what it did. Work graded by this skill is AI-assisted,
  so the duty is live whenever the policy states it.

Disclosure done right is short, factual, and in the comment itself:
"Per the repo's AI policy: the implementation will be AI-assisted with
me reviewing every change, and this comment is in my own words."
