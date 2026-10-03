# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

S-A-Adit

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5970271888

**Cause:** `health_check()`'s Redis block built its client from `settings.redis_host` / `settings.redis_port`, but `Settings` (`core/config.py`) only defines `redis_url`. The `AttributeError` that raised was caught by the surrounding `except Exception` and reported as `dependencies.redis: "unhealthy"` — before any actual connection to Redis was attempted, which is exactly what my repro showed (`redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"`, regardless of whether Redis is reachable).

**Fix:** built the client with `redis.Redis.from_url(settings.redis_url, decode_responses=True)` instead — a one-line change to the constructor call, nothing else in that block.

**Not touching:** the `postgres: "unhealthy"` result in the same response — that's a separate SQLAlchemy 2.x issue (raw `"SELECT 1"` instead of `sqlalchemy.text(...)`), already tracked as #61. Kept this fix to the Redis block only.

**Test:** added `tests/unit/test_health.py` — this issue doesn't have a pre-seeded test the way some others in the tracker do. One case patches a reachable Redis and asserts `dependencies.redis == "healthy"` (confirmed it fails on the original code and passes on the fix, by actually reverting and restoring it); a second case patches a connection failure and confirms the probe still correctly reports `"unhealthy"` on a real failure, not just unconditionally "healthy" now. Also re-ran my repro steps from above against the fix: `dependencies.redis` still reads `"unhealthy"` in this environment (no Docker here, Redis isn't running), but the log line changed from the `AttributeError` above to a genuine `Error 10061 connecting to localhost:6379` connection refusal — the bug itself is gone; what's left is an honest "Redis isn't up," which is correct.

On the open question from my plan: the `pyproject.toml` mypy override for this module suppressed three error codes. I checked locally with the override cleared — `attr-defined` is gone (this fix), `call-overload` wasn't actually firing at all, and `index` is 7 pre-existing errors unrelated to this bug (`health_status`'s untyped dict literal). Removed `attr-defined` and `call-overload` from the override, kept `index`.

Branch: `fix/62-redis-health-url`.

---

## Your branch

**Branch**

fix/62-redis-health-url

**Evidence**

The Unit 2 reproduction ran against the real code directly (`TestClient` against
the actual `api.main:app`, and a live `uvicorn` server hit with `curl` — no
stand-in script), so it re-runs unchanged against the built fix. Same
environment as Unit 2: Windows 11, Python 3.12.5, Postgres reachable
natively at `localhost:5432` (no Docker on this machine), Redis not running.

**Check — `TestClient` call against `api.main:app`**

Command (same before and after):
```
.venv/Scripts/python.exe -c "
from fastapi.testclient import TestClient
from api.main import app

client = TestClient(app)
resp = client.get('/health')
print('status:', resp.status_code)
print('body:', resp.json())
"
```

Before (Unit 2, posted at
https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5851153832):
```
2026-09-26 19:51:07 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=75259b93-...
2026-09-26 19:51:07 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=75259b93-...
2026-09-26 19:51:07 [debug    ] vector_db_health_check_passed  request_id=75259b93-...
status: 503
body: {'detail': {'status': 'unhealthy', 'dependencies': {'postgres': 'unhealthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}, 'safety_events_last_hour': 0, 'timestamp': '2026-09-26T23:51:07.039389'}}
```

After (against the fix, on `fix/62-redis-health-url`):
```
2026-10-03 00:28:15 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=1d706167-eda0-4fb5-aab6-06222a037cb4
2026-10-03 00:28:19 [error    ] redis_health_check_failed      error='Error 10061 connecting to localhost:6379. No connection could be made because the target machine actively refused it.' request_id=1d706167-eda0-4fb5-aab6-06222a037cb4
2026-10-03 00:28:19 [debug    ] vector_db_health_check_passed  request_id=1d706167-eda0-4fb5-aab6-06222a037cb4
status: 503
body: {'detail': {'status': 'unhealthy', 'dependencies': {'postgres': 'unhealthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}, 'safety_events_last_hour': 0, 'timestamp': '2026-10-03T04:28:15.203212'}}
```

`dependencies.redis` still reads `"unhealthy"` in both runs, but the *reason*
changed: before, `AttributeError: 'Settings' object has no attribute
'redis_host'` — the bug itself, firing before any connection attempt; after,
`Error 10061 connecting to localhost:6379` — a genuine connection refusal,
because Redis genuinely isn't running in this no-Docker environment, not
because of the fixed bug.

**Check — the new unit test, run against the fix and against the original code**

Since no live check here can show the actual "healthy" outcome (no Docker, no
Redis running), this is the check that demonstrates it, with a verified
before/after:

Command (same before and after): `.venv/Scripts/python.exe -m pytest tests/unit/test_health.py -v`

Before (`health.py` reverted to the pre-fix version via `git checkout HEAD~1 -- api/routes/health.py`):
```
tests/unit/test_health.py::TestHealthCheckRedisProbe::test_redis_reachable_reports_healthy FAILED
tests/unit/test_health.py::TestHealthCheckRedisProbe::test_redis_unreachable_still_reports_unhealthy PASSED
2026-10-03 00:29:50 [error] redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"
FAILED tests/unit/test_health.py::TestHealthCheckRedisProbe::test_redis_reachable_reports_healthy
1 failed, 1 passed, 3 warnings in 2.56s
```

After (fix restored):
```
tests/unit/test_health.py::TestHealthCheckRedisProbe::test_redis_reachable_reports_healthy PASSED
tests/unit/test_health.py::TestHealthCheckRedisProbe::test_redis_unreachable_still_reports_unhealthy PASSED
2 passed, 3 warnings in 2.42s
```

`test_redis_unreachable_still_reports_unhealthy` passes both before and after
by design — it's the control confirming the fix doesn't make the probe report
`"healthy"` unconditionally; a genuine Redis connection failure must still be
caught and reported. Full commands and output (plus the isolated `Settings`
attribute check and the live-`uvicorn` run) are in `evidence.md`, kept
alongside `plan.md` in the fork's working directory but out of the commit
itself, same as `plan.md` and `comment.md`.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Only one full run was done: `agreement: 19/20 scored items  (bar: 18/20: PASS)`.
This is a single-run answer — no intermediate `--only` iterations preceded it
in this working session.

**Package analysis**

`pkg-20` (ghostty-org/ghostty#11261, "crash: capacity changes during print
with managed memory can be unsafe"). My rubric's verdict is `accept`; the
gold label is `reject` (category `thread-convention`).

My rubric disagrees with gold here, and the reason is a real gap, not a
misread: the repo-facts block states a strict policy — "All AI usage in any
form must be disclosed, stating the tool used and the extent of the
assistance; the human in the loop must fully understand the work;
AI-assisted issues and comments must be reviewed and edited by a human
before submission" — and the candidate plan comment never discloses any AI
assistance at all, despite that requirement. That's exactly the
`thread-convention` failure family the rubric's own header comment names
("the comment ignores what the thread or the repo's stated conventions
ask"). But `rubric.md`'s checks table only has three rows: Diagnosis, Scope,
and Test. None of them reads the repo's contribution policy or the plan
comment's compliance with it — Diagnosis passes (the cause matches the
repro), Scope passes (the generation-counter change is coherent with the
diagnosed cause), and Test passes (`zig build test` plus the fuzz corpus
re-run is a concrete, falsifiable check). All three pass on their own
narrow terms, so the verdict is `accept`, even though the comment itself
would be rejected on this repo for ignoring its disclosure policy. The gap
is coverage, not a wrong grade on an existing check.

**Check rationale**

From `rubric.md` as uploaded to `tools/plan-check/`, the `Scope` row:

> Pass if every change the plan proposes is connected to fixing the
> diagnosed cause. Fail if the plan includes adjacent information or
> proposed changes that are not connected to the issue — even a
> reasonable-sounding proposal fails this check if it is not coherent with
> the diagnosed cause.

This wording is deliberately strict about *connection to the cause* rather
than about *reasonableness* in general — a plan can propose something
sensible and still fail this check if it doesn't trace back to the
diagnosed bug. That is what the `scope-creep` category in this eval set is
built around, and my rubric reads all four of those packages (`pkg-06`,
`pkg-12`, `pkg-15`, `pkg-19`) correctly as `reject`. I felt this strictness
firsthand building my own plan for #62: the Postgres `"unhealthy"` result
sits in the exact same function and would have been an easy, reasonable-
sounding thing to "clean up while I'm in there," and this check's wording is
why my own plan explicitly named it `Out:` and tied the exclusion back to it
being a separate, already-tracked issue (#61) rather than just leaving it
unaddressed.

**Trade-offs**

The `pkg-20` disagreement above is this check's own trade-off made visible:
`Scope` (and `Diagnosis` and `Test`) only ever asks whether the plan is
internally coherent with the diagnosed cause — it has no opinion on whether
the plan comment honors the repo's own stated conventions (AI-disclosure,
templates, review-bandwidth notes). That is a real case this check's
current form will miss, not a hypothetical one: the rubric's own header
comment lists "the comment ignores what the thread or the repo's stated
conventions ask" as one of the failure families to cover, and the current
three rows don't cover it. I'm not revising the rubric for this submission's
scope, but `pkg-20` is the stated case this check accepts it will miss, and
I know this because I ran the package through and read why.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
