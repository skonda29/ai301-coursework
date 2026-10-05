# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

---

## Your identity upstream

**GitHub username**

skonda29

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5902202860

Hi, I'd like to pick this one up as my Unit 2 issue. I have not posted a reproduction yet; this comment is to say what I am doing and what I will post back.

What drew me to it: `api/routes/health.py` builds its Redis client from `settings.redis_host` and `settings.redis_port`, and `Settings` in `core/config.py` defines neither — it carries a single `redis_url` field. Reading the code, the `except Exception` around that probe looks like what turns the resulting `AttributeError` into `"redis": "unhealthy"` and a 503 even with Redis up — that is the part I want to confirm by running it rather than assert from the source. `pyproject.toml` also carries a mypy `attr-defined` baseline entry pointing at this file and naming this issue, which lines up with that reading.

Next steps for me, in order: set up the sandbox per `docs/SETUP.md` (Docker services, `make setup`), call `GET /health` with Redis running, and confirm whether the log shows `redis_health_check_failed` with an `AttributeError` for `redis_host` as the issue describes. I will post a reproduction report with my environment, the exact steps, and the log excerpt — including a control run showing whether Redis itself is reachable from the same interpreter via `settings.redis_url`, since that is what separates "Redis is down" from "the probe is reading a field that does not exist."

I am not promising a fix or a date, only the investigation and the report. If it turns out I cannot reproduce it, I will post that result with the transcript instead.

Two notes on process. I see several classmates are working this issue as their Unit 2 target; per the course's sandbox rules I am posting my own claim and will produce my own reproduction independently rather than piggybacking on theirs. And for disclosure: I am using an AI assistant to help organize my notes and draft this comment; I am running every step myself and I understand what I am reporting.

---

*Edited to correct this comment. As originally posted, the first sentence read "I have not fixed or reproduced it yet." That was not accurate at the time: I had already run the bug locally about an hour and a half earlier, though I had not yet produced the documented reproduction this comment goes on to promise. I would rather fix the sentence and say so than leave a claim standing that overstated how little I had done. The reproduction report is in a later comment on this thread, and the rest of this comment is unchanged.*

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5914769306

Reproduction report for #62. Reproduced: while Redis is up and reachable, `GET /health` reports `"redis": "unhealthy"` and the log shows `redis_health_check_failed` with `AttributeError: 'Settings' object has no attribute 'redis_host'`. The response status is 503, but see the note at the end: a second, unrelated defect in the same handler also marks `postgres` unhealthy, so the status code alone is not what identifies this bug — the Redis log line is.

First, a correction to my own thread history: my comment on 2026-09-24 said I was "close to completing" this issue. That overstated where I actually was — I had read the code but had not stood the project up or run anything. The claim comment above and this report are the accurate account.

**Environment**

- Repo: `codepath/pathreview-ai301-fa26-s3` at commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088` (`main`), run from my fork `skonda29/pathreview-ai301-fa26-s3`
- macOS 15.1.1 (build 24B91), arm64 (Apple Silicon)
- Python 3.12.11 (Homebrew) in `.venv`; fastapi 0.141.1, uvicorn 0.54.0, redis-py 8.1.0, pydantic 2.13.5, pydantic-settings 2.15.0, sqlalchemy 2.1.1
- Docker 28.0.4, Docker Compose v2.34.0-desktop.1; backing services from the repo's `docker-compose.yml`: `postgres:16-alpine` on 5433, `redis:7-alpine` on 6379, `chromadb/chroma:0.4.22` on 8001
- `.env` is `.env.example` copied unmodified, so `REDIS_URL=redis://localhost:6379/0`

One deviation to note: `python` is not on my PATH (only `python3`, which is 3.14.2 here), so `make setup`'s `python -m venv .venv` would have fallen through to 3.14. I pinned Python 3.12.11 on PATH as `python` for that one command so the venv was built on a supported interpreter (`requires-python = ">=3.11"`). Nothing else about the documented flow changed.

**Steps**

1. Forked and cloned, then followed `docs/SETUP.md`: `cp .env.example .env`, `docker compose up -d`, waited for `docker compose ps` to show `db` and `redis` healthy, then `make setup` (deps, `alembic upgrade head`, `scripts/seed_db.py`, `npm install`).
2. Started the backend the way `make run` does: `.venv/bin/uvicorn api.main:app --host 0.0.0.0 --port 8000`.
3. Confirmed Redis was actually up immediately before the request, then called the endpoint:

```
$ docker compose exec -T redis redis-cli ping
PONG

$ curl -s -w '\nHTTP %{http_code}\n' http://localhost:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-30T05:45:19.515904"}}
HTTP 503
```

4. Server log for that same request (`request_id` matches across the three lines):

```
2026-09-29 22:45:19 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=417df5c8-8dc0-47fd-8f38-5a5a6d33b4db
2026-09-29 22:45:19 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=417df5c8-8dc0-47fd-8f38-5a5a6d33b4db
2026-09-29 22:45:19 [debug    ] vector_db_health_check_passed  request_id=417df5c8-8dc0-47fd-8f38-5a5a6d33b4db
```

**Control run** — the same `Settings` object, same interpreter, same venv, to separate "Redis is down" from "the probe reads a field that does not exist":

```
$ .venv/bin/python
>>> import redis
>>> from core.config import settings
>>> settings.redis_url
'redis://localhost:6379/0'
>>> hasattr(settings, 'redis_host')
False
>>> redis.Redis.from_url(settings.redis_url).ping()
True
>>> settings.redis_host
AttributeError: 'Settings' object has no attribute 'redis_host'
```

Redis answers `True` through the field that exists, and the field the probe actually reads raises. So the 503 is not Redis being unreachable.

**Expected:** with Redis running and reachable, `/health` reports `"redis": "healthy"` and returns 200.

**Actual:** `/health` returns 503 with `"redis": "unhealthy"`, and the log records `redis_health_check_failed` with `AttributeError: 'Settings' object has no attribute 'redis_host'` — the behavior the issue describes. The `except Exception` around the probe in `api/routes/health.py` catches the `AttributeError` and reports it as a dependency outage, which is the part I said I would confirm by running rather than assert from the source; the log line above is that confirmation.

**One thing I found that is not this issue.** In the same response, `postgres` also reports `"unhealthy"`, for an unrelated reason: `await db.execute("SELECT 1")` passes a bare string where SQLAlchemy 2.x requires `text(...)`, so that probe raises `Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')`. That is a different defect in the same handler and I am not claiming it here — flagging it so nobody reads the `postgres: unhealthy` line in my output as part of #62, and so it does not get silently folded into a fix for this one. Worth noting that the 503 status alone therefore does not distinguish the two; the log lines do.

**Scope check:** I did not test whether Redis reports healthy once the probe uses `redis_url`, beyond the control ping above — that belongs to the fix, not the reproduction.

Disclosure: I used an AI assistant to help organize this report. I ran every command above myself on my own machine, the transcripts are my own output, and I understand what I am reporting.

## Eval iterations

**Run history**

Before spending anything on the harness I hand-graded `calib-02` against my revised rubric as a free warm-up. It failed `env-recorded` ("on all my machines" is not a version or an OS), `steps-rerunnable` (no steps at all, only assertion), and `artifact-shows-issue-behavior` (no artifact, only "Same here!!"), and `conventions-and-disclosure` passed because Joplin states no AI policy. Verdict reject, agreeing with the gold label. That exercise changed nothing in the rubric but it did expose one soft spot in wording I describe under Trade-offs.

Harness runs, in order:

1. **Targeted smoke run** — `--only pkg-20,pkg-16,pkg-09,calib-04 --include-calibration`. **3/3 scored items agreeing** (`clear-accept 1/1`, `disclosure 1/1`, `wrong-target 1/1`), plus `calib-04` also agreeing unscored. I picked these four deliberately rather than `--limit 3`: they are the four judgments I thought most likely to be wrong — the single disclosure package, the silent-version-deviation reject, a cannot-reproduce that must still be an accept, and the no-environment borderline. Spending about $0.80 to test my four riskiest calls before a $4 full run was the whole point.
2. **Full run** — **20/20 scored items**, `clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`, bar PASS.
3. **Confirming full run with `--save-run`** — graded 19 items and **errored on `pkg-07`** with `bad JSON: Invalid \escape: line 6 column 150 (char 579)`. The 19 it did grade all agreed, but the harness correctly refused to write `eval-run.txt` because an errored item makes the run incomplete. This was not a rubric disagreement: the grader's own output was malformed, not its judgment.
4. **`--only pkg-07`** — **1/1 agreeing**, verdict `accept` matching gold. That confirmed the previous failure was a transient output-formatting fault rather than something in my rubric that `pkg-07` trips, so I did not revise anything in response to it.
5. **Confirming full run with `--save-run eval-run.txt`** — **20/20 scored items**, every category matched, bar PASS. This is the run in the committed `eval-run.txt`, whose agreement line reads `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

No rubric revision happened between run 1 and run 5. Because nothing was ever loosened, there was no revision for which canaries were owed; the one re-run I did (step 4) was to diagnose a harness error, not to re-test a changed check.

**Package analysis**

**`pkg-20`** (`ghostty-org/ghostty#13604`). My rubric decided **reject**; the gold label is also **reject**.

What makes this package worth examining is that it is the best reproduction in the set and still must be held. My run's per-check output records seven of eight checks passing:

- `env-recorded` — pass: `"ghostty 1.3.1 (release build, Fedora 42 RPM), GTK backend, GNOME 48 (Wayland), dark system scheme" — version + platform + build profile stated`
- `steps-rerunnable` — pass: `config file content, exact launch command, query command, and control-run command all given inline`
- `artifact-shows-issue-behavior` — pass: `single-theme run returns ^[[?997;2n (light) matching issue's reported wrong-mode symptom`
- `target-configuration-faithful` — pass: `ran latest release 1.3.1 on an issue that is still open/unfixed; claim names it's not debug-build-specific`
- `claim-specific-and-honest` — pass: `claim names mode-2031, non-conditional theme, draft patch; next step is 'test the draft patch ... report back before opening a PR' — no fix/date promised`
- `control-or-contrast-run` — pass, `next-step-grounded` — pass

The single failure is `conventions-and-disclosure`: `policy requires disclosing 'all AI usage in any form ... stating the tool used and extent'; neither the claim comment nor the repro report contains any AI disclosure`. Because that check is `required` and my verdict rule rejects on any required fail, the package is held.

My rubric read it that way because of one sentence I put at the front of that check's pass condition: "Treat the package as **AI-assisted work**, because it is; so a stated disclosure duty always applies." Without that instruction, a grader has an easy escape — it cannot observe whether AI was used, so it can call the duty inapplicable and pass the check. That escape is exactly what the one-package `disclosure` category exists to punish, and it is why the category floor has teeth: proof quality cannot buy this miss back, since `pkg-20`'s proof is flawless.

The second half of why it read correctly is the policy taxonomy in my evidence guide, which sorts contribution policies into four kinds and says what each one obliges. Ghostty requires disclosure of all AI usage in any form, so silence fails. The nearby packages do not: `pkg-07` (p5.js) has a conditional assistive-use policy and its comment discloses anyway; `pkg-03` (ripgrep) requires comments be written by humans in their own words, which is an own-words clause and not a disclosure clause, so its human-voiced comment passes without an AI sentence; `pkg-05` (conda) and `pkg-12` (prettier) carry responsibility-and-understanding clauses with no disclosure ask; and the thirteen packages with no AI policy owe nothing, because silence is not a duty. `pkg-09` (fd) is the sharpest case: its policy requires disclosure in pull requests and then says outright that there is no disclosure ask for issue comments. A check that looked for the word "AI" in a policy and demanded a disclosure sentence whenever it found one would wrongly reject `pkg-03`, `pkg-05`, `pkg-09`, and `pkg-12` — four of the eight clear accepts. Reading the wording for what it actually obliges, rather than reacting to the topic, is what lets one check catch `pkg-20` without taking half the accepts down with it.

**Check rationale**

Quoted from the `rubric.md` uploaded to `tools/repro-check/` (sha256 `eab7ee3675513682`, the same fingerprint recorded in the header of the committed `eval-run.txt`):

> | target-configuration-faithful | The configuration the report says it ran (from its environment record and steps) compared against the versions and conditions the **issue and its thread** say the behavior occurs on, plus any difference the report itself names and what it says the difference means. | **Pass** if **either** (1) the recorded configuration is one the issue or thread **implicates** — the version the issue targets, a later release of the same line, or the platform the issue names when the issue is platform-specific; **or** (2) the report **explicitly names the difference** between what it ran and what the issue targets, and says what that difference means for its result ("filed against 13.0.0, unchanged on 15.2.0", "the report is macOS + fish, mine is Linux + zsh, which is likely why I could not trigger it"). **Fail** if: (a) the report ran a configuration the issue does **not** implicate — most often an **older version than the one the issue confirms the bug on** — and never names the deviation, so the shown behavior is that other configuration's behavior rather than evidence about the report; (b) the report **generalizes its result to a configuration it never ran**, or asserts its result holds on a build or channel the maintainers have said they could not reproduce on, without having run that one. Treat an unnamed deviation as failing even when the artifact looks like the reported symptom: a silent deviation is the failure. | required |

Why it reads this way: this check exists only because of a problem I could not solve inside the artifact check. My first design had a single check asking whether the artifact shows the issue's behavior, and `pkg-16` (pandas) breaks it. That report runs pandas 1.5.3 against an issue the reporter confirmed on the latest release and on `main`, and the traceback it shows — `ValueError: Length of new names must be 1, got 3` — genuinely is a crash from `rename_axis` with a tuple. Read as an artifact alone, it looks like the reported symptom, so a combined check either has to pass `pkg-16` or be written so suspiciously that it starts failing honest reports. Splitting the question in two fixed it: the artifact check asks whether the right *thing* happened, and this check asks whether it happened in a configuration that tells us anything about the issue. `pkg-16` passes the first and fails the second.

The final clause, "Treat an unnamed deviation as failing even when the artifact looks like the reported symptom: a silent deviation is the failure," is the sentence doing that work, and I added it after realising a grader would otherwise resolve the tension in `pkg-16`'s favour — the artifact is right there, and the version line is easy to skim past.

The branch in condition (2) is what I chose over the stricter rule I first considered, which was to require the recorded configuration to match the issue's target. That stricter version would have rejected four of the eight clear accepts: `pkg-03` runs ripgrep 15.2.0 on an issue filed against 13.0.0, `pkg-11` runs yq on macOS where the issue says linux, `pkg-07` runs p5.js 1.11.7 against an issue filed on 1.9.4/1.10.0, and `pkg-12` runs prettier 3.9.6 against an issue filed on 3.8.4. Every one of those states its delta in the report. That is the behavior worth rewarding rather than punishing, so the check grades *whether the deviation was disclosed*, not whether one occurred — which also makes it the check that most directly taught me how to write my own report, where the one deviation I had (pinning Python 3.12 because `python` is not on my PATH) is named in place.

What I rejected in favour of this: a version-distance threshold, something like "fail if more than one minor release behind the issue's target." I had used numeric thresholds in Unit 1's rubric and they worked well there, but here a number measures the wrong thing. `pkg-03`'s two-major-version gap is fine because it is disclosed and forward, while a single silent patch-version slip backwards would not be. The honest signal is disclosure, not distance, and no threshold can express that.

**Trade-offs**

The cost of this rubric is concentrated in one place: `artifact-shows-issue-behavior` carries two pass branches, and the second one — an honest cannot-reproduce counts as a pass — is deliberately a hole in a check whose whole job is to demand that the artifact show the bug. It has to be there. `pkg-09` (fd) and `pkg-10` (starship) are both gold accepts in which the reporter never once produced the symptom; a check that only recognised branch (A) would reject both and cost me two of the eight clear accepts. But branch (B) is doing something structurally weaker than branch (A): branch (A) can be checked against the issue's own error text, while branch (B) asks whether a *negative* result was honestly obtained, and the evidence for that is mostly the report's own account of itself. What stops it from passing everything is that it still demands the artifact of the real attempt. That is the line separating `pkg-09` and `pkg-10` from `pkg-04`, `pkg-13`, and `pkg-15`, which are also reports with no symptom shown — the difference is that `pkg-09` shows the marker-order output it got instead, and `pkg-15` shows nothing at all behind "I verified this race condition."

The case I accept it will miss follows directly: a package that fabricates a plausible-looking cannot-reproduce transcript, names a credible environment difference, and never actually ran anything. Nothing in branch (B) can catch that. The eval set does not contain one, so the run gives me no evidence either way, and it would be dishonest to claim the check is sound against a case it was never tested on. In live mode the gap is smaller, because the transcript is mine and I know whether I ran it.

I also want to record a trade-off my hand-grading of `calib-02` exposed, because it cost nothing to find and would have cost a run to find otherwise. My `claim-specific-and-honest` check requires three things of a claim, and `calib-02`'s comment ("+1!! ... can't believe this has been around since the other issue and never got fixed. claiming this one, someone has to do it") is genuinely arguable on the first of them: it does reference the earlier linked report, which is specific to this issue, while being pure boilerplate in every other respect. Two reasonable graders could split on that sub-condition. The reason it does not matter is the check's AND structure — the comment states no next step the author controls, so sub-condition (2) fails and the check fails regardless of how (1) is read. The structure absorbs the ambiguity rather than depending on resolving it, which is why I left the wording alone instead of trying to define "specific" more tightly and risking a change that would flip one of the eight clear accepts.

Finally, nothing changed anywhere else, and here is how I know. No check was ever revised between the smoke run and the final run, so no package's result could have moved; the only re-run I did (`--only pkg-07`) was diagnosing a malformed-JSON harness error, and it returned the same `accept` the first full run had produced. The two full runs that completed agree with each other item for item, all 20 of them, and with the targeted smoke run on the four packages they overlap. Because nothing was loosened, no canary was owed — the `disclosure` canary the eval README warns about matters only when a revision could flip the single `pkg-20` package, and my `conventions-and-disclosure` check is the same text in the committed `rubric.md` that it was in the smoke run, as the matching sha256 fingerprints in `eval-run.txt` confirm.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
