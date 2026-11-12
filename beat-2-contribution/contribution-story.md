# Contribution: License metric

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Srinitya Kondapally
**GitHub Username:** skonda29
**Issue:** https://github.com/ICTU/quality-time/issues/11842
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: built the issue-scout, then patched its queries and two checks**

I built the issue-scout over my unit-1 skill, with seven `gh search
issues` queries, six drop conditions and four ordering keys in
`search-strategy.md`. I anchored every query with `--owner` to the 25
organizations in my profile's areas after the unscoped first version
returned 482 results led by same-day Hacktoberfest repos. My scan, run
by hand with `gh` because my `claude` login had expired, graded the top
5 of 422 unique issues and accepted only `jupyterlab/lumino#449`. I
patched the two checks that misread `nodejs/bluesky#49` and
`jupyterlab/jupyter-ai#1327`: `maintainer-active` now traces a bot
commit to the human who merged or reviewed it, and `scope-bounded` now
dates a design debate from when the question was raised.

**Unit 6: installed the voice guard, wrote repo memory, fixed a claim-rule clash**

The guard's wiring test passed only because the guard blocked `gh issue
comment --help` for having "no body the guard can read", which shows
the hook is wired but not how well it judges. My first claim rule
clashed with repro-check: the guard passed a claim with a draft-PR date
(exit 0) that repro-check rejected as a delivery date, then stopped the
version without a date (exit 2). I reworded the rule to ask for a rough
date for my next update on the thread, never for a PR, and claim
version 3 passed both the guard (exit 0) and repro-check (accept). My
40-line `CLAUDE.local.md`, kept out of git with `.git/info/exclude`,
came from CONTRIBUTING.md, AGENTS.md, the developer manual and one
$0.36 orientation run, which found that the collector lookup needs a
class named `DependencyTrackLicenses`.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I searched two ways. The first was my scout's scan: seven `gh search
issues` queries anchored with `--owner` to 25 organizations in my
profile's areas. The official CLI run on 2026-11-12 found 411 unique
issues and cut them to 37. It graded the top 5 and accepted two,
`storybookjs/addon-designs#268` and `jupyterlab/lumino#449`. Each came
with a caveat. On #268 a collaborator has "totally no idea if it's
still necessary" about the 2019 code. #449 has been silent since its
label was added on 2022-12-26. The second way was the course Issue
Selection site. I took 10 candidates from it and graded them in direct
mode: a Kiro agent ran my skill's files with `gh`, because my `claude`
login was still expired. It accepted 3 and rejected 7, and
`ICTU/quality-time#11842` "License metric" ranked first on fit. When I
graded my three picks through the CLI, it gave the same verdicts in the
same order. Every required and preferred check on #11842 passed. Its
scope evidence reads "Single user story 'License metric' with
acceptance criteria and tasks (data model metric, DependencyTrackLicense
collector, changelog); open questions explicitly listed under 'Out of
scope'; opened by MEMBER". Its note says "It's mid-size for a first
issue." The issue fits my profile. It's Python. It's a collector that
consumes a documented REST API, Dependency-Track's
`v1/component/project/{uuid}` and `v1/license`. Its tests run against
mocked responses. The lead maintainer, `fniessink`, already wrote down
what "done" looks like, which is the guidance my profile says I need.
It is also bigger than the work my profile prefers: documentation
fixes, small bug fixes and small features, in that order. That is part
of why I'm limiting my first PR to the issue's Tasks section. The
thread has 0 comments, no assignee and no linked PR, so nobody is there
to collide with. I want to learn to find my way around a codebase this
size. I also want to write tests that meet the repo's 100% line and
branch coverage bar. Claim comment: claim pending, 2026-11-12.

On a real wild search the scout fell short in ways I only saw by
running it. My first queries were unscoped language-wide sweeps. They
returned 482 issues led by same-day hobby repos and Hacktoberfest
farms, one titled "Hacktoberfest Task - Add new Japan Fact 2499".
`gh search issues` returns no star count or repo age to rank on, so I
moved the fix into the queries with the `--owner` anchors. Next,
`maintainer-active` rejected `nodejs/bluesky#49` because its last 5
commits were all `github-actions[bot]` (all 30 when I pulled more). Yet
a MEMBER had approved an outside contributor's PR there on 2026-08-11.
I patched the check to trace a bot commit to the human who merged or
reviewed it. The CLI hasn't tested that patch yet. On 2026-11-12, drop
condition 2 removed #49 as stale (last updated 2026-08-12) before
anything was graded. From the course list, `ICTU/quality-time#12823`
looked ideal, with the maintainer's exact replacement wording already
in the thread. Then the thread showed `est4ever` claiming it on
2026-10-22 and posting progress on 2026-10-26, plus an open PR #14940.
I fed #12823 in by hand, and the rubric's `not-claimed` check rejected
it. In the paid scan the same gap cost money. The free search stage
can't see claim comments or linked PRs. As a result,
`nodejs/doc-kit#1201` took one of the 5 grading slots before
`not-claimed` rejected it for its open PR #1204. This unit I also
replaced my `scope-bounded` row with the course's `scope-feasible` row.
My Unit 1 eval dropped from 20/20 to 19/20.
`excalidraw/excalidraw#11811`, a one-line feature wish with gold label
reject, now grades accept. I left that alone. Turning a feature wish
back into a reject would undo the rule the patch removes on purpose.

**Toolkit**

- Tool: issue-scout, run as `claude -p "issue-scout: run a scan"` and
  `claude -p "issue-scout: grade <3 issue URLs>"`. It uses my
  `search-strategy.md` (seven `--owner`-anchored queries, six drop
  conditions, four ordering keys) and my `rubric.md`. Before that I
  hand-graded 10 course-site candidates in direct mode.
- What it produced: the scan graded 5 of 411 unique issues (2 accept, 3
  reject). The grade run accepted all three of my picks in the same fit
  order as my hand grading. Every check on #11842 passed, with the note
  "It's mid-size for a first issue."
- Difficulties: the first scan attempt ended `error_during_execution` /
  `aborted_tools` after 33 turns and $0.92, with no read-out. My
  `--allowedTools` list had no `Write` or `mkdir`, and every script the
  model tried was denied. With the retry ($1.35), the scan cost about
  $2.28, not the $1 the course quotes. 4 of the 5 graded slots repeated
  the 2026-11-03 scan. The run never exercised my `maintainer-active`
  patch.
- What changed: I retried once with a wider allow-list. The public `gh`
  write commands (`issue comment`, `issue edit`, `pr create`, `pr
  comment`, `pr review`, `issue create`) were explicitly disallowed. I
  applied the `scope-feasible` patch and accepted the 19/20 eval. The
  next patch I'd make is an "already graded" URL list as a free drop
  condition. I haven't made it.

---

## Understanding the Issue

**Problem Description**

Quality-time can already read a product's dependencies from
Dependency-Track, but it can't watch their licenses. A product owner
who wants to know how many components ship under a copyleft license
has no metric for it. Dependency-Track already stores each component's
license. It also sorts licenses into groups ("Copyleft", "Weak
copyleft", "Permissive", "Non-commercial") and attributes such as
OSI-approved, FSF-Libre and Deprecated. This is a missing feature, not
a bug.

**Expected Behavior**

From the issue body (`gh issue view 11842 -R ICTU/quality-time`), users
can add a "licenses" metric with Dependency-Track as its source. It
counts the components with one or more of the configured licenses. The
acceptance criteria list seven parameters. Three are project names,
project versions, and "Only include latest project versions (yes/no)".
The other four are include/exclude bullets: for components, license
group, license attributes, and license name/SPDX identifier
(`licenseId` in the license endpoint). The Tasks section names no
parameters at all. It says only that "the collector filters the
components and adds the metadata". The issue doesn't say whether each
license bullet means one parameter or an include/exclude pair. The
CLI's "about 7 filter parameters" matches the seven bullets. Last, the
criteria want Quality-time to show, for each component, the project
name and version, component name, license name, license groups and
attributes. The component name should link to
`{hostname}/component/{uuid}` in Dependency-Track.

**Current Behavior**

On upstream `master` at `ef52a51` there is no licenses metric anywhere.
`grep -n '"licenses"'` on the shared data model's `metrics.py` finds
nothing. `ALL_DEPENDENCY_TRACK_METRICS` lists only `dependencies`,
`security_warnings`, `source_up_to_dateness` and `source_version`.
Asking the collector for a licenses class returns `None` and logs
"Couldn't find collector subclass for source dependency_track and
metric licenses". The Dependency-Track base class would also drop
license data today. Its `__slim_component` keeps only name, uuid,
directDependencies, repositoryMeta and version.

**Affected Components**

- `components/shared_code/src/shared_data_model/metrics.py`: the new
  `"licenses"` metric goes here. The `"dependencies"` metric at line
  107 is the one to copy.
- `components/shared_code/src/shared_data_model/sources/dependency_track.py`:
  add `licenses` to `ALL_DEPENDENCY_TRACK_METRICS` (line 18) and to the
  parameters' `metrics` lists. Add a `licenses` entry under `entities=`
  (line 122). The component filters already exist as
  `components_to_include` (line 87) and `components_to_ignore` (line
  92), along with the project name, version and latest-version
  parameters.
- `components/shared_code/src/shared_data_model/sources/quality_time.py`:
  the developer manual says a new metric's name must be added to
  `metric_type` there, or a shared_code test fails.
- `components/collector/src/source_collectors/dependency_track/`:
  `base.py` holds `DependencyTrackBase`, which handles auth, paging,
  project filters and component streaming. Its `__slim_component` at
  line 153 needs the license fields. `json_types.py` needs them too, in
  `DependencyTrackComponent`. `dependencies.py` is the closest template
  for the new collector.
- `components/collector/src/source_collectors/__init__.py`: the new
  class has to be imported next to the other Dependency-Track
  collectors (lines 38-41). `SourceCollector.get_subclass` matches
  class names against the lowercased `source_type + metric_type` with
  the underscores removed. For `dependency_track` + `licenses` that
  means a class named `DependencyTrackLicenses`, not the issue's
  `DependencyTrackLicense`.
- Tests: `components/collector/tests/source_collectors/dependency_track/`.
  `base_test.py` has the `DependencyTrackTestCase` fixtures, and
  `test_dependencies.py` is the model for the full `test_licenses.py`.
- `docs/src/changelog.md` for the changelog task.

**Toolkit**

- Tool: one scoped orientation run from the clone root, `claude -p
  "Read only components/collector and components/shared_code. Tell me
  where the Dependency-Track collectors live, the 2-4 files that
  matter, how they connect, and where their tests are. ..."`. Its
  answer went into my repo-memory file `CLAUDE.local.md`.
- What it produced: the file map above, in 10 turns, 32 seconds and
  about $0.36. It included the `__slim_component` gap and the
  `DependencyTrackLicenses` naming rule.
- Difficulties: it didn't tell me the Dependency-Track field names for
  component licenses or the shape of the `v1/license` response. I
  checked its line numbers with grep before trusting them.
- What changed: both unknowns are listed under "Not yet checked" in
  `CLAUDE.local.md`. I kept the class-name question out of my claim
  draft, so the claim asks one question. I saved it for the next
  comment or the PR.

---

## Reproduction Process

**Environment Setup**

I work on macOS 15.1.1 (build 24B91) on arm64. The tools are Python
3.14.2, Node v24.4.1, npm 11.4.2, uv 0.13.8, just 1.59.0, Docker
28.0.4, Docker Compose v2.34.0-desktop.1 and gh 2.86.0. I forked and
cloned with `gh repo fork ICTU/quality-time --clone=true
--remote=true`. That gave me https://github.com/skonda29/quality-time
as `origin` and `ICTU/quality-time` as `upstream`, on `master` at
`ef52a51e23205b0497e188f2ef25f30e841558ca`. The Python components need
nothing beyond `uv` and `just`. `cd components/shared_code && just
test` and `cd components/collector && just test` install their own
dependencies. Both passed with 100% coverage, the collector at 10850
statements and 1018 branches. `cd components/collector && just check`
passed all 12 checks.

The first challenge was that `uv` and `just` weren't installed. Every
`just` recipe shells out through `uv`, so nothing ran until `brew
install uv just`. The second was Node. The frontend's `package.json`
pins `"node": "26.13.0"`, the developer manual says "we currently use
Node.js v25", and I have v24.4.1. I didn't install Node 26, because the
frontend display is out of scope for my first PR. The frontend is not
built or tested here. A smaller one: `just` prints only one OK line per
recipe and hides test output unless something fails. To see counts I
ran `PYTHONPATH=src uv run python -m unittest` directly: 26 tests in
`test_dependencies.py`, 799 in the collector. I read coverage with
`COVERAGE_RCFILE=../../.coveragerc uv run coverage report`.

**Steps to Reproduce**

This is a feature request. Reproducing it means showing the feature is
missing on unmodified `master`. I run the existing `dependencies`
metric next to it as the contrast case.

1. Fork and clone as above. Check that `git rev-parse HEAD
   upstream/master` prints `ef52a51` for both and `git status
   --porcelain` prints nothing. Then run `brew install uv just`.
2. Grep the shared data model: `grep -n '"licenses"'
   components/shared_code/src/shared_data_model/metrics.py` and
   `grep -rn -i 'license'
   components/shared_code/src/shared_data_model/sources/dependency_track.py`.
3. In `components/shared_code`, run a short script with
   `PYTHONPATH=src uv run python`. It looks up `dependencies` and
   `licenses` in `DATA_MODEL.metrics` and in the Dependency-Track
   source's entities.
4. In `components/collector`, run a script that calls
   `SourceCollector.get_subclass('dependency_track', m)` for both
   metrics. Then run `grep -rn 'DependencyTrackLicense' components/`
   and `just test`.
5. Observed result: both greps in step 2 print nothing (exit 1).
   `dependencies` is True on all three data model checks, and
   `licenses` is False on all three. `get_subclass` returns
   `DependencyTrackDependencies` for `dependencies` and `None` for
   `licenses`. No `DependencyTrackLicense` exists. `just test` passes,
   because nothing tests a licenses metric yet.
6. The same gap as a failing test: on my fork's branch
   `repro/11842-license-metric`, run `cd components/collector && just
   test --coverage=no
   tests/source_collectors/dependency_track/test_licenses.py`. Both
   tests fail.

**Reproduction Evidence**

- **Commit showing reproduction:**
  https://github.com/skonda29/quality-time/commit/1c4e546cb78ec85e91cffecf5b99d87bb4db7dec.
  It adds one test file that asserts the data model has a `licenses`
  metric and that `get_subclass('dependency_track', 'licenses')`
  returns a class. Both assertions fail on `master` at `ef52a51`.
- **Screenshots/logs:** from my before-state report:

  ```text
  $ cd components/shared_code && PYTHONPATH=src uv run python /tmp/qt_dm.py
  'dependencies': in DATA_MODEL.metrics=True; dependency_track in metric sources=True; in dependency_track entities=True
  'licenses': in DATA_MODEL.metrics=False; dependency_track in metric sources=False; in dependency_track entities=False

  $ cd components/collector && PYTHONPATH=src uv run python /tmp/qt_col.py
  get_subclass('dependency_track', 'dependencies') -> <class 'source_collectors.dependency_track.dependencies.DependencyTrackDependencies'>
  Couldn't find collector subclass for source dependency_track and metric licenses
  get_subclass('dependency_track', 'licenses') -> None
  ```

  And from the test in the fork commit, trimmed:

  ```text
  $ cd components/collector && just test --coverage=no tests/source_collectors/dependency_track/test_licenses.py
  install-py-dependencies OK
  py-unit-test NOK
  FAIL: test_licenses_collector_registered (...)
  AssertionError: unexpectedly None
  FAIL: test_licenses_metric_in_data_model (...)
  AssertionError: 'licenses' not found in ['average_issue_lead_time', 'change_failure_rate', ...]
  Ran 2 tests in 0.001s
  FAILED (failures=2)
  ```

- **My findings:** the feature is missing exactly where the issue's
  Tasks section points. There is no metric in the data model, no
  Dependency-Track support for it, and no collector class. Two things
  surprised me. The collector lookup builds class names from the metric
  key, so the class has to be `DependencyTrackLicenses`, not the
  issue's `DependencyTrackLicense`. And `__slim_component` in `base.py`
  throws away every component field except name, uuid,
  directDependencies, repositoryMeta and version. The license fields
  have to be added there before a collector can see them. I haven't yet
  checked which Dependency-Track fields carry a component's licenses.

**Toolkit**

- Tool: repro-check, run three times in live mode from the clone root
  with `claude -p "repro-check: live mode, full package. ..."`. Each
  run graded my claim draft plus the before-state report. The
  allow-list was read-only, and the public `gh` write commands and
  `git push` were disallowed.
- What it produced: run 1 (about $0.47) rejected the package. Five of
  six required checks passed. `claim-specific-and-honest` failed on my
  sentence "I aim to have a draft PR up by around November 26.", which
  its rubric counts as a delivery date. Run 2 (about $0.39) accepted
  version 2, which ended "I'll post progress on this thread as I go."
  Run 3 (about $0.48) accepted version 3, which ends "I'll post an
  update here by around November 19." Runs 2 and 3 passed all six
  required checks and both preferred checks.
- Difficulties: my voice guard stopped version 2 (exit 2) under my rule
  "Claim a concrete plan with a rough date for the draft". Version 1
  had passed the guard and failed repro-check, so no wording could pass
  both. Runs 1 and 2 had no `uv` or `python` entry in the allow-list.
  They couldn't re-run my Python scripts and took the output from the
  report. Run 3 had both entries, but its one try at running Python sat
  inside a compound shell command, and the allow-list denied it.
  Compound commands were denied in every run (3, 4 and 2 denials). That
  is why run 1 wrongly said the skill has no `voice-guide.md`. The file
  exists, and the one command that would have read it was denied.
- What changed: I reworded the guide's claim rule to "Claim a concrete
  plan, and say roughly when my next update comes". It asks for a rough
  date for my next update on the thread, and never a delivery date for
  a PR, fix or merge. Version 3 passed the guard (exit 0) and run 3. It
  is the claim to post, and it isn't posted yet. Before posting I'd
  still cut the "Fork commit" section from the report. All three runs
  flagged it for exposing my local paths and username. I'd also add a
  sentence disclosing AI use, which my own `CLAUDE.local.md` asks for.

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

**2026-11-12**

Chose `ICTU/quality-time#11842`. Of the 10 course-site candidates I
graded, 3 were accepted, and #11842 ranked first on fit. Its thread is
empty: no comments, no assignee, no linked PR. The work is a Python
collector against a documented REST API, and the lead maintainer wrote
the acceptance criteria. The CLI grading agreed. The runner-up,
`KaotoIO/kaoto#2451`, has a closed-unmerged earlier attempt (PR
#2473). A maintainer warned that part of the path "is not fully
implemented yet". It also needs a Java 17+ build, which this machine
doesn't have.

**2026-11-12**

Scoped my first PR to the issue's Tasks section: the `licenses` metric
in the shared data model, the `DependencyTrackLicense` collector, and
the changelog. The acceptance criteria also ask for a per-component
display. The Tasks section doesn't list any frontend work, so I left
the display out. My claim draft asks `fniessink` whether it belongs in
this PR or a follow-up. The agent running my coursework steps made this
call, and I'm keeping it until the maintainer answers. I also counted
the parameters in the issue body myself. The Tasks section names none.
The acceptance criteria list seven, and four of them are
include/exclude: components, license group, license attributes, and
license name/SPDX id. Components include/exclude already exists in
`sources/dependency_track.py`. Whether each license bullet is one
parameter or a pair is a question for later.

**2026-11-12**

Installed the missing toolchain with `brew install uv just` (uv 0.13.8,
just 1.59.0). With those, `just test` in `shared_code` and `collector`
and `just check` in `collector` all pass on `master` at `ef52a51`. I
did not install Node 26.13.0 for the frontend, since the frontend is
out of scope for now.

**2026-11-12**

Wrong turn on the claim. My first draft ended "I aim to have a draft PR
up by around November 26." It passed my voice guard and failed
repro-check as a delivery date. The second draft ended "I'll post
progress on this thread as I go." It passed repro-check and failed my
voice guard's rule asking for a rough date. Nothing is posted.

**2026-11-12**

Settled the claim clash. With my go-ahead, the agent running my
coursework steps reworded my voice guide's claim rule. The rule now
asks for a rough date for my next update on the thread. It never
allows a delivery date for a PR, fix or merge. Claim version 3 ends
"I'll post an update here by around November 19." It passed the voice
guard (exit 0) and repro-check run 3 (accept). It isn't posted yet.

**2026-11-12**

Pushed a reproduction commit to my fork, on the branch
`repro/11842-license-metric`. It adds one file,
`components/collector/tests/source_collectors/dependency_track/test_licenses.py`,
with two tests. Both fail on `master`, with "'licenses' not found in
[...]" and "unexpectedly None". Unit 7 will grow this file into the
real tests for the collector.

**Toolkit**

- Tool: my repo-memory file `CLAUDE.local.md` in the clone, which
  starts with `@AGENTS.md`, plus the scoped orientation run that fed
  it.
- What it produced: a 40-line file in four sections. They cover the
  conventions from CONTRIBUTING.md, the subsystem my issue touches, the
  build and test commands that ran OK on 2026-11-12, and house style
  with the repo's AI policy ("none stated").
- Difficulties: no build command ran until I installed `uv` and
  `just`, which weren't on this Mac. The frontend's `engines.node` is
  26.13.0, and I have Node v24.4.1. The orientation run didn't cover
  the Dependency-Track license fields or the `v1/license` response.
- What changed: I installed uv 0.13.8 and just 1.59.0 with Homebrew
  and wrote them into the file as prerequisites. I skipped Node 26,
  because the frontend is out of scope for my first PR. The two
  unknowns sit under "Not yet checked" in the file.

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

- [ICTU/quality-time#11842](https://github.com/ICTU/quality-time/issues/11842):
  the acceptance criteria and Tasks section that set my plan and my
  first PR's scope.
- [Developer manual, `docs/src/development.md`](https://github.com/ICTU/quality-time/blob/ef52a51e23205b0497e188f2ef25f30e841558ca/docs/src/development.md):
  the prerequisites (uv, just, Node.js), the 100% line and branch
  coverage rule, and the step of adding a new metric's name to
  `metric_type`.
- [CONTRIBUTING.md](https://github.com/ICTU/quality-time/blob/ef52a51e23205b0497e188f2ef25f30e841558ca/CONTRIBUTING.md):
  comment on the issue before starting work, then fork, branch, and
  open the PR against `master`.
- [AGENTS.md](https://github.com/ICTU/quality-time/blob/ef52a51e23205b0497e188f2ef25f30e841558ca/AGENTS.md):
  the commit message rule, with `Fixes #NNNN.` in the body and never
  the subject.
