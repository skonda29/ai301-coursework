# Procedure: how this tool grades a PR package

## Read order

Read the whole package before grading anything, in this order. The
order is not cosmetic: the plan is the standard the diff is measured
against, so the plan is read **before** the diff, and the diff is read
**before** the description. Reading the description first is the single
most expensive mistake available here — a confident "implements the
plan exactly" primes you to skim the diff for confirmation, and the
packages that fail `diff-matches-plan` are exactly the ones whose
descriptions read best.

1. **Read the `## Issue` section.** Note the reported symptom and the
   specific observable it names (an error string, an exit code, a wrong
   output). One line is enough. This is what any evidence must
   eventually show changing.
2. **Read the `## Repo facts` block** and write down two things
   verbatim, before any PR text can colour them:
   - the `pull requests:` line — every section, checklist item, issue
     link, and in-diff artifact (changelog, whatsnew, cheatsheet) it
     asks for, or "no template, nothing owed";
   - the `contribution policy:` line — and classify it now as one of
     **no AI rule** / **responsibility-only** / **own-words** /
     **disclosure-required** / **disclosure-required-but-exempts-this-artifact**.
     Write the classification down; limb (b) of `standards-met` reads
     it later and must not re-derive it from the description.
3. **Read the `## Thread highlights` section.** For each entry note the
   speaker's association (OWNER / MEMBER / COLLABORATOR / CONTRIBUTOR /
   NONE) and whether they gave direction or asked a question the fix
   would answer. Record "no maintainer direction" explicitly when none
   is present. Association is what separates direction from opinion.
4. **Read the `## Plan context` block, slowly, and build two lists.**
   This is the instrument for the heaviest check, so build it
   deliberately rather than reading for gist:
   - **List P (promised work):** every deliverable the plan commits to,
     one line each — the code change, each additional deliverable (a
     docs update, a changelog entry, a test), and the files it names.
   - **List X (excluded work):** everything the plan's not-in-scope
     line rules out, plus every limit, deferral, or known shortfall the
     plan records. Quote the not-in-scope sentence verbatim; it is
     frequently the single most decisive sentence in the package.
   Also note the repro evidence the plan built on: its steps and the
   artifact it produced. That is the before half of any before/after.
5. **Read the `## Candidate PR`'s diff, and build List D.** Walk the
   diff hunk by hunk and record **every file path it touches**, with a
   one-line note of what changed in each. Then, for each file in List
   D, mark it `planned` (named in List P or clearly required by it),
   `excluded` (List X rules it out), or `unaccounted` (neither).
   While walking, flag any hunk that contains debug output,
   commented-out code, unused code, or a change whose before and after
   are semantically identical. This single pass feeds both
   `diff-matches-plan` and `diff-reviewable`; do not walk the diff
   twice and risk two different readings of the same hunks.
6. **Read the commit list.** Note vague or process-flavoured messages
   (`wip`, `fix`, `fmt + cleanup`, `misc cleanups while debugging`) as
   a signal to re-read the diff closely. They are corroboration only,
   never a fail by themselves.
7. **Read the `## Test evidence` block.** Record what was run, what it
   printed, and whether a before and an after both appear. Then check
   which of the plan's test-plan items it covers and which it does not.
8. **Read the title and description last**, against everything above.
   Record every claim they make about fidelity, completeness, or
   coverage as a **claim to verify**, not as a fact.

In live mode, perform the same order against live sources: `scope.md`
first (per the frame), then the issue and the repo's template and
policy files, then the thread, then `plan.md` with its `## Deviations`
section, then `git diff main...HEAD` (use `--stat` for the file list,
then the full diff), then `git log main..HEAD --oneline` for the
commits, then the student's captured evidence, then the draft title and
description.

## Evidence gathering

One gathering move per evidence family. `references/evidence-guide.md`
says where each family lives and what good looks like there; these are
the steps for pulling it.

1. **Plan-to-diff correspondence.** Take List P, List X and List D from
   read-order steps 4 and 5. Produce three findings:
   - every List D file marked `unaccounted` or `excluded`, with the
     hunk that makes it so;
   - every List P deliverable with **no corresponding hunk** in the
     diff;
   - for each of the above, whether the plan's deviation note or the
     description accounts for it, quoting the sentence that does.
   A difference accounted for in writing is not a finding; record it as
   accounted and move on.
2. **Description claims.** Quote every sentence in the description that
   asserts fidelity or completeness ("exactly as planned", "no changes
   beyond the plan", "no functional changes outside X", "the docs now
   document this"). Set each against the findings from move 1 and mark
   it `supported` or `contradicted`, naming the hunk or the absence
   that decides it.
3. **Evidence-to-change correspondence.** Quote the test evidence.
   Then answer three questions in order and record each: does a
   **before** appear; does an **after** appear; and does the thing
   exercised **touch a line the diff changed**? For the third, name the
   diff hunk the evidence runs through. If the evidence runs a control
   case, a different flag, or an input the diff does not affect, record
   that explicitly — it is the most common way a confident evidence
   block proves nothing. Then list each plan test-plan item as
   `covered`, `uncovered`, or `uncovered-but-disclosed`.
4. **Repo checks.** From the repo-facts note in read-order step 2, list
   the checks the repo asks for. For each, record whether the evidence
   shows it run **with its outcome visible**, shows it claimed without
   output, or does not mention it.
5. **Diff debris.** Collect the hunks flagged during the read-order
   step 5 walk, one line each, with the file and what the debris is.
   Record "no debris found" explicitly when the walk found none, so the
   check has a stated basis either way.
6. **Standards.** For limb (a): for each template ask and contribution
   ask from step 2, record `met`, `unmet`, or `not applicable`, and for
   an ask naming an in-diff artifact, say whether that file appears in
   List D. For limb (b): take the policy classification from step 2 and
   record whether the description contains a sentence naming AI
   assistance and its extent. Do not re-read the policy here; use the
   classification already written down.
7. **Thread and regression (preferred checks).** Record what the
   description says about each direction item or open question from
   step 3, and whether List D includes a test or fixture that would
   fail without the fix.

In live mode the same seven moves apply, with the issue-side half
gathered from the locations `references/evidence-guide.md` names on
GitHub and the candidate half from the working copy and the drafts.
Gather only what the package contains or quotes.

## Check execution

1. **Execute the checks in this order:** `diff-matches-plan`,
   `evidence-decisive`, `diff-reviewable`, `standards-met`, then the
   preferred `thread-engaged` and `regression-guard`. The order runs
   fidelity-first because a diff that is not the planned change makes
   every downstream question moot, and running it first keeps the
   deciding reason the most upstream one rather than whichever check
   happened to be read first.
2. **Execute every check, regardless of earlier failures.** Do not stop
   at the first fail. The output carries a grade for all six: the
   student needs the whole picture, and the harness records each entry.
3. **Grade each check from the gathered findings only**, applying the
   pass condition exactly as `rubric.md` words it. Do not re-read the
   package for a check whose family was already gathered — a second
   reading of the same hunks produces a second opinion, which is how an
   executor becomes unrepeatable. Re-read only when a gathered note is
   genuinely insufficient to apply the stated condition, and say in the
   summary when that happened.
4. **Write one evidence line per check**: the fact or quote that
   decided it, pointing at a hunk, a file path, a line of output, or a
   clause of policy. On a fail, the line names the specific
   sub-condition or limb from the rubric that failed. A grade with no
   deciding fact is not a grade.
5. **When the evidence a check needs is genuinely absent** from the
   package — not merely hard to find — grade `unclear` and say in the
   evidence line what was missing and where it was looked for. Evidence
   that is present and fails the condition is `fail`; evidence that
   does not exist is `unclear`. The distinction is information for the
   student, so keep them apart.
6. **Where a condition does not settle the case**, apply it in the
   direction the rubric's wording points and note in the summary that
   the call was close. Do not invent an extra requirement to break a
   tie, and do not soften a stated fail condition because the rest of
   the package is strong: `diff-matches-plan` fails a correct,
   well-evidenced fix that also carries an unannounced flag, and
   `standards-met` fails an excellent PR whose description omits a
   required disclosure. Those are the rubric's decisions.
7. **Where this procedure is silent on a step you needed**, take the
   most literal reading of the rubric and **report the gap in the
   summary** rather than inventing a step and leaving it unrecorded.

## Verdict assembly

1. Collect the four required grades (`diff-matches-plan`,
   `evidence-decisive`, `diff-reviewable`, `standards-met`) and the two
   preferred grades (`thread-engaged`, `regression-guard`).
2. **Convert `unclear` to `fail` for every required check** when
   applying the rule, per `rubric.md`'s verdict rule. Keep the original
   `unclear` in the output JSON: the verdict treats it as a fail, and
   the student still needs to see that the cause was absence.
3. **Discard the preferred grades for verdict purposes.** They appear
   in the output and the summary and never move the verdict either way.
4. **Apply the rule:** `accept` if all four required checks are `pass`;
   otherwise `reject`. There is no third verdict and no threshold — one
   required fail is enough to hold the package.
5. **Name the deciding check.** On a `reject`, the deciding check is
   the earliest failing check in the execution order from Check
   execution step 1. Quote its evidence line in the summary as the
   reason the package is held, then list any other failing checks after
   it. On an `accept`, state that all four required checks passed and
   quote the evidence line of the check that came closest to failing,
   so the student knows where the package is thinnest.
6. Emit the fenced JSON block in the schema `SKILL.md` fixes, one entry
   per check in execution order, and nothing after it.
