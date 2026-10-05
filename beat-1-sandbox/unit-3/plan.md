# Plan: issue #62 — health check reads `settings.redis_host`, which does not exist on `Settings`

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62
Reproduction: my repro comment on that issue,
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5914769306

## Diagnosis

The Redis probe in `api/routes/health.py` builds its client from two
fields that `Settings` does not define. `core/config.py` declares a
single `redis_url: str = Field(default="redis://localhost:6379/0")`
and no `redis_host` or `redis_port`. Reading either attribute raises
`AttributeError`, the probe's `except Exception` catches it, and the
handler reports Redis as a downed dependency.

This is grounded in the reproduction I posted, not inferred from the
source. With Redis verified up immediately before the request:

```
$ docker compose exec -T redis redis-cli ping
PONG

$ curl -s -w '\nHTTP %{http_code}\n' http://localhost:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-30T05:45:19.515904"}}
HTTP 503
```

and the server log for that same request (`request_id` matches):

```
2026-09-29 22:45:19 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=417df5c8-8dc0-47fd-8f38-5a5a6d33b4db
```

The control run in that report is what rules out "Redis is simply
unreachable" as the cause. Same interpreter, same venv, same
`Settings` object:

```
>>> redis.Redis.from_url(settings.redis_url).ping()
True
>>> settings.redis_host
AttributeError: 'Settings' object has no attribute 'redis_host'
```

Redis answers through the field that exists, and the field the probe
actually reads raises. So the defect is the attribute read, and the
fix belongs at the client construction rather than anywhere in the
Redis setup or the connection handling.

`pyproject.toml` corroborates this independently: the mypy baseline
carries `api/routes/health.py attr-defined` with the comment
"issue #62 (`settings.redis_host` does not exist on Settings)". mypy
has been catching this statically the whole time; the suppression is
what kept it quiet.

## Scope

**In scope, one bounded change:** construct the Redis client in
`api/routes/health.py` from `settings.redis_url`, the field that
exists, so the probe reports Redis's actual reachability. Plus the two
pieces of bookkeeping the repo's own CONTRIBUTING requires when a
seeded bug is fixed: drop the now-obsolete `attr-defined` entry from
the mypy baseline, and add the regression test the change needs.

**Not in scope:**

- **The `postgres` probe's failure in the same handler.** My
  reproduction showed `postgres` also reporting `"unhealthy"`, from
  `await db.execute("SELECT 1")` passing a bare string where
  SQLAlchemy 2.x requires `text(...)`. That is issue #61, filed
  separately, and the mypy baseline comment names it as "related #61".
  It sits four lines from my change and I am deliberately leaving it
  alone: fixing it here would make this diff two unrelated defects and
  would quietly close someone else's issue.
- **The `except Exception` swallowing pattern itself.** The handler
  catches bare `Exception` around each probe, which is what turned a
  programming error into a reported outage. Narrowing those handlers
  so a missing attribute surfaces as a 500 rather than a false
  dependency failure is a real improvement and a real behavior change,
  and it is not what this issue reports. It belongs in its own issue.
- **The `call-overload` and `index` mypy suppressions** for this
  module. They are not this defect, and `warn_unused_configs` will say
  so if my change makes them obsolete.
- Any change to `Settings`. Adding `redis_host`/`redis_port` fields to
  match the probe would be the other direction of fix; the issue is
  explicit that `redis_url` "is what the probe should use instead", so
  the probe moves, not the config.

## Files

- `api/routes/health.py` — the Redis client construction inside
  `health_check()`, the one site that reads the missing attributes.
- `pyproject.toml` — the `[[tool.mypy.overrides]]` block for
  `module = "api.routes.health"`: remove `"attr-defined"` from
  `disable_error_code`, leaving the other two codes alone.
- `tests/unit/test_health.py` — new file. The repo has no health tests
  today, so the regression case needs one.

## Approach

1. In `health_check()`, replace the host/port construction

   ```python
   r = redis.Redis(
       host=settings.redis_host,
       port=settings.redis_port,
       db=0,
       decode_responses=True,
   )
   ```

   with a URL-based client, `redis.Redis.from_url(settings.redis_url,
   decode_responses=True)`. `from_url` parses host, port and database
   out of the URL, so the `db=0` argument goes away with it: the
   default `.env` value `redis://localhost:6379/0` already selects
   database 0, and hard-coding `db=0` alongside a URL would override a
   deployment that pointed `REDIS_URL` at a different database.

2. Remove `"attr-defined"` from the `api.routes.health` mypy override
   and confirm `make typecheck` is clean, which is the static proof
   that no unresolved attribute read is left in the file.

3. Add `tests/unit/test_health.py` with two cases that run without
   Docker, by patching the Redis constructor: one asserting the probe
   reports `"redis": "healthy"` and that it was built from
   `settings.redis_url`, and one asserting a connection failure still
   reports `"redis": "unhealthy"`, so the fix does not paper over a
   genuine outage.

4. Re-run the reproduction from the issue against the running app, and
   record before and after.

## Test plan

This is the reproduction from my repro comment, re-run against the
change. The observable is the Redis field and the log line, not the
HTTP status — see the note below, which matters for reading the
result honestly.

**Before** (recorded in the repro comment, quoted above): with Redis
up, `GET /health` reports `"redis":"unhealthy"` and the log carries
`redis_health_check_failed error="'Settings' object has no attribute
'redis_host'"`.

**After**, same steps (`docker compose up -d`, confirm
`redis-cli ping` returns `PONG`, start uvicorn, `curl /health`):

1. The response body reports `"redis":"healthy"`.
2. The log line `redis_health_check_failed` is gone, replaced by
   `redis_health_check_passed`. No `AttributeError` for `redis_host`
   appears anywhere in the log.
3. Stopping Redis (`docker compose stop redis`) and re-requesting
   returns `"redis":"unhealthy"` again, now for the right reason — a
   connection error in the log rather than an `AttributeError`. This
   is the control that shows the fix reports reachability instead of
   always passing.
4. `tests/unit/test_health.py` passes under `make test-unit`, and
   `make typecheck` is clean with the `attr-defined` suppression
   removed.

**The HTTP status will still be 503 after this fix, and that is
expected.** `health_check` sets the top-level `status` to `unhealthy`
if *any* probe fails, and the `postgres` probe still fails on issue
#61. So a 200 is not the success criterion for this change and
treating it as one would hide the result. The criterion is the
`redis` field flipping to `healthy` and the `AttributeError` log line
disappearing. The response becomes 200 only once #61 is also fixed.

## Risks and unknowns

- **`decode_responses=True` must be preserved.** The original client
  set it and `from_url` does not imply it, so dropping it would change
  the type of everything `ping()`-adjacent returns. The probe only
  calls `ping()`, which returns a bool either way, so nothing in this
  handler depends on it — but I am keeping the flag rather than
  removing it, because the handler is not the only possible future
  caller of this pattern and silently changing it is the kind of thing
  that bites later.
- **A `REDIS_URL` with an unusual scheme.** `from_url` accepts
  `redis://`, `rediss://` and `unix://`. The committed `.env.example`
  uses `redis://`, which I tested. I have not tested TLS or a unix
  socket, and I am not adding handling for them — `from_url` is the
  library's own parser and is the right place for that concern to
  live.
- **Removing the `attr-defined` suppression could surface a finding I
  have not seen**, if another attribute read in the file is also
  unresolved. I will run `make typecheck` before committing; if
  something unrelated to #62 appears, I will restore the suppression
  and say so here rather than widen the change to chase it.
- **Unknown I am not resolving:** whether the seeded-bug convention
  expects the `call-overload` and `index` codes to come off this
  module too. The CONTRIBUTING note ties only `attr-defined` to #62,
  so I am leaving the other two and will raise the question in the PR
  rather than guess.

## Deviations

The change itself landed as planned: one edit at the client
construction in `api/routes/health.py`, `"attr-defined"` removed from
the `api.routes.health` mypy override, and a new
`tests/unit/test_health.py`. Nothing in the scope section moved — #61,
the `except Exception` pattern, `Settings`, and the `call-overload`
and `index` suppressions are all untouched, and the branch contains no
work the plan did not describe. Four things are worth recording.

**1. The full `make typecheck` cannot run in my environment, so I
verified the plan's claim a narrower way.** `mypy api/ core/
ingestion/ rag/ agent/ safety/` stops with
`numpy/__init__.pyi:737: error: Type statement is only supported in
Python 3.12 and greater [syntax]`, and reports "errors prevented
further checking". I confirmed this is not mine: I stashed all my
changes, re-ran it against pristine `main`, and got the identical
error, so it predates the branch and is an artifact of this venv's
mypy-and-numpy pairing rather than anything in the diff. What the plan
actually needed to establish was that no unresolved attribute read is
left in the file once the suppression is gone, and
`mypy api/routes/health.py` does establish that: it reports `Success:
no issues found in 1 source file` with `"attr-defined"` removed. CI
runs `make typecheck` on its own toolchain, so I will watch that job on
the PR rather than assume my local result generalizes. One more
data point arrived at commit time: the repo's `pre-commit` hooks
installed their own pinned environments and ran `ruff`, `black` and
`mypy` over the staged files, and all three passed — so mypy is clean
on a toolchain that is not my venv, which is the reassurance my local
numpy failure could not give me.

**2. `ruff` required a style change to the test I had not anticipated.**
My first version of the unreachable-Redis test nested
`with patch(...)` and `with pytest.raises(...)`, which trips `SIM117`
("Combine `with` statements"). I combined them into one parenthesized
`with`. This is a lint fix to satisfy `make lint`, not a change of
approach, and the assertions are identical. `ruff check .` and
`black --check` are both clean on the two files I touched.

**3. I shipped three tests rather than the two the plan described.**
The plan named a healthy case and an unreachable case. I added a third,
`test_settings_has_no_redis_host_or_port`, asserting that
`Settings` still defines neither attribute. It pins the defect's root
condition, so if someone later adds those fields the test fails and
the probe gets revisited deliberately instead of by accident. Small
addition, same scope, and I would rather note it than let the diff
quietly differ from the posted plan.

**4. A judgment call I am flagging rather than deciding alone.** The
`NOTE` comment block in `pyproject.toml` still reads
`api/routes/health.py  attr-defined -> issue #62 (settings.redis_host
does not exist on Settings) and related #61`, even though the
`attr-defined` entry it describes is now gone. I left the comment in
place: CONTRIBUTING says to remove "its entry here", which I read as
the `disable_error_code` entry, and that same sentence also carries
the only in-repo pointer to #61, which is still open. Deleting the
whole line would lose that. Updating the prose instead felt like
editing documentation the maintainers may want to word themselves, so
I am raising it in the PR instead of guessing.

**Verification actually run, with output recorded.** `GET /health`
with Redis up now reports `"redis":"healthy"` and logs
`redis_health_check_passed`; the `AttributeError` line is gone. With
Redis stopped, it reports `"redis":"unhealthy"` and logs
`Error 61 connecting to localhost:6379. Connection refused.` — a real
connection failure rather than a missing attribute, which is the
control showing the probe reports reachability instead of always
passing. The HTTP status is still 503 in both cases because the
`postgres` probe fails on #61, exactly as the plan said to expect;
judging this fix by the status code rather than the `redis` field would
have hidden the result. Full before/after transcripts are in
`beat-1-sandbox/unit-3/plan-and-implement.md` under Evidence.
