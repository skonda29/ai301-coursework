# Procedure: how this skill grades a plan package

## Read order

Read the whole package before grading anything. The order matters: the
reproduction is the ground truth every later check measures the plan
against, so it is read **before** the plan, and the plan is read
before its comment. Reading the plan first invites you to adopt its
framing and then look for evidence that fits it, which is exactly the
failure the `diagnosis-grounded` check exists to catch.

1. **Read the `## Issue` section.** Note, in one line each: the
   reported symptom, the error text or observable result the issue
   names, and any cause the reporter asserts. Mark the asserted cause
   as *claimed, not established* — the issue is a third party, not
   evidence.
2. **Read the `## Repo facts` block.** Note two things verbatim: what
   the `bug reports:` template asks for, and the exact wording of the
   `contribution policy:` line. Classify the policy now, before any
   plan text can influence you, into one of: **no AI rule** /
   **responsibility-only** / **own-words** / **disclosure-required** /
   **disclosure-required-but-exempts-issue-comments**. Write the
   classification down; limb (b) of `thread-and-conventions` reads it
   later.
3. **Read the `## Thread highlights` section.** For each highlight,
   note the speaker's association (OWNER / MEMBER / COLLABORATOR /
   CONTRIBUTOR / NONE) and whether they gave **direction**: naming a
   culprit site, proposing an approach, rejecting an approach, posting
   a patch or test build, or stating a constraint. Record a list of
   the direction items, with the speaker, or record "no maintainer
   direction" explicitly. Also note any **prior art**: open or merged
   PRs, posted patches, another contributor on the same route.

   When **no commenter carries a maintainer association** (every entry
   is CONTRIBUTOR or NONE), record "no maintainer direction" and treat
   limb (a) of `thread-and-conventions` as owing nothing to the
   thread — but check the **issue body** for direction of its own (a
   reporter naming the field or code site the fix should use, a draft
   patch), because on a thread with no maintainers that is the only
   direction available. In live mode on a classroom or house issue,
   where the other commenters are classmates, their posted
   reproductions and stated routes are **prior art, not direction**:
   gather them for `prior-art-engaged`, where a comment that
   acknowledges the parallel work and stands on its own evidence
   satisfies the check. A classmate's route never obliges the plan to
   follow it. Record prior-art usernames only as they appear in the
   package or thread; do not supply a name the source does not show.
4. **Read the `## Repro evidence` block, slowly, before the plan.**
   Write down: the environment it ran on; the steps; the artifact and
   the exact symptom it shows; and then, separately and most
   importantly, **every control run and what each one rules in or
   out**. State each control as a proposition, for example "no
   background painted, exit 0, therefore the background fill is
   necessary to the abort" or "top-level `[.a] | length == 1`,
   therefore `collect` already synthesizes null outside condition
   contexts". This list is the instrument for `diagnosis-grounded`; a
   control you did not write down is a control you will not apply.
   Where the evidence is a cannot-reproduce or a measurement matrix,
   note what it pins down rather than what it failed to show.
5. **Read the `## Candidate plan`.** Note its stated cause, its
   in-scope and not-in-scope statements, each discrete change it
   proposes, the files or sites it names, its approach and order, its
   test plan, and its stated risks. Enumerate the proposed changes as
   a numbered list of your own, one per independently shippable unit
   of work — this list, not the plan's own section headings, is what
   `scope-bounded` counts.
6. **Read the `## Candidate plan comment` last**, against the thread
   notes from step 3 and the policy classification from step 2.

In live mode, perform the same order against live sources: the issue
page, then the repo's CONTRIBUTING and AI policy files, then the
thread, then the student's own posted repro comment on that issue (or,
on the house issue, the repro pack as quoted in the drafts), then
`plan.md`, then the draft comment. If the drafts quote no repro
evidence at all, record that absence; it is what `diagnosis-grounded`
and `test-plan-decisive` then grade against.

## Evidence gathering

One gathering move per evidence family. `references/evidence-guide.md`
says where each lives and what good looks like there; these are the
steps for pulling it.

1. **Diagnosis and grounding.** Pull the plan's cause as a single
   quoted sentence. Set it beside the control-run propositions from
   read-order step 4. For each proposition, mark `consistent`,
   `contradicts`, or `not relevant`. Then check the plan's cause
   against each decisive observation in the evidence (intermediate
   values, timings, the stage at which the damage is already visible)
   and mark the same way. Record the single strongest contradiction,
   if any, with the quote that produced it.
2. **Scope.** Take the numbered list of discrete changes from
   read-order step 5. For each, record one word for what it serves:
   `defect` (it addresses the reported bug or its reproduction),
   `unrequested` (it is independently shippable work the issue did not
   ask for — migration, refactor, new option, adjacent UI, CI or
   harness change), or `support` (tests, docs, or a rename the fix
   itself requires). Then record the plan's not-in-scope statement
   verbatim, and whether each deferral carries a reason.
3. **Executability.** Record the specific files, functions, or code
   sites named, as a list. Record the chosen approach in one sentence.
   Then record what the plan's **first step** actually is, verbatim,
   and judge only this: could a stranger perform that first step
   today? Note separately any decision the plan leaves open, and
   whether it is the central decision or a secondary detail.
4. **Test plan.** Quote the test plan. Extract every outcome it names
   and mark each `observable` (a reader could check it and it would
   differ before and after the fix) or `not observable` (a suite-green
   claim, an impression, a feeling). Then check whether any observable
   maps onto the repro evidence's own steps or artifacts, and record
   that mapping.
5. **Honesty.** Record each stated risk, unknown, or open question,
   and what the plan says it would do about each. Note any place the
   plan states as settled something the evidence left open.
6. **Comms.** For limb (a): for each direction item from read-order
   step 3, record whether the comment **follows it**, **declines it
   with a reason**, or **does not mention it**. For limb (b): take the
   policy classification from read-order step 2 and record whether the
   comment contains a sentence naming AI assistance and its extent.
   Also record, for `prior-art-engaged`, what the comment says about
   each prior-art item.

In live mode the same six moves apply, with the issue-side half
gathered from the locations `references/evidence-guide.md` names on
GitHub, and the candidate half from the student's drafts. Gather from
the drafts only what the drafts contain or quote: other files in the
working directory are not part of the package a maintainer will read.

## Check execution

1. **Execute the checks in this order:** `diagnosis-grounded`,
   `scope-bounded`, `executable`, `test-plan-decisive`,
   `thread-and-conventions`, then the preferred checks
   `risks-stated` and `prior-art-engaged`. The order runs
   cause-first because a plan whose cause the evidence excludes is
   already held, and grading it in that order keeps the deciding
   reason the most upstream one rather than whichever check happened
   to be read first.
2. **Execute every check, regardless of earlier failures.** Do not
   stop at the first fail. The output reports a grade for all seven,
   because the student needs the whole picture and the harness records
   each one.
3. **Grade each check from the gathered evidence only**, using the
   pass condition exactly as the rubric words it. Do not re-read the
   package for a check whose family you already gathered in the
   Evidence gathering stage; re-reading invites a second, different
   reading of the same facts. Re-read only when the gathered note is
   genuinely insufficient to apply the stated condition, and say so in
   the output when that happens.
4. **For each check, write one line of evidence**: the fact or the
   quote that decided it. A grade without a deciding fact is not a
   grade. When a check fails, the evidence line names the specific
   sub-condition or limb that failed, in the rubric's own terms.
5. **When the evidence a check needs is genuinely absent from the
   package** — not merely hard to find, but not there — grade the
   check `unclear` and say in the evidence line what was missing and
   where it was looked for. Absence of evidence is `unclear`; evidence
   that is present and fails the condition is `fail`. These are
   different grades and the distinction is information for the
   student.
6. **Where a check's condition does not settle the case**, apply it in
   the direction the rubric's own wording points and say in the
   summary that the call was close. Do not invent an additional
   requirement to break the tie, and do not soften a stated fail
   condition because the rest of the plan is strong: `scope-bounded`
   fails a correct fix wrapped in unrequested work, and
   `thread-and-conventions` fails an otherwise excellent plan whose
   comment omits a required disclosure. Those are the rubric's
   decisions, not discretionary ones.
7. **Where this procedure is silent on a step you needed**, take the
   most literal reading of the rubric and **report the gap in the
   summary** rather than inventing a step and leaving it unrecorded.

## Verdict assembly

1. Collect the five required grades (`diagnosis-grounded`,
   `scope-bounded`, `executable`, `test-plan-decisive`,
   `thread-and-conventions`) and the two preferred grades
   (`risks-stated`, `prior-art-engaged`).
2. **Convert `unclear` to `fail` for every required check**, per the
   rubric's verdict rule. Keep the original `unclear` grade in the
   output JSON — the verdict treats it as a fail, but the student
   needs to see that the cause was absence rather than a failed
   condition.
3. **Discard the preferred grades for verdict purposes.** They appear
   in the output and in the summary, and they never move the verdict
   in either direction.
4. **Apply the rule:** `accept` if all five required checks are
   `pass`; otherwise `reject`. There is no third verdict and no score
   threshold — one required fail is enough to hold the package.
5. **Name the deciding check.** On a `reject`, the deciding check is
   the earliest failing check in the execution order from Check
   execution step 1; quote its evidence line in the summary as the
   reason the package is held, and list any other failing checks after
   it. On an `accept`, state that all five required checks passed and
   quote the evidence line of the check that was closest to failing,
   so the student knows where the plan is thinnest.
6. Emit the fenced JSON block in the format `SKILL.md` specifies, with
   one entry per check in the execution order, and nothing after it.
