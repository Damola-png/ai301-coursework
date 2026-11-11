# Contribution: Review creation does not verify profile ownership

Path: `beat-2-contribution/contribution-story.md`

**Contribution Number:** [1]
**Student:** Adedamola Olayefun
**GitHub Username:** Damola-png
**Issue:** https://github.com/ascherj/pathreview/issues/163
**Pull Request:** https://github.com/ascherj/pathreview/pull/699

This is an existing July-August 2026 contribution, revisited on November 11 for the AI301 story at my request. Its original journal identifies it as AI-201 work. I confirmed during the November 11 catch-up that my instructor approved reusing this PR for Assignment 5. The new tools below were installed retrospectively; they did not select, guard or approve the August PR, and the original dates remain visible.

---

## Toolkit

The phase-specific blocks record what actually ran, what it produced and its limits. This is the example shape only:

```text
**Toolkit**
- Tool: [name and use]
- What it produced: [observable result]
- Difficulties: [limitation]
- What changed: [revision or next change]
```

**Unit 5: 2026-11-11 - scout setup and free search, not a retrospective selection verdict**

The issue-scout was installed separately from the preserved issue-select submission, with Python/API queries and the carried-forward fit profile. Two free searches returned 100 issues each, 200 unique results and 169 unassigned survivors; the two highest-ranked results were generic news-agent tasks rather than ownership work, exposing the weakness of relying on cheap metadata. Claude OAuth was expired, so no candidate received a model verdict, and this scan did not select the already-existing #163 contribution. A next strategy revision would strengthen API/ownership title signals and check whether that loses relevant candidates before paying for grading.

**Unit 6: 2026-11-11 - canonical voice rules and local review-service memory**

The canonical voice guide now has claims, questions and pings rules with wrong/right pairs, and the installed plan/precheck skills point to it. Direct invocation of the voice hook on a body-less comment command correctly stopped with "no body the guard can read"; this tests the guard program, not successful posting through a signed-in Claude session. A scoped Claude orientation was attempted but failed authentication, so the short CLAUDE.local.md was distilled from direct source and CONTRIBUTING inspection instead. The rules now distinguish service mocks from live HTTP evidence and forbid nudging a self-held draft.

**Unit 7: 2026-11-11 - baseline-aware manifest and a blocked final model check**

The ship-gate was installed with a settings backup and a local-only manifest. Its three required checks passed: the 21-test review-service suite, lint on the two changed Python files and non-writing formatting on that pair; the full-repo exclusions have dated baseline evidence rather than disappearing. The gate then stopped because the precheck's Claude login was expired, so no accept verdict or complete gate pass is claimed. During this catch-up, fresh tests also exposed six owner-fixture failures, which were corrected in the test-only follow-up rather than hidden behind the older journal.

**Unit 8: 2026-11-11 - live PR state and a playbook that prevents an inappropriate nudge**

The installed PR monitor's free --state run read #699, its reviews/checks and its quiet period, and confirmed all four playbook sections are filled. It reported no comments/reviews, REVIEW_REQUIRED and no reported checks; it counted 100 quiet days even after my new push because its quiet clock measures activity by someone other than the author. No full monitor draft was generated while Claude authentication remained unavailable, so there is no generated reply to pretend I revised or sent. The playbook uses a provisional 14-day threshold but forbids nudging this still-draft PR; its insufficient merged-PR sample is disclosed instead of invented.

---

## Why I Chose This Issue

The July journal records a tier-2, bounded authorization bug: POST /reviews supplied the authenticated user ID, but the service ignored it when creating a review. That fits the Python/FastAPI work in my recorded profile and gives a concrete question to investigate: can another user's profile cause a write? It also offered a chance to compare the write path with ownership checks already present in get_review and list_reviews rather than inventing a separate policy.

The original selection was based on that manual fit checklist, not the November scout. The retrospective scout has no graded verdict because authentication stopped the model stage. For a fresh contributor today, open PRs and claims are real availability signals; continuing my existing #699 is not evidence this issue is free for a new claim. The maintainer's issue comment explicitly permits existing claimants to continue, but that does not establish an earlier personal claim from me.

**Claim comment:** claim pending (2026-11-11). No Damola-png claim comment was found among the issue's 17 comments. The existing PR is linked above; no backdated claim or new public comment has been invented.

**Toolkit**

- Tool: issue-scout, installed with a carried-forward Python/FastAPI fit profile, two broad GitHub CLI searches, deterministic metadata ranking and the Unit 6 scope-difficulty patch.
- What it produced: 200 unique search results, 169 unassigned survivors and two ungraded candidates; not an accept/reject verdict on #163.
- Difficulties: cheap metadata over-ranked unrelated agent tasks, and expired OAuth blocked grading. The tool was not present at the original selection.
- What changed: Bounded contribution became scope-feasible, open edges became difficulty notes, and the sandbox claim exception was removed from the live scout. No claim is made that the patched rubric has passed a new regression eval.

---

## Understanding the Issue

**Problem Description**

The route passes current_user.id to create_review, but the original implementation constructs a Review from profile_id without using that user ID to authorize the target profile.

**Expected Behavior**

Only the owner may create a review. A missing or inaccessible profile must be rejected before Review is added or committed; a valid owner's review should still start pending.

**Current Behavior**

At the reproduction commit, an unauthorized request reaches db.add(). On the fixed service, the profile is fetched by ID and its user_id is compared with the caller. A missing profile or owner mismatch raises HTTPException with status 404 and detail "Profile not found".

**Affected Components**

api/routes/reviews.py carries the authenticated ID; core/services/review_service.py enforces the pre-write guard; core/models/profile.py supplies ownership; tests/unit/test_review_service.py exercises the service boundary.

**Toolkit**

- Tool: direct source inspection and the original PLAN.md, checked against the current service and route.
- What it produced: the precise route-to-service path and the 404/no-write contract, not a claim that the entire authorization system is secure.
- Difficulties: a mock-backed test cannot establish live SQL filtering, a deployed endpoint result or background processing isolation.
- What changed: the story names the actual ID lookup followed by owner comparison instead of saying the SQL query itself filters on both IDs.

---

## Reproduction Process

**Environment Setup**

The fresh November verification used Windows PowerShell, Python 3.12.14, pytest 9.1.1, SQLAlchemy 2.0.54 and FastAPI 0.142.2. Ruff was 0.16.10, Black 26.10.0 and mypy 2.4.0. The existing development environment was reused; no new dependency installation is claimed for this checkout.

A separate fork checkout and a detached reproduction worktree kept the sandbox branch untouched. The original reproduction uses a mocked asynchronous database session against the actual create_review function, not two live authenticated HTTP clients. A new environment needs the project's Python development dependencies, as described in its setup guide; the exact interpreter used here was C:/Users/damol/OneDrive/Documents/pathreview/.venv/Scripts/python.exe.

**Steps to Reproduce**

1. Check out reproduction commit 13aa66480b8fd02bed5ed4e4a26f43efc2a7306e in the fork.
2. From that checkout, run the selector below. Its fixture supplies a target profile associated with a different user and asserts that review creation must not write.
3. Observe the unexpected add() call, then run the same selector from the fixed branch and compare the result.

```powershell
& 'C:/Users/damol/OneDrive/Documents/pathreview/.venv/Scripts/python.exe' -X utf8 -m pytest tests/unit/test_review_service.py::TestReviewService::test_create_review_rejects_profile_owned_by_another_user -q --tb=short
```

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/Damola-png/pathreview/commit/13aa66480b8fd02bed5ed4e4a26f43efc2a7306e
- **Screenshots/logs:** fresh November output at that commit: `AssertionError: Expected 'add' to not have been called. Called 1 times.` Result: `1 failed, 1 warning in 5.86s`.
- **My findings:** the fixed branch rerun gives `1 passed, 1 warning in 5.06s`. The current cross-user test also asserts 404, "Profile not found", no add and no commit; this is service-level evidence, not a live HTTP reproduction.

**Toolkit**

- Tool: pytest, isolated Git checkouts and the existing asynchronous session/result fixtures.
- What it produced: an observed unauthorized write before the fix and a passing identical test selector after it.
- Difficulties: the old journal alone could not prove today's result, and the first restricted-environment invocation stalled before producing output.
- What changed: that stalled invocation was stopped, the actual commands were rerun with the required filesystem access, and the old reproduction commit was executed directly rather than paraphrased as a new run.

---

## Solution Approach

**Analysis**

Authorization belongs before the first write. Existing read functions already join through Profile.user_id; the create path was inconsistent because it accepted user_id but never used it. Returning 404 for both missing and inaccessible profiles preserves the existing non-disclosure convention.

**Proposed Solution**

Keep the existing service fix: fetch the Profile by the requested ID, compare its owner with the authenticated UUID, and raise 404 before constructing or adding Review when unavailable. Preserve the pending state for an owner. Do not change schemas, dependencies, unrelated services, the sandbox health fix or all existing lint/type debt.

**Implementation Plan**

1. Trace the route's profile_id and authenticated user_id into create_review.
2. Check ownership before Review construction and add/commit.
3. Cover cross-user rejection and valid-owner creation using the real service function.
4. Add the missing-profile no-write control and run the same reproduction selector.
5. Compare the focused checks and full repo checks with untouched main, keeping all negative outcomes visible.

**Deviations**

The August PR already contains PLAN.md and JOURNAL.md in addition to the two Python files; it is not a two-file diff. The November audit at head 9bac5ae found six failing success tests because bare Profile mocks had no caller-matching user_id. Follow-up cee1b85 sets the owner on those fixtures and adds the missing-profile regression, without changing production logic. This is a later test repair, not work that was present when the PR opened.

**Toolkit**

- Tool: the historical PLAN.md, direct comparison of read/write ownership behavior and current test execution.
- What it produced: the bounded pre-write guard and a precise separation between the historical implementation and November fixture changes.
- Difficulties: the older journal claimed 20 focused passes, but the actual pre-follow-up branch had 6 failed and 14 passed.
- What changed: fixture identity was corrected explicitly; the production guard was not weakened to satisfy the tests.

---

## Testing Strategy

The decisive observation is a rejected cross-user operation with no database write, plus a successful valid-owner control. The missing-profile case must reject with the same 404 contract and no add/commit.

All rows below used the same Python environment. Commands are the arguments following the absolute Python path above; make is unavailable, so these are explicit equivalents of the Makefile tools.

| Check | Command arguments | Untouched main d5f196d | Current branch cee1b85 |
|---|---|---|---|
| Focused review service | `-m pytest tests/unit/test_review_service.py -q --tb=short` | Not used as the before reproduction | 21 passed, 1 warning |
| Full unit suite | `-X utf8 -m pytest tests/unit -q -m unit --tb=short` | 53 failed, 375 passed | 40 failed, 390 passed |
| Full Ruff | `-m ruff check . --output-format concise` | 182 findings, exit 1 | 173 findings, exit 1 |
| Full Black | `-m black --check .` | 52 files would reformat, exit 1 | 50 files would reformat, exit 1 |
| Full mypy | `-m mypy api/ core/ ingestion/ rag/ agent/ safety/` | 103 errors in 26 files, exit 1 | 103 errors in 26 files, exit 1 |
| Integration | `-m pytest tests/integration -v -m integration` | Not rerun | 0 collected, exit 5 |
| Changed-file Ruff | `-m ruff check core/services/review_service.py tests/unit/test_review_service.py` | Not claimed | Pass |
| Changed-file Black | `-m black --check core/services/review_service.py tests/unit/test_review_service.py` | Not claimed | Pass; 2 unchanged files |

All 40 failing branch test IDs also fail on main; there are no newly failing IDs. Normalized file/message/error-code comparisons found no new Ruff or mypy findings. Those comparisons support dated exclusions, not a claim that the full checks passed. No tests exist in the integration folder, so exit 5 is not an integration success.

No GitHub checks are currently reported for #699. The five green jobs on sandbox PR #137 are unrelated and are not transferred to this contribution's story.

**Toolkit**

- Tool: pytest, Ruff, Black and mypy, with clean-baseline comparisons and a three-row focused ship manifest.
- What it produced: observed before/after behavior, 21 focused passes, baseline-versus-branch counts and explicit empty-integration evidence.
- Difficulties: the old repository has substantial baseline failures, and a broad formatter would add unrelated edits.
- What changed: focused checks remain mandatory; full checks remain visible in the manifest's dated exclusion table, with no unsafe fixes or hidden failures.

---

## Implementation Notes

**2026-07-21**

The fork's commit 9794364 records issue selection in JOURNAL.md. The recorded reason is a bounded API/service authorization gap with an observable regression, not evidence of using the later AI301 scout.

**2026-07-26**

Commit 13aa664 adds the original ownership reproduction. The plan follows in 1f55a1c and aa76df5. The November rerun confirms the original no-write assertion fails at the unexpected add call.

**2026-08-03 to 2026-08-13**

PR #699 opened on August 3. The history includes 9ec4bda removing unrelated lockfile changes, 62a60a4 on August 4 updating ownership behavior, and 9bac5ae on August 13 adding the existing reflection. The original plan/journal are historical artifacts; later work is not backdated into them.

**2026-11-11**

The contribution story was still blank. Separate checkouts were created; the current tests exposed six owner-fixture errors, then cee1b85 corrected them and added the missing-profile control. The focused suite passed 21 tests and the follow-up was pushed to the existing branch. All new course tooling and verification in this story occurred on this date.

The official scout, voice guard, ship-gate and PR monitor were installed locally. The free scout search/rank and monitor-state stages worked. Direct no-body guard tests correctly stopped; the gate's three project checks passed, then the Claude precheck failed authentication. On being asked, I chose to continue with available evidence instead of claiming those model stages completed. I also confirmed instructor approval to reuse this older contribution; that does not change the dates of the original work.

**Toolkit**

- Tool: Git history, the older journal, fresh command captures and the official course runners.
- What it produced: separate dates for original work and catch-up work, plus the test-only follow-up commit.
- Difficulties: missing Units 5-7 tools and expired Claude OAuth could not be reconstructed as successful August runs.
- What changed: the timeline now distinguishes verified history, today's execution, historical claims and still-pending model work.

---

## Pull Request

**PR Link:** https://github.com/ascherj/pathreview/pull/699

**PR Description:** the posted description names #163, the ownership guard, inaccessible-profile rejection, prevention of add/commit and targeted tests. The older description is not a full current template/evidence/disclosure record. A proposed template-complete follow-up description and a reply draft are local only; they have not been posted while the voice workflow is unavailable.

**Maintainer Feedback:**

- 2026-11-11: none yet. The fetched PR timeline contains no comments or reviews.

**Status:** Awaiting review. The PR is open but still **draft**, with REVIEW_REQUIRED and no recorded review; it has not been marked ready or given a new review request. This is not a claim that a reviewer is waiting on my response.

The current head is [cee1b85](https://github.com/Damola-png/pathreview/commit/cee1b85cb80db439456d4488387d43f911c6e867). No PR comments were posted during this catch-up. The monitor's 100-day quiet figure is since activity by someone other than the author, not since the latest push.

**Toolkit**

- Tool: ship-gate with the existing pr-precheck, plus the PR monitor's free state mode and the four-section local playbook.
- What it produced: three successful manifest checks followed by an authentication stop, and actual PR status with no feedback. No final precheck verdict or monitor-generated draft exists.
- Difficulties: Claude OAuth is expired; only one merged PR appears in the target repo search, not the required five-sample response history.
- What changed: the playbook labels its 14-day threshold provisional, records #176's approximately 0.96-day open-to-merge interval as the only observation, and prohibits nudging the self-held draft. A future login is needed before model drafting or a voice-checked post.

---

## Learnings and Reflections

**Technical Skills Gained**

The useful skill here is tracing an authorization decision across layers: an authenticated ID reaching a function is not enough unless the function checks it before writing. The existing read behavior provided the 404 convention. The November fixture failure reinforced another concrete lesson: a success-path mock has to represent an authorized owner, not merely be a non-null object. Session methods and returned SQLAlchemy scalar results also have different async/synchronous boundaries.

**Challenges Overcome**

My original journal describes the challenge of following the repository's access/error conventions rather than writing the guard in isolation. The fresh audit added a measurable example: the earlier 20-pass account did not match the branch, and the correct response was to repair six owner fixtures and rerun, not repeat that account. Comparing main with the branch separated 40 remaining baseline failures from this contribution. Authentication remains unresolved; recording it honestly is not the same as overcoming it.

**What I'd Do Differently Next Time**

I would check the owner/error contract first, create valid-owner and cross-user fixtures together, and save the smallest failing command before implementing. I would install and authenticate the scout/voice/gate workflow before opening the PR, so its verdicts genuinely belong to the original timeline. I would keep personal journal files outside the upstream diff and write the complete testing/disclosure sections before asking for review. I would also confirm reuse eligibility before treating an earlier course contribution as the current assignment.

---

## Resources Used

- [Issue #163 and the maintainer's reproduction/ownership direction](https://github.com/ascherj/pathreview/issues/163#issuecomment-5037759852), checked against the actual service and route.
- [Original reproduction commit](https://github.com/Damola-png/pathreview/commit/13aa66480b8fd02bed5ed4e4a26f43efc2a7306e), rerun rather than cited as an unverified success.
- [Existing contribution journal](https://github.com/Damola-png/pathreview/blob/9bac5ae8bb731d928dde1bd2219dbaf1e155e649/JOURNAL.md) and [original plan](https://github.com/Damola-png/pathreview/blob/9bac5ae8bb731d928dde1bd2219dbaf1e155e649/PLAN.md), used to reconstruct the actual chronology and the earlier reflection.
- [Contributor guide](https://github.com/ascherj/pathreview/blob/main/docs/CONTRIBUTING.md), [Makefile](https://github.com/ascherj/pathreview/blob/main/Makefile), and [PR template](https://github.com/ascherj/pathreview/blob/main/.github/PULL_REQUEST_TEMPLATE.md), used for conventions and check sources.
- Official [Unit 5 scout materials](https://github.com/codepath/ai301-unit5-starter), [Unit 6 voice/memory materials](https://github.com/codepath/ai301-unit6-starter), [Unit 7 gate materials](https://github.com/codepath/ai301-unit7-starter), and [Unit 8 monitor materials](https://github.com/codepath/ai301-unit8-starter), installed and inspected during the catch-up.
- [PR #176](https://github.com/ascherj/pathreview/pull/176), the only merged PR returned by the response-history search. Its author badge is CONTRIBUTOR; one observation cannot establish five eligible external response times.

**AI assistance and remaining checks:** OpenAI Codex helped inspect sources, execute commands, repair the test fixtures and draft this retrospective story. Claude Code's attempted orientation and precheck stopped on expired OAuth. The scout's paid grading, actual voice evaluation, full monitor drafting and any new upstream text remain pending; no assistant-only work is represented as independently executed by the student. I must review this write-up in my own voice and restore the login before those remaining tool stages. Reuse approval was confirmed during this catch-up. The full portal grading rubric and exact submission deadline could not be retrieved by the available web access; this draft was checked against the supplied Unit 8 checklist, not claimed to be scored against an unseen rubric.
