# Issue #61: SQLAlchemy health-check database probe

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61
Contributor: Damola-png
Posted plan: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5997935131
Fork: https://github.com/Damola-png/pathreview-ai301-fa26-s1
Branch: `fix/61-sqlalchemy-health-check`
Base: `f89c06fc3ff292df2a04a39ac51319d32a76b779`

## Reproduction evidence

My posted reproduction: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5997780956

Environment: Windows 11 build 26200, Python 3.12.14, SQLAlchemy 2.0.54, aiosqlite 0.22.1, FastAPI 0.142.2, pytest 9.1.1, pytest-asyncio 1.4.0, and httpx 0.28.1. The issue explicitly allows invoking the handler with a live session.

The report states:

> The database fixture is a live in-memory SQLite session through SQLAlchemy's async API. The control `await session.execute(text("SELECT 1"))` returns 1. Calling the real `health.health_check` with that same session logs the textual-SQL coercion error and raises HTTP 503; a GET to the real router with a database dependency override also returns 503.

The exact trigger was:
```powershell
.\.venv\Scripts\python.exe -X utf8 -m pytest tests/unit/test_health.py -q --tb=short
```

Quoted observed output:
```text
postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
E   assert 503 == 200
2 failed, 1 passed, 4 warnings in 7.47s
```

The negative database-error test passed before any fix: an OperationalError produces HTTP 503 and an unhealthy PostgreSQL status. Redis/configuration are controlled only in the tests, isolating the independent #62 problem. This is not a full-stack PostgreSQL/Redis deployment test.

## Diagnosis

At the base above, `api/routes/health.py` passes a bare `"SELECT 1"` string to `db.execute`. The observed coercion error, paired with the same-session `text("SELECT 1")` control returning 1, identifies the statement representation as the failing input. The existing exception handler then marks PostgreSQL unhealthy and raises HTTP 503. The connection is usable in the control, so hiding the exception or relaxing the 503 rule would not address the cause.

SQLAlchemy's documented textual statement constructor is `text()`: https://docs.sqlalchemy.org/en/20/core/sqlelement.html#sqlalchemy.sql.expression.text. The backend-independent diagnosis is supported by the control and error; PostgreSQL-specific integration is not established by the SQLite run.

## Scope

In scope: wrap the existing database probe in SQLAlchemy `text()`, keep its query and error behavior unchanged, and commit regression tests that exercise the actual handler and router with a live async SQLAlchemy session.

Out of scope: Redis configuration (#62), vector-store/safety placeholders, changing response fields/status logic, startup/migrations, a repo-wide SQL rewrite, new endpoints, and unrelated cleanup.

Files to touch:
- `api/routes/health.py`: import `text` from SQLAlchemy and replace the probe with `await db.execute(text("SELECT 1"))`.
- `tests/unit/test_health.py`: retain the already-run three-test reproduction as regression coverage.
- `pyproject.toml`: add `aiosqlite>=0.20.0` only to development dependencies so CI can run the live SQLite fixture.

The existing health-module mypy suppressions will not be removed speculatively: `db` is unannotated and #62 still affects this module. If a check proves an issue-61-specific suppression obsolete, record and re-check that scope change before posting an update.

## Approach

1. Keep the failing before capture and the test input unchanged.
2. Import `text` in the health module and wrap only the constant probe.
3. Declare aiosqlite as a development test dependency; do not add it to production dependencies.
4. Read the three-file diff for unrelated changes. Keep `plan.md` and `comment.md` out of fix commits.
5. Re-run the same reproduction tests, then lint/format/type checks. Capture real output before committing and pushing to the fork.

The tests isolate Redis and configuration with pytest monkeypatch; they do not replace or copy the database probe. The positive tests execute a live SQLAlchemy session, and the HTTP test mounts the production router with FastAPI's database dependency override.

## Test plan

Re-run the exact pytest command above against the changed health module with the same virtual environment.

Expected after:
- The same-session `text("SELECT 1")` control still returns 1.
- The actual handler returns healthy status with `dependencies.postgres == "healthy"`.
- GET `/health` through the production router returns HTTP 200 with PostgreSQL healthy when the other dependencies are test-controlled healthy.
- The OperationalError control still produces HTTP 503, PostgreSQL unhealthy, and Redis healthy.
- All three tests pass, replacing the before result of two failures and one pass.

Also run Ruff and Black on the repository and mypy on the affected module/tests. Attempt the broader unit suite with project dev dependencies if the local environment supports installation. Save limitations rather than treating skipped integration/frontend/CI jobs as passing. All five PR CI jobs must pass in Unit 4; this unit does not open a PR.

## Risks and unknowns

- SQLite proves SQLAlchemy statement coercion and the real handler/router behavior, not PostgreSQL credentials, network connectivity, or the complete deployment. A PostgreSQL-backed check remains follow-up integration coverage.
- Redis #62 remains unfixed; an unisolated full-stack `/health` may still return 503. Do not claim the whole endpoint is repaired in deployment.
- Local Python 3.12 is allowed by the project's >=3.11 requirement but differs from CI's Python 3.11. CI is still required before a Unit 4 PR is ready.
- Existing Pydantic and UTC timestamp deprecation warnings are outside this scope.
- If the three-test rerun does not give the expected result, stop and update this diagnosis/plan before expanding the implementation.

AI assistance: OpenAI Codex helped inspect the source, run the reproduction, and draft this plan. Claude Code runs the installed plan-check skill. Neither the report nor this plan claims a human executed each command.

## Deviations

The production change stayed within the posted plan: only the text() import and probe wrapping changed in health.py, with aiosqlite declared as a development dependency and the three reproduction cases retained.

One test-only adjustment followed verification. The additional focused mypy run treated FastAPI's HTTPException.detail as a string and reported three dictionary-index errors in the negative control. The test now asserts that detail is a dictionary before checking the same status/dependency fields. No trigger, fixture, expected status, or production error handling changed. This does not invalidate the posted plan's three-test promise or require an upstream scope update.

The exact reproduction command changed from two failures and one pass to three passes against the real changed handler/router. The broader backend unit suite passed with the repository's seeded expected-failure markers left intact. Redis #62, deployment integration, and the five PR CI jobs remain outside the evidence from this build.

Unit 4 precheck added the unit marker to the three regression tests. The Makefile runs pytest with -m unit; without this module marker these tests were excluded by that command despite passing when called directly. No test trigger or expected outcome changed.

The Unit 4 suppression audit found no issue #61 xfail marker. A temporary copy of the mypy configuration with only the health override removed reported ten errors: seven dependency-status dictionary index errors, two missing Redis settings attributes (#62), and one Redis constructor overload error. None was the SQL statement-coercion bug. The active suppressions remain; the obsolete comment saying they also cover #61 was corrected. This comment-only clarification is the additional pyproject change, not a new suppression or an unrelated bug fix.

The Unit 4 assignment explicitly requires leaving the CI checkbox unticked before opening the PR and grades the PR as opened. That supersedes this plan's earlier assumption that CI must already be green to submit Unit 4; all five jobs remain required for maintainer review. The description will disclose pending CI, the expected empty integration suite, and any local frontend failure instead of claiming a full-stack or all-green result.

Publishing found an infrastructure mismatch: Damola-png/pathreview was a fork of ascherj/pathreview, outside the cohort repository's fork network. The same tested head (14792e0) was pushed to the new Damola-png/pathreview-ai301-fa26-s1 fork of the cohort repo. The old fork and origin remote were preserved; course-fork is the PR push remote. No production/test commit or before/after result changed.

