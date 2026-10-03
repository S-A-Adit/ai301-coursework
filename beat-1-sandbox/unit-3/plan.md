# Plan: issue #62 — Health check references `settings.redis_host`, which does not exist on `Settings`

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62
Repro comment (Unit 2): https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5851153832

## Diagnosis

**Intended behavior:** `GET /health` should report Redis's true status —
`dependencies.redis` should read `"healthy"` when Redis is actually reachable,
and only read `"unhealthy"` when it genuinely is not.

**Actual cause:** `api/routes/health.py`, inside `health_check()`, builds its
Redis client like this:

```python
r = redis.Redis(
    host=settings.redis_host,
    port=settings.redis_port,
    db=0,
    decode_responses=True,
)
```

`Settings` in `core/config.py` defines only a single `redis_url` field
(`redis_url: str = Field(default="redis://localhost:6379/0")`) — it has no
`redis_host` or `redis_port` attributes at all. Accessing `settings.redis_host`
raises `AttributeError` before `redis.Redis(...)` is ever constructed, let
alone before any network call to Redis is attempted. The surrounding
`except Exception` swallows that `AttributeError` and unconditionally sets
`dependencies.redis = "unhealthy"`.

This is confirmed directly in my Unit 2 reproduction, not inferred. Isolating
the attribute lookup on its own, with no endpoint involved:

> ```
> $ .venv/Scripts/python.exe -c "
> from core.config import settings
> print('redis_url:', settings.redis_url)
> try:
>     print(settings.redis_host)
> except AttributeError as e:
>     print('AttributeError:', e)
> "
> redis_url: redis://localhost:6379/0
> AttributeError: 'Settings' object has no attribute 'redis_host'
> ```

And through the actual endpoint, both via `TestClient` and a live `uvicorn`
server:

> ```
> status: 503
> body: {'detail': {'status': 'unhealthy', 'dependencies': {'postgres': 'unhealthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}, ...}}
> ```
>
> server log: `redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"`

So the bug never reaches Redis at all — it fails on the attribute lookup
itself, which is exactly what the issue describes and what the repro confirms.

**File(s) involved:** `api/routes/health.py` (the fix itself); `pyproject.toml`
(the `[[tool.mypy.overrides]]` block for `api.routes.health` — `docs/CONTRIBUTING.md`
states directly: "`api/routes/health.py` `attr-defined` is issue #62. If you fix
one of those, remove its suppression too.").

## Scope

**In:**
- `api/routes/health.py`: change the Redis block in `health_check()` to build
  the client from `settings.redis_url` instead of the nonexistent
  `settings.redis_host` / `settings.redis_port`.
- `pyproject.toml`: remove the `attr-defined` entry from the
  `[[tool.mypy.overrides]]` block for `module = "api.routes.health"`, per
  `docs/CONTRIBUTING.md`'s instruction to remove a seeded bug's suppression
  when it's fixed.
- A new unit test covering this behavior (see Test plan — no test currently
  exists for this issue; see Risks).

**Out:**
- The `postgres: "unhealthy"` result in the same response. My Unit 2 repro
  flagged this explicitly as a separate, pre-existing bug: `await
  db.execute("SELECT 1")` passes a raw string where SQLAlchemy 2.0 requires
  `sqlalchemy.text("SELECT 1")`. This is tracked as its own issue,
  **#61** ("Health check DB probe passes a raw SQL string, which fails under
  SQLAlchemy 2.x") — confirmed via the GitHub API as a separate, currently-open
  issue. Not touching the Postgres check or its exception handling.
- The `vector_db` check and the `safety_events_last_hour` placeholder in the
  same function — neither is implicated by the diagnosed cause.
- The other two mypy error codes on the same override
  (`call-overload`, `index`) — `CONTRIBUTING.md` names `attr-defined`
  specifically as this issue's suppression; I'm not assuming the other two
  codes are also about this bug without checking (see Risks).
- No retry, timeout, or connection-pooling behavior added to the Redis check —
  only the attribute mismatch is being fixed.

## Approach

Replace the `host=`/`port=` constructor call with `redis.Redis.from_url`,
`redis-py`'s standard constructor for a URL-based client:

```python
r = redis.Redis.from_url(settings.redis_url, decode_responses=True)
```

`settings.redis_url`'s default (`redis://localhost:6379/0`) already encodes
the DB index in its path, so `from_url` captures the equivalent of the current
`db=0` without a separate keyword argument. This is a one-line change to the
client construction; nothing else in the Redis block (the `try`/`except`,
the `r.ping()` call, the status assignment) needs to change.

## Test plan

New unit test, `tests/unit/test_health.py` (no test file exists for this
route today, and this issue — unlike most seeded bugs in
`scripts/issues_manifest.json` — has no pre-seeded `@pytest.mark.xfail` test
to un-mark; confirmed by checking the manifest entry for this issue, which
contains no "covering test" sentence the way, e.g., the `KeywordSearcher`
issue's manifest entry does).

- **Before the fix:** patch `redis.Redis` (as imported in
  `api.routes.health`) with a mock; calling the Redis block raises
  `AttributeError` on `settings.redis_host` before the mock is ever
  constructed — this reproduces the exact failure from the Unit 2 repro
  without needing a real Redis connection.
- **After the fix:** the same test, with the mock's `.ping()` returning
  `True`, asserts the client was constructed via `redis.Redis.from_url`
  with `settings.redis_url`, and that `dependencies.redis == "healthy"` in
  the function's result — flipping the exact field the repro showed as
  `"unhealthy"`.
- **Also:** re-run my Unit 2 repro steps as-is (`TestClient` call, then the
  live `uvicorn` + `curl` call) against the fixed code, with a real Redis
  reachable, and confirm `dependencies.redis` now reads `"healthy"` and the
  `redis_health_check_failed` log line no longer appears — the same
  observable the repro captured, flipped.
- To confirm the fix doesn't just always report "healthy" regardless of
  truth, also re-run the `TestClient` call once with Redis stopped, and
  confirm `dependencies.redis` correctly reads `"unhealthy"` with a genuine
  connection-refused error in the log, not the `AttributeError` from before.

## Risks and unknowns

- `health_check()` takes `db=Depends(get_db)`, and there is no existing
  dependency-override pattern in this repo's tests (`tests/integration/` is
  currently empty; `tests/conftest.py` has no DB/client fixtures). I don't yet
  know the cleanest way to exercise `/health` end-to-end in a fast unit test
  without a real Postgres connection; worst case, I test the Redis-client
  construction directly (patching `redis.Redis` at the module level, as
  above) rather than the full endpoint, and note that in Deviations if it
  changes.
- The `pyproject.toml` override for `api.routes.health` suppresses three
  mypy error codes (`attr-defined`, `call-overload`, `index`), but
  `CONTRIBUTING.md` only names `attr-defined` as this issue's. I don't yet
  know whether `call-overload`/`index` are from the same Redis block or from
  unrelated code in the same file (plausibly issue #61's Postgres check, or
  something else). I'll run mypy locally against the fixed file before
  deciding whether to remove just `attr-defined` or more of the list, and
  default to removing only `attr-defined` if the others still fire.
- My Unit 2 repro ran on a native (non-Docker) Postgres setup with Redis not
  running at all; I haven't yet confirmed the fix against a reachable Redis
  instance in this environment — that confirmation is part of the Test plan
  above, not yet done.

## Deviations

The core of the plan held: the fix is the single-line
`redis.Redis.from_url(settings.redis_url, decode_responses=True)` swap exactly
as planned, scope stayed exactly as stated (only `health.py`'s Redis block,
the `attr-defined` mypy suppression, and a new test — #61's Postgres check
untouched), and all of it is committed on `fix/62-redis-health-url`. Three
things differed from what the plan said, in each case because an open
question from Risks and unknowns resolved during the build rather than before
it:

1. **The mypy override question resolved exactly as the contingency planned,
   with one answer I hadn't guessed.** I ran mypy with the `api.routes.health`
   override temporarily cleared, as planned. `attr-defined` was gone, as
   expected. `index` was still firing (7 pre-existing errors from
   `health_status`'s untyped dict literal, unrelated to #61 or #62), so I kept
   it suppressed, also as planned. What I hadn't anticipated: `call-overload`
   wasn't firing at all — it dropped out cleanly with no errors appearing, so
   the final override is `["index"]`, not `["attr-defined", "index"]` plus a
   judgment call. I also updated the explanatory comment above the override
   block, since it named `attr-defined` -> #62 specifically and that's no
   longer accurate.

2. **The test didn't need a dependency-override or `TestClient` at all** —
   simpler than the plan's "worst case" fallback, not a worse compromise.
   `health_check(db=...)` is a plain async function with `Depends(get_db)`
   only as a default value; calling it directly with a mock session (the same
   pattern this repo already uses in `tests/unit/test_review_service.py`)
   exercises the real function body with no FastAPI wiring needed. I patch
   `redis.Redis.from_url` at its real module path (not "as imported in
   `api.routes.health`" the way the plan phrased it) since `health.py` does a
   local `import redis` inside the function, so the module-qualified path is
   what actually needs patching.

3. **I did not confirm the fix against a real, reachable Redis instance** —
   the one test-plan item I'm not able to check off. This machine has no
   Docker, the same constraint my Unit 2 repro already disclosed. What I
   did instead: the new unit test proves the healthy path with a mocked
   `ping()`, confirmed to fail on the original code and pass on the fixed
   code (verified both ways by temporarily reverting the fix and re-running).
   And re-running the actual Unit 2 `TestClient` repro against the fixed code,
   with Redis still not running here, shows the failure mode itself changed:
   the log line is now `error='Error 10061 connecting to localhost:6379. No
   connection could be made because the target machine actively refused it.'`
   — a genuine connection refusal — where it previously read `error="'Settings'
   object has no attribute 'redis_host'"`. That's real evidence the attribute
   bug is gone, just not the "healthy" branch itself, live, in this
   environment.

Nothing here changes the plan's diagnosis, scope, or chosen approach, so the
posted plan comment is still accurate as written; no follow-up correction
comment is needed on the issue thread.
