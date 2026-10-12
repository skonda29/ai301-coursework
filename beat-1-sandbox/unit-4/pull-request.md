# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

---

## Your pull request

**Pull request**

<!-- PASTE THE PR URL HERE after opening it. Open with:
     cd ~/Desktop/codepath/pathreview-ai301-fa26-s3
     gh pr create --repo codepath/pathreview-ai301-fa26-s3 \
       --base main --head skonda29:fix/62-health-redis-url \
       --title "$(head -1 pr_draft.md)" \
       --body "$(tail -n +2 pr_draft.md)"
     Then replace this whole comment with the printed PR link:
     https://github.com/codepath/pathreview-ai301-fa26-s3/pull/<N> -->

**Branch**

fix/62-health-redis-url

**My pr-precheck verdict on the draft**

I ran my own tool on the package before opening the PR, from the root of my fork's clone,
on the branch, with the command the assignment specifies:

```
claude "pr-precheck: grade my draft PR for issue <URL>: plan in plan.md, the diff on this
branch, title and description in pr_draft.md, test evidence in test_evidence.md"
```

Verdict: **accept**, all four required checks passing.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62 — branch fix/62-health-redis-url (pr_draft.md)",
  "checks": [
    {"name": "diff-matches-plan", "grade": "pass", "evidence": "git diff --stat main...HEAD lists exactly api/routes/health.py, pyproject.toml, tests/unit/test_health.py — the plan's '## Files' set; the one difference (3 tests vs the plan's 2) is recorded in plan.md Deviations note 3 and restated in the description's Changes bullet."},
    {"name": "evidence-decisive", "grade": "pass", "evidence": "Before/after through the changed hunk: '\"redis\":\"unhealthy\"' with error=\"'Settings' object has no attribute 'redis_host'\" becomes '\"redis\":\"healthy\"' with redis_health_check_passed; negative control with Redis stopped logs 'Error 61 connecting to localhost:6379. Connection refused.'; lint/test-unit shown passing and typecheck/integration shown with their real non-passing outcomes rather than omitted."},
    {"name": "diff-reviewable", "grade": "pass", "evidence": "Walked all three hunks: 6 deleted lines replaced by 'r = redis.Redis.from_url(settings.redis_url, decode_responses=True)', one disable_error_code list edit, one new test file — no debug output, no commented-out code, no dead code, no re-indent churn, no unrelated files; single conventional commit 71fcfe8."},
    {"name": "standards-met", "grade": "pass", "evidence": "Limb (a): all six PULL_REQUEST_TEMPLATE.md sections present and answered, 'Closes #62' present, checklist present with unticked boxes each carrying output and a reason, and the CONTRIBUTING-required pyproject.toml suppression removal appears in the diff (no xfail marker for #62 exists — verified by grep). Limb (b): no AI rule stated in CONTRIBUTING, README, or .github, so nothing owed; the description discloses anyway."},
    {"name": "thread-engaged", "grade": "pass", "evidence": "All nine thread comments carry 'association: none' — no maintainer direction, so nothing owed; the description nonetheless follows the issue body's stated direction, quoting that redis_url \"is what the probe should use instead\", and cites #61 for the postgres deferral."},
    {"name": "regression-guard", "grade": "pass", "evidence": "tests/unit/test_health.py::test_redis_probe_uses_redis_url_and_reports_healthy asserts from_url.assert_called_once_with(settings.redis_url, decode_responses=True) and dependencies['redis'] == 'healthy' — both fail on main, where the probe calls redis.Redis(host=settings.redis_host, ...) and reports unhealthy."}
  ],
  "verdict": "accept"
}
```

The run also named the thinnest spot, which is worth recording because it is structural rather
than fixable: `standards-met` limb (a) passes with the template's **"CI is green on this PR (all
five jobs)"** box unticked, because nothing can trigger those jobs until the PR exists. My
description leaves the box unticked and commits to confirming the run afterwards rather than
ticking a box for work that has not happened.

## Eval iterations

**Run history**

1. **Targeted smoke run** — `--only pkg-01,pkg-20,pkg-13,pkg-09,pkg-18,calib-03
   --include-calibration`: **5/5 scored items agreeing** (`clear-accept 1/1`,
   `silent-drift 1/1`, `standards-wall 2/2`, `unreviewable 1/1`), with `calib-03` also
   agreeing unscored. I picked these six rather than `--limit 6` because they are the
   judgments I expected to get wrong: **both** members of the 2-package `standards-wall`
   category (the one the floor turns on — `pkg-01`'s ignored template and `pkg-20`'s missing
   disclosure fail for unrelated reasons), a disclosed-shortfall accept (`pkg-13`), the two
   packages the gold notes call arguable (`pkg-09`, `pkg-18`), and the rotation trap
   (`calib-03`, the beautiful package whose second hunk rewrites a file the plan never
   touches). About \$1.50 to test the five calls most likely to be wrong, before \$5 on a full
   run.
2. **First full run** — **19/20 scored items**, bar PASS, every category matched
   (`clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2
   unreviewable 3/3`). One disagreement: `pkg-16`, which my rubric rejected on
   `diff-matches-plan` and the gold labels accept.
3. **Canary re-run** — `--only pkg-16,pkg-03,pkg-06,pkg-09,pkg-17,calib-03,pkg-13
   --include-calibration` after narrowing `diff-matches-plan`: **6/6 scored agreeing**, plus
   `calib-03` agreeing unscored. `pkg-16` flipped to accept as intended, and **all four**
   `silent-drift` packages held as rejects along with the trap — which is exactly what the
   canaries were for, since the edit loosened the check that catches drift.
4. **Confirming full run** — **20/20 scored items**, bar PASS, every category matched
   (`clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2
   unreviewable 3/3`). This is the run in the committed `eval-run.txt`, whose agreement line
   reads `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

One revision happened across those runs, described under Package analysis. All four file
fingerprints in the committed `eval-run.txt` header — `SKILL.md sha256:3c8aaed6f6dd59f1`,
`rubric.md sha256:53127bae09c71c46`, `procedure.md sha256:590d06b321a8fbbf`,
`evidence-guide.md sha256:25cec5d0d0963ca5` — match the bytes of the files in
`tools/pr-precheck/` exactly, so the committed run is a record of the committed tool.

**Package analysis**

**`pkg-16`** (`kubernetes/minikube#23471`). On my first full run my rubric decided **reject**;
the gold label is **accept**. After one revision it decided **accept**, and that is the result
in the committed run. It was the only package my tool ever disagreed with, and it is one of the
four the assignment calls genuinely arguable.

The deciding check was `diff-matches-plan`, and this is the evidence line my first run recorded:

> Diff's own comment 'Partial success stays non-fatal for multi-node start path' shows only
> partial-failure tolerance preserved for start path; the new `if len(succeeded)==0 { return
> err }` branch in shared DoLoadImages also changes start-path behavior on total failure
> (nil→error), unaccounted by the plan's deviation note (which only addresses per-node partial
> tolerance) and contradicting the description's unqualified claim that 'the start-time image
> loading path keeps its tolerant behavior'.

That is a real observation, carefully made, and it is still wrong. The reasoning traced a true
consequence: `DoLoadImages` is shared between the `image load` command and the start path, so
adding a total-failure error branch inside it does change what the start path does when every
target fails. The description's deferral paragraph says the start path "keeps its tolerant
behavior" without qualifying which failures, so read strictly, diff and description disagree.

What the check missed is that **the plan specified exactly this change, in exactly this
function**. Its scope sentence reads: "`DoLoadImages` returns an aggregated error when every
target machine failed; `cmd/image.go`'s load command surfaces that error to the user and exits
non-zero." The deferral that follows is scoped by its own parenthetical — "the start-time
loading path (multi-node, where a **per-node** failure may genuinely be non-fatal) keeps its
current tolerant behavior" — and the open question it leaves to maintainers is which start-path
failures should become fatal. Total failure was never promised to stay tolerant. The diff's
inline comment draws the same boundary out loud: "when nothing succeeded, the operation failed,
whatever the start path tolerates (#23471)."

So my check had confused a **consequence of the planned change** with **unplanned work**. The
plan named the function; the change to that function necessarily reaches its other caller; that
reach is the plan being implemented, not scope being added. I had written the check to compare
the diff against the plan's named files and scope, which is the right instrument — it is what
catches `calib-03`'s rewrite of `print/property.js` — but I had not said what to do when the
named change ripples inside the named code. I added that limit (quoted in full below), and on
the re-run the same check passed it with:

> List D (cache_images.go, image.go, cache_images_test.go) matches List P's named files
> exactly; deferred start-path behavior preserved per plan's deviation note and description's
> 'deliberately out of scope' paragraph

**Check rationale**

Quoted from the `rubric.md` uploaded to `tools/pr-precheck/` (sha256 `53127bae09c71c46`, the
same fingerprint recorded in the header of the committed `eval-run.txt`):

> | diff-matches-plan | The set of files and changes in the diff, compared against the plan-context block's scope statement, its not-in-scope line, its named files, and its recorded deviation notes; then the PR description's own claims about fidelity read against the same diff. Live: `git diff main...HEAD` against `plan.md` including its `## Deviations` section. | **The diff delivers the plan's work and only the plan's work, except where a difference is recorded in writing.** A difference is recorded when the plan's deviation note names it **or** the description states it plainly as a limit, a deferral, or an addition; a deviation carried in both is the strongest form and the one to look for. **Fail** if any of: (a) **the diff does more than the plan** — it changes a file the plan never names, adds a user-facing flag, option, or setting, renames or restructures code, or reworks an adjacent behavior, and no deviation note or description sentence accounts for it. A plan that says "Not in scope: new flags" against a diff adding one is this failure in its clearest form; (b) **the diff does less than the plan** while the description claims otherwise — the plan promised two deliverables and one is absent, or the description says a thing is now documented, handled, or fixed when no hunk does it. Claiming more than the diff delivers is drift in the shrinking direction and fails the same way; (c) **the description asserts a fidelity the diff contradicts** — "implements the plan exactly", "no changes beyond the plan", "no functional changes outside X" — while an unaccounted hunk sits in the diff. Treat such a sentence as a claim to verify, not as evidence. A deliberately scoped-down PR whose shortfall is recorded in the plan and restated in the description **passes**: the disclosed shortfall is the honest case this check is built to protect. **Two limits on what counts as "more than the plan".** First, the unit of comparison is **the change the plan specified, not every downstream consequence of it**: when the plan specifies a change to a function, module, or file it names, the effect that change necessarily has on the other callers of that same named code is **planned work**, not drift — a plan saying "function F returns an aggregated error when every target failed" authorizes F returning that error to every caller of F, unless the plan states that a particular caller must be insulated from it. Grade the hunks against the plan's specified change; do not treat a ripple inside the named code as an unannounced addition, and do not read a description's shorthand summary of a deferral as a promise that no behavior anywhere else moved. Second, work the plan **names as support** for its change — its test, its fixture, its changelog entry — is planned even when the plan did not enumerate the file. Drift is a change to code the plan never specified and no note accounts for, which is a different thing from a consequence of the change it did specify. | required |

Why it reads this way. This is the check that carries the most weight in the set: four packages
fail on it by category, the rotation trap turns on it, and two of the seven clear accepts are
only accepts *because* of its disclosed-shortfall clause. Three decisions in the wording did
that work.

**It compares file sets, not prose.** The gathering instrument is mechanical — enumerate the
diff's paths, enumerate the plan's named files, and mark each path `planned`, `excluded`, or
`unaccounted`. That is what catches `calib-03`, the package built to split "students who diff
the diff against the plan from students who graded the description": a perfect checklist,
decisive evidence, a confident "no changes beyond the plan", and a second hunk silently
rewriting `src/language-js/print/property.js`, a file the plan never names. No amount of reading
the description reveals that. Only the file sets do.

**It treats fidelity claims as claims, not evidence.** The sentence "Treat such a sentence as a
claim to verify, not as evidence" exists because in this material the assertion correlates with
drift rather than fidelity: `pkg-03` says "implements the posted plan exactly" over an
unrequested rewrite plus a new config option, `pkg-06` says "no functional changes outside the
system menu fix" over a renaming pass plus a new setting, and `pkg-09` says "exactly as planned"
over a new `--time-format` flag its plan explicitly excludes. A grader that credits those
sentences fails all three.

**It fails in both directions.** Sub-condition (b) exists for `pkg-17`, where the plan promises
a dev warning *and* a docs update, the diff contains only the warning, and the description says
the docs "now document the limitation". Claiming a deliverable the diff does not contain is
drift in the shrinking direction, and it is the direction a reader is least likely to check,
because nothing suspicious is *present* — something promised is simply absent.

What I revised, and what I rejected. The revision is the two-limit paragraph at the end,
added after `pkg-16` (above). What I rejected in favour of it was the obvious quick fix: making
the check tolerate any difference the description mentions at all. That would have passed
`pkg-16` in one line, and it would have passed `pkg-03`, `pkg-06`, and `pkg-09` too, since each
of those describes its extra work somewhere in its own prose — `pkg-09`'s third commit is
literally titled "cli: add --time-format for custom date parsing". Mentioning extra work is not
the same as accounting for it against a plan that excluded it. The narrower limit — a ripple
inside code the plan named is planned; a change to code the plan never specified is not — fixed
`pkg-16` without touching any of the four.

**Trade-offs**

What `diff-matches-plan` gives up is **the ability to see work the diff does not contain and
the plan did not name**. It compares three written artifacts against each other, so it is blind
to anything outside all three. A PR that implements its plan faithfully while introducing a bug
the plan never anticipated passes this check, correctly — that is `evidence-decisive`'s job and,
past that, a human reviewer's. I accepted that boundary deliberately: a check that tried to also
judge whether the planned change was *a good idea* would be answering a different question than
"is this ready to submit", and the contract forbids the tool from wandering there.

The case I accept it will miss is narrower and follows from the `pkg-16` revision. The limit I
added trusts the plan's naming: a ripple inside named code counts as planned. A plan that names
a very broad target — a whole module, or a function called from a dozen places — therefore
licenses a correspondingly broad set of consequences, and a PR could ride that breadth into real
unreviewed scope while passing. The eval set contains no such package, so the run gives me no
evidence either way, and I would rather say that than claim the check is sound against a case it
was never tested on. In live mode the exposure is smaller, because the plan is mine and I know
how broadly I named things.

**The canary that proved the revision safe.** Loosening the check that catches drift is exactly
the change that could flip a `silent-drift` package into a false accept, so before spending a
confirming full run I re-ran with every member of that category as a canary — `pkg-03`,
`pkg-06`, `pkg-09`, `pkg-17` — plus `calib-03` as the trap and `pkg-13` as a disclosed-shortfall
control. All six scored packages agreed, at about \$1.75 instead of \$5, and the following full
run confirmed it at 20/20 with `silent-drift 4/4`. They held because none of them fails on a
ripple inside named code: `pkg-03` and `pkg-06` add user-facing settings their plans never
mention, `pkg-09` adds a flag its plan explicitly excludes, `pkg-17` is the shrinking direction,
and `calib-03` edits a file outside the plan's `Files:` line entirely. The revision changed how a
*named* target's consequences are counted; it did not weaken the requirement that the target be
named.

**One gap I chose not to close, and why.** Running the tool on my own PR draft, it reported a
genuine procedure gap: `procedure.md` does not say how to grade a template checklist item that
**cannot** be satisfied before the PR exists. The Path Review template's first Testing box is
"CI is green on this PR (all five jobs)", and nothing can trigger those jobs until the PR is
opened. The tool resolved it correctly on its own, taking the rubric's literal fail condition
("a required checklist **absent**") and passing a checklist that is present with every item
addressed — then reported the gap instead of hiding it, which is the behavior `SKILL.md`
instructs. I left `procedure.md` unchanged. Patching it would have changed the file's
fingerprint and made the committed `eval-run.txt` a record of a tool I no longer had, and the
honest options were to re-run the full eval for \$5 or to document the gap here. The gap moves no
verdict in the eval set — no bundle contains an unopened PR — so I documented it. That is the
trade: a known, written-down gap in the procedure, in exchange for a committed run whose four
fingerprints still match the four files I uploaded.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
