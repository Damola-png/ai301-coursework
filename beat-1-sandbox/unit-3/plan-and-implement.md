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

Damola-png

**Plan comment**

[Posted plan comment](https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5997935131)

<!-- BEGIN EXACT PLAN COMMENT -->
Plan for #61, based on [my reproduction](https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5997780956) at `f89c06fc3ff292df2a04a39ac51319d32a76b779` (Windows 11, Python 3.12.14, SQLAlchemy 2.0.54).

The real handler and /health router failed with:
```text
postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
E   assert 503 == 200
2 failed, 1 passed, 4 warnings in 7.47s
```
The same live SQLAlchemy session returns 1 for `text("SELECT 1")`. That identifies the bare-string probe as the cause of the false unhealthy database result, rather than a failed connection.

I will import `sqlalchemy.text` in `api/routes/health.py` and change only the probe to `await db.execute(text("SELECT 1"))`. I will keep the three reproduction tests in `tests/unit/test_health.py` and add aiosqlite to the development dependencies in `pyproject.toml` so CI can execute their live async SQLite fixture.

Validation: re-run
```powershell
.\.venv\Scripts\python.exe -X utf8 -m pytest tests/unit/test_health.py -q --tb=short
```
I expect all three tests to pass: the real handler reports PostgreSQL healthy, the mounted production router returns HTTP 200 with other dependencies test-controlled healthy, and the OperationalError control still returns HTTP 503 with PostgreSQL unhealthy. I will capture the after output and run lint/format/type checks.

The SQLite fixture is a live database session, but it is not a PostgreSQL deployment test. Redis/configuration are isolated in these tests to avoid masking #61 with #62. I will not change Redis, placeholder checks, exception handling, response shape, or unrelated modules. The existing health-module type suppressions stay unless an issue-61-specific obsolete suppression is demonstrated; any scope change will be recorded and re-checked. A full-stack endpoint may still return 503 due to #62, and PostgreSQL integration and all five PR CI jobs remain required follow-up checks before Unit 4 review.

AI assistance: OpenAI Codex helped inspect the source, run the reproduction, and draft this plan; Claude Code checks the package with the installed plan-check skill.

<!-- END EXACT PLAN COMMENT -->

---

## Your branch

**Branch**

`fix/61-sqlalchemy-health-check`

Fork: https://github.com/Damola-png/pathreview-ai301-fa26-s1
Branch: https://github.com/Damola-png/pathreview-ai301-fa26-s1/tree/fix/61-sqlalchemy-health-check

Unit 4 publishing correction: the original Damola-png/pathreview fork belongs to ascherj/pathreview, not the cohort fork network. It remains intact; the same tested commits were pushed to the correct cohort fork above, through the added course-fork remote. No production code changed during that move.

Pushed implementation commit: `e1d0feb` (`fix(api): wrap health probe SQL in text`). Exactly three files were committed: `api/routes/health.py`, `pyproject.toml`, and `tests/unit/test_health.py`. Neither `plan.md` nor `comment.md` is in the fix commit.

Unit 4 follow-up commit: `14792e0` (`test(api): include health regressions in unit checks`). It marks the three regression tests as unit tests and corrects the stale issue-61 suppression comment after a diagnostic confirmed the active errors belong to Redis/settings and dictionary typing. These changes are recorded in the updated `plan.md` Deviations; the branch still changes only the same three files. Fresh Unit 4 outputs are preserved in `../unit-4/test_evidence.md`, without altering the Unit 3 run or before/after captures below.

**Evidence**

Reproduction source: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5997780956

Working directory for both runs: `C:\Users\damol\OneDrive\Documents\pathreview`. Before: cohort base `f89c06fc3ff292df2a04a39ac51319d32a76b779`, with the new reproduction tests but unchanged production code. After: the three-file fix on `fix/61-sqlalchemy-health-check`, including the recorded dictionary type assertion in the negative control.

Windows 11 build 26200, Python 3.12.14, SQLAlchemy 2.0.54, aiosqlite 0.22.1, FastAPI 0.142.2. Both positive cases use a live SQLAlchemy async SQLite session and call the actual health handler/router. Redis and configuration are isolated to prevent #62 from deciding the result. The negative control injects OperationalError and verifies the existing unhealthy/503 handling. This is not evidence of a full PostgreSQL/Redis deployment passing.

Before command (exit 1):
```powershell
.\.venv\Scripts\python.exe -X utf8 -m pytest tests/unit/test_health.py -q --tb=short
```

Before output:
```text
FF.                                                                      [100%]
================================== FAILURES ===================================
______________ test_health_check_accepts_live_sqlalchemy_session ______________
tests\unit\test_health.py:55: in test_health_check_accepts_live_sqlalchemy_session
    result = await health.health_check(db=database_session)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
api\routes\health.py:86: in health_check
    raise HTTPException(
E   fastapi.exceptions.HTTPException: 503: {'status': 'unhealthy', 'dependencies': {'postgres': 'unhealthy', 'redis': 'healthy', 'vector_db': 'healthy'}, 'safety_events_last_hour': 0, 'timestamp': '2026-10-05T15:32:34.203148'}
---------------------------- Captured stdout call -----------------------------
2026-10-05 10:32:34 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
2026-10-05 10:32:34 [debug    ] redis_health_check_passed
2026-10-05 10:32:34 [debug    ] vector_db_health_check_passed
___________ test_health_endpoint_returns_200_for_reachable_database ___________
tests\unit\test_health.py:76: in test_health_endpoint_returns_200_for_reachable_database
    assert response.status_code == 200
E   assert 503 == 200
E    +  where 503 = <Response [503 Service Unavailable]>.status_code
---------------------------- Captured stdout call -----------------------------
2026-10-05 10:32:35 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
2026-10-05 10:32:35 [debug    ] redis_health_check_passed
2026-10-05 10:32:35 [debug    ] vector_db_health_check_passed
============================== warnings summary ===============================
core\config.py:7
  C:\Users\damol\OneDrive\Documents\pathreview\core\config.py:7: PydanticDeprecatedSince20: Support for class-based `config` is deprecated, use ConfigDict instead. Deprecated in Pydantic V2.0 to be removed in V3.0. See Pydantic V2 Migration Guide at https://errors.pydantic.dev/2.13/migration/
    class Settings(BaseSettings):

tests/unit/test_health.py::test_health_check_accepts_live_sqlalchemy_session
tests/unit/test_health.py::test_health_endpoint_returns_200_for_reachable_database
tests/unit/test_health.py::test_health_check_preserves_503_for_database_failure
  C:\Users\damol\OneDrive\Documents\pathreview\api\routes\health.py:27: DeprecationWarning: datetime.datetime.utcnow() is deprecated and scheduled for removal in a future version. Use timezone-aware objects to represent datetimes in UTC: datetime.datetime.now(datetime.UTC).
    "timestamp": datetime.utcnow().isoformat(),

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
=========================== short test summary info ===========================
FAILED tests/unit/test_health.py::test_health_check_accepts_live_sqlalchemy_session
FAILED tests/unit/test_health.py::test_health_endpoint_returns_200_for_reachable_database
2 failed, 1 passed, 4 warnings in 7.47s
```

After command (exit 0), re-run on the real changed code:
```powershell
.\.venv\Scripts\python.exe -X utf8 -m pytest tests/unit/test_health.py -q --tb=short
```

After output:
```text
...                                                                      [100%]
============================== warnings summary ===============================
core\config.py:7
  C:\Users\damol\OneDrive\Documents\pathreview\core\config.py:7: PydanticDeprecatedSince20: Support for class-based `config` is deprecated, use ConfigDict instead. Deprecated in Pydantic V2.0 to be removed in V3.0. See Pydantic V2 Migration Guide at https://errors.pydantic.dev/2.13/migration/
    class Settings(BaseSettings):

tests/unit/test_health.py::test_health_check_accepts_live_sqlalchemy_session
tests/unit/test_health.py::test_health_endpoint_returns_200_for_reachable_database
tests/unit/test_health.py::test_health_check_preserves_503_for_database_failure
  C:\Users\damol\OneDrive\Documents\pathreview\api\routes\health.py:28: DeprecationWarning: datetime.datetime.utcnow() is deprecated and scheduled for removal in a future version. Use timezone-aware objects to represent datetimes in UTC: datetime.datetime.now(datetime.UTC).
    "timestamp": datetime.utcnow().isoformat(),

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
3 passed, 4 warnings in 3.93s
```

The full captures are also committed as `before.txt` and `after.txt`. The new test cases fail on the unfixed code and pass on the fix; they do not copy the production probe into a stand-in script.

The installed plan-check first returned `accept` before the plan was posted and the code edited. Its complete JSON/read-out is in `plan-check-before-build.txt`. The test-only type assertion is explained under `## Deviations` in `plan.md`; the posted comment's scope and promised cases remain true, so no corrective upstream comment is needed.

After recording that adjustment, the installed skill re-checked the updated plan and unchanged posted comment and returned `accept` again, with all seven required checks and the preferred check passing. The complete output is in `plan-check-after-build.txt`. Neither live run changed the rubric or replaced the scored eval transcript.

Additional local checks on the final implementation:

```powershell
$env:LLM_PROVIDER = 'mock'
.\.venv\Scripts\python.exe -X utf8 -m pytest tests/unit -q --tb=short
```
```text
378 passed, 53 xfailed, 5 warnings in 22.30s
```
The 53 seeded expected failures are not fixed by this issue. Complete output: `unit-suite.txt`.

```powershell
.\.venv\Scripts\python.exe -m ruff check --no-cache .
```
```text
All checks passed!
```

```powershell
.\.venv\Scripts\python.exe -m black --check .
```
```text
111 files would be left unchanged.
```
The Black line is an excerpt; its complete output is in `black.txt`.

```powershell
.\.venv\Scripts\python.exe -m mypy api/ core/ ingestion/ rag/ agent/ safety/ --ignore-missing-imports --cache-dir "$env:TEMP\ai301-issue61-mypy-cache"
```
```text
Success: no issues found in 76 source files
```

```powershell
.\.venv\Scripts\python.exe -m mypy api/routes/health.py tests/unit/test_health.py --ignore-missing-imports --cache-dir "$env:TEMP\ai301-issue61-focused-mypy-cache"
```
```text
Success: no issues found in 2 source files
```
The focused command also prints unused-override notes because it checks only two files; full output: `test-typecheck.txt`. The first focused check found three dictionary-index type errors in the negative control; adding the dictionary assertion made the final check pass without weakening its expected results.

```powershell
.\.venv\Scripts\python.exe -X utf8 -m pytest tests/integration -q --tb=short
```
```text
no tests ran in 0.71s
```
Exit 5: the repository's integration directory has no tests, and its CI workflow explicitly treats this code as success. This is collection evidence only, not a live PostgreSQL/Redis integration pass. PR CI has not been run and no Unit 4 PR has been opened.

Frontend setup/check, from `pathreview/frontend/`:
```powershell
npm.cmd ci --no-audit --no-fund
npm.cmd test -- --run
```
```text
added 324 packages in 44s
FAIL src/components/__tests__/ProfileForm.test.tsx > ProfileForm > validates portfolio URL character limit
Error: Test timed out in 5000ms.
Test Files  1 failed | 1 passed (2)
Tests  1 failed | 17 passed (18)
```
These are excerpts; full outputs are in `frontend-first-run.txt` and `frontend-retry.txt`. The identical default command was run twice, with the same 5-second timeout on the test that types 501 characters. No frontend source, test, lockfile, or timeout configuration was changed in this issue's commit. This local frontend check is not green and must not be presented as all-five-jobs CI passing.

Diagnostic only, with a longer command-line timeout (no file/config edit):
```powershell
npm.cmd test -- --run --testTimeout 30000
```
```text
Test Files  2 passed (2)
Tests  18 passed (18)
Duration  19.14s (transform 547ms, setup 4.37s, collect 1.20s, tests 11.22s, environment 8.53s, prepare 1.94s)
```
Full output: `frontend-diagnostic.txt`. This shows the existing assertions pass with more time on this Windows environment; it does not replace the default-timeout failures or the requirement for real PR CI.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

One full scored run was completed on October 5, 2026, using the official Unit 3 harness and pinned `sonnet` model. No targeted retries or rubric revisions followed this run.

Command, from `ai301-unit3-starter/eval/`:
```powershell
& 'C:\Users\damol\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe' -X utf8 run_eval.py --rubric 'C:\Users\damol\.claude\skills\plan-check\rubric.md' --evidence 'C:\Users\damol\.claude\skills\plan-check\references\evidence-guide.md' --skill 'C:\Users\damol\.claude\skills\plan-check\SKILL.md' --save-run eval-run.txt --out 'C:\Users\damol\AppData\Local\Temp\ai301-unit3-eval-run-1.json'
```

Result, quoted from the submitted harness-written transcript:
```text
categories: clear-accept 7/7  scope-creep 4/4  thread-convention 1/2  unbuildable 3/3  wrong-cause 4/4
agreement: 19/20 scored items  (bar: 18/20: PASS)
```
The target is 18/20 and at least one match per category; this run satisfies both. The four calibration packages were not scored. The saved transcript was generated by `--save-run`, not edited manually. Its timestamp is `2026-10-05T15:22:47Z`; the uploaded rubric, evidence guide, procedure, and SKILL.md match its four SHA-256 fingerprints.

**Package analysis**

In the only full run, `pkg-20` received `accept` from the rubric and `reject` from gold. All required checks passed in the model's output.

The package's repo facts say, "All AI usage in any form must be disclosed, stating the tool used and the extent of the assistance". The candidate comment describes a generation counter and conditional recomputation of `prev`, but names no AI tool or extent. The model's deciding Thread and conventions evidence was: "Comment is consistent with plan and repro and follows maintainer direction; no evidence in the bundle of AI use requiring disclosure, and silence adds no requirement."

That explains the acceptance rather than merely restating the score: the procedure uses only the frozen package, and the grader treated missing evidence of candidate AI use as removing the disclosure obligation. Mechanically, the plan follows the maintainer's conditional-recompute direction, covers both fuzz cases and the no-hyperlink control, and states the benchmark risk. Gold still rejects the package. The missed signal is the outgoing comment's policy compliance, not its pointer diagnosis or implementation steps. This conditional interpretation is a known limit retained in the submitted rubric; I did not rewrite a passing run's transcript to conceal it.

**Check rationale**

Current Thread and conventions row, quoted exactly from `tools/plan-check/rubric.md`:

```markdown
| Thread and conventions | Thread highlights and Repo facts contribution policy/template asks; Candidate plan comment compared with Candidate plan and repro. Live: the full issue thread, contributor docs and linked policies, draft comment, and voice guide. | The comment presents this contributor's diagnosis, proposed change, and validation, agrees with the plan and repro, and follows explicit maintainer direction and posting conditions. Include AI disclosure when required and respect a stated human-authored-comment rule; claiming compliance does not excuse text that explicitly contradicts it. Silence creates no extra policy requirement. Do not substitute a generic pledge or another person's plan. In Path Review, another student's claim or plan does not block this student's independent plan. | required |
```

This wording asks for three concrete sources: the actual outgoing comment, its plan/reproduction, and explicit repository or maintainer conditions. A coherent implementation plan is not enough when its comment conflicts with posting rules. I rejected imposing a universal disclosure requirement on repositories with no such rule, treating another student's classroom claim as a blocker, or accepting a generic promise instead of the contributor's own diagnosis/change/validation. In this run `pkg-04` was correctly rejected, but `pkg-20` exposed ambiguity in "when required" and "Silence creates no extra policy requirement." The wording and limitation are unchanged in the uploaded file.

**Trade-offs**

This check's conditional disclosure language can miss `pkg-20`: the grader read silence about candidate AI use as evidence that disclosure was not required, even though the bundle supplies a strict AI policy. Requiring disclosure whenever a policy is present could avoid that miss, but could also reject a genuinely human-authored comment when the frozen package contains no evidence of AI assistance. I retained the evidence-only threshold and documented this limit instead of adding an unstated assumption to a frozen eval bundle. No canary retry was run because the rubric was not loosened or revised; the full run is the evidence that all seven clear accepts, all four scope-creep cases, all three unbuildable cases, all four wrong-cause cases, and one of the two thread-convention cases matched. In my actual upstream report and plan, Codex assistance is disclosed explicitly.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
