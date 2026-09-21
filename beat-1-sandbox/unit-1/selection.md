# Unit 1: Issue Selection

## Chosen issue

**Link:** https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62

**Skill verdict output:**

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62",
  "checks": [
    {"name": "repo-not-archived", "grade": "pass", "evidence": "archived: no"},
    {"name": "maintainer-active", "grade": "pass", "evidence": "Andrew Burke commits within 365d"},
    {"name": "scope-bounded", "grade": "pass", "evidence": "single bug in api/routes/health.py, clear repro steps"},
    {"name": "not-claimed", "grade": "pass", "evidence": "no assignee, no linked PR, 0 comments"},
    {"name": "ai-contribution-allowed", "grade": "pass", "evidence": "no stated AI policy"},
    {"name": "good-first-issue-label", "grade": "pass", "evidence": "labels include good first issue"},
    {"name": "maintainer-engaged", "grade": "pass", "evidence": "opened by Aburke225 (OWNER)"}
  ],
  "verdict": "accept"
}
```

## Reflection

### Run history

I started by writing a rubric covering all four criterion families from the lecture (maintainer alive, repo in use, scope fits a newcomer, not already claimed) plus the fifth surface from the evidence guide (AI contribution policy). My initial rubric had five required checks (`repo-not-archived`, `maintainer-active`, `scope-bounded`, `not-claimed`, `ai-contribution-allowed`) and two preferred checks (`good-first-issue-label`, `maintainer-engaged`).

My first smoke run (`--limit 3`) scored 2/3: issue-01 was incorrectly rejected on `scope-bounded`. The model misread issue-01's multi-file documentation plan (updating README, creating a new task page, updating several related docs files) as an umbrella issue. The issue is actually one coherent documentation task with implementation steps—not multiple independent sub-tasks for separate contributors.

I revised the `scope-bounded` check's condition (a) to explicitly distinguish between "a single coherent task that touches multiple files" (bounded, pass) and "an umbrella designed for multiple contributors to each take a separate sub-task" (not bounded, fail). I added clarifying language: "A single coherent task such as 'document feature X' or 'fix bug Y' that describes implementation steps or touches several related files is NOT an umbrella."

I then tested with `--only issue-01,issue-20` and both matched gold. A wider `--only` run on edge cases (issue-05, issue-09, issue-12, issue-15, issue-20) scored 4/5: issue-20 was incorrectly accepted. The model saw issue-20's template-style spec ("Success looks like: logo tool in the shapes toolbar…") as sufficient specification, but the gold label rejects it because the feature requires a product decision (should a generic whiteboard tool embed a company logo?) that no maintainer has endorsed.

I revised condition (b) to capture "bare feature wish or unvalidated product proposal"—a feature that requires a product/design decision the project has not committed to, with no maintainer endorsement. I added: "A bug report, a documentation task for existing functionality, or a detailed proposal with clear acceptance criteria is not a bare feature wish even if no maintainer has responded." This preserved issue-01 (documentation for existing GA feature) while correctly rejecting issue-20.

After this revision, `--only issue-01,issue-20` confirmed both correct. My first full run scored **20/20** with all categories passing (claimed 4/4, clear-accept 8/8, dead-repo 3/3, policy 1/1, scope 4/4). The saved `--save-run` also scored **20/20**.

### Issue analysis

**Issue-12** (`bookwyrm-social/bookwyrm#1133`): My rubric's verdict was **reject**; the gold label is also **reject**.

My rubric rejected issue-12 because the `ai-contribution-allowed` check failed. The contribution policy in the repo-facts block states: "Meaningful human interaction is the whole point of BookWyrm. We do not accept AI-generated code or documentation." This is an outright ban, matching the fail condition verbatim ("we do not accept AI-generated code").

What makes this issue interesting is that it passes every other check cleanly. The repo is active (last push 2026-08-12, release v0.9.1 on 2026-07-20). The scope is bounded (add in-progress books to a reading-goal progress bar—a single UI enhancement). It is unclaimed (no assignee, no linked PRs, no active claim comments). A maintainer even engaged in the thread (mouse-reeve, a MEMBER, suggested using custom HTML/CSS). Without the `ai-contribution-allowed` check, this issue would have been accepted, which is precisely why the policy surface exists in the rubric: it catches the case where every other signal says "go" but the repo's rules say "stop."

My rubric read this correctly because the pass condition draws a bright line between outright bans and conditional policies. The BookWyrm policy is unambiguous—"We do not accept AI-generated code or documentation"—and my check fails on exactly that language. The conditional policies in other eval issues (e.g., conda's "generative AI tools welcome; you are responsible…" and zulip's "AI tools allowed; contributors must personally understand…") correctly pass because they set conditions, not bans.

### Check rationale

**Check:** `not-claimed`

**Current wording (from rubric.md):**

> **All three** of the following must hold: (1) **No assignee** is currently listed. (2) **No linked PR is currently open** (a closed-unmerged PR is an abandoned attempt and does not block; a merged PR does not block). (3) **No active claim comment** exists — defined as a comment within **180 days** of the capture date where someone states they are working on the issue ("I'll take this," "working on this," "can I work on this") AND that claim has not been released (by a maintainer removing assignment, by a bot auto-unassignment message, or by the claimant saying they are dropping it) AND is not contradicted by a maintainer explicitly inviting new takers. A claim older than 180 days with no follow-up PR or activity is stale and does not block. A maintainer or collaborator explicitly stating that a specific contributor's PR is the only one accepted counts as an active claim regardless of the 180-day window.

**Why it reads evidence this way:** The claim check has to handle a wide range of real-world situations the eval set presents. The 180-day staleness window exists because of issue-09 (conda#7617): a contributor offered to work on it in 2022, but 4+ years later with no follow-up, the maintainer (a MEMBER) explicitly invited new takers—that claim is dead. Without the staleness rule, a reasonable rubric would reject a perfectly available issue. The "closed-unmerged PR is not a claim" rule exists because of the same issue: its linked PR #11627 is closed (not merged), which is an abandoned attempt, not an active claim. The "open linked PR means claimed" rule is the most common claim signal in the eval set: issue-03 has an open PR the collaborator explicitly locked to one contributor, issue-08 has an assignee plus an open draft PR, issue-13 has two open PRs racing, and issue-18 has three open PRs plus claim comments. The "bot auto-unassignment releases a claim" clause handles the zulip issues where zulipbot repeatedly assigns and unassigns people after 14 days of inactivity.

### Trade-offs

My rubric trades **nuance on borderline scope calls** for **precision on the four families it covers well**. The `scope-bounded` check uses five distinct sub-conditions, which makes it thorough but also means the model must apply judgment (e.g., "is this an umbrella or a single task with multiple steps?"). The initial misfire on issue-01 showed that the original wording was too aggressive—it caught false positives on legitimate multi-file tasks. The revised wording is more explicit but longer, which increases the chance that a model skims it imprecisely on a different rubric run.

The rubric also trades **recall for specificity** on the claim check. The 180-day staleness window is a hard threshold: a claim from day 181 is stale, but one from day 179 is active. In practice, this worked perfectly on the eval set, but a real-world scenario with a 170-day-old claim and no follow-up might deserve the same stale treatment. I chose a fixed threshold over "use judgment" because the assignment asks for "thresholds with numbers, not adjectives"—the grading discipline is served by a number the model can check mechanically, even if the number is somewhat arbitrary.

Finally, the preferred checks (`good-first-issue-label`, `maintainer-engaged`) are deliberately weak—they rank but never gate. A rubric that made `maintainer-engaged` required would reject issue-01 (opened by a CONTRIBUTOR, no maintainer comments) and issue-16 (same situation), both of which are clear accepts. The trade-off is that accepted issues without maintainer engagement rank lower, which is a softer but more accurate signal than a hard gate.

### Selection rationale

I ran my skill in live mode on three open Path Review issues: #73 (README/.env.example docs mismatch), #62 (health check uses `settings.redis_host` which doesn't exist), and #53 (PII scrubber misses parenthesized phone numbers). All three were accepted—they share the same repo-level signals (active maintainer, not archived, no AI ban, Path Review house rules) and each is a bounded, unclaimed, good-first-issue-labeled tier-1 bug.

The skill ranked #73 highest on fit because it is the smallest task (pure docs, 1–2 hour estimate) and my profile lists "prefers docs." I chose **#62** instead because it is the strongest candidate for carrying into Unit 2:

1. **Reproducibility.** The issue provides exact steps to reproduce (`GET /health` with Redis running → 503 with `AttributeError` for `redis_host` in the log) and names the precise code location (`api/routes/health.py`). Unit 2 requires reproducing the bug, so having concrete repro steps and a named file makes that deliverable straightforward.

2. **Fit for learning.** The fix requires reading how `core/config.py` defines `Settings` (a single `redis_url` field) and changing the health-check probe to use it. This exercises Python, FastAPI configuration patterns, and Redis client setup—skills in my profile I want to deepen, whereas #73 (docs-only) and #53 (regex tweak) would teach me less about backend architecture.

3. **Bounded but substantive.** Unlike #73 (which is almost trivially small) and #53 (which is a one-line regex fix), #62 requires understanding the interaction between the config model and the health route, making it a better demonstration of scope judgment for the course without being complex enough to risk getting stuck.
