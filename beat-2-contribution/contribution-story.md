# Contribution: Review creation does not verify profile ownership

Path: `beat-2-contribution/contribution-story.md`

**Contribution Number:** [1]
**Student:** Adedamola Olayefun
**GitHub Username:** Damola-png
**Issue:** https://github.com/ascherj/pathreview/issues/163
**Pull Request:** https://github.com/ascherj/pathreview/pull/699

My instructor approved reusing this July-August contribution for Assignment 5. The AI301 tools were added on November 11, not before the original PR opened.

---

## Toolkit

**Unit 5: 2026-11-11 - setting up the scout**

The scout ran two free searches, returning 200 unique issues and 169 unassigned results. Its top two choices were generic news-agent tasks, not the API ownership work I wanted. Claude login failed before grading, so there is no scout verdict on #163. I would tighten the API/ownership ranking signals and check what gets excluded before using that strategy again.

**Unit 6: 2026-11-11 - voice rules and local repo notes**

The shared voice guide has rules for claims, questions and pings, with examples. A direct test confirmed that the guard blocks a comment with no body, but a full voice check still needs a working Claude login. The scoped Claude orientation also failed, so the local repo notes came from reading the relevant code and contributor guide directly.

**Unit 7: 2026-11-11 - checking the branch against main**

The manifest's three focused checks passed: review-service tests, changed-file lint and changed-file formatting. Full-repo checks were kept in a dated exclusion table because their failures also exist on main. The gate then stopped at the Claude precheck, so it did not give a final pass.

**Unit 8: 2026-11-11 - checking the PR without sending a nudge**

The monitor read the PR's state and found no reviews, comments or reported CI checks. The playbook uses a provisional 14-day wait because the repo search returned only one merged PR, not five response-time samples. No monitor draft was generated while Claude login was unavailable. The playbook also says not to nudge a PR that I am still holding as a draft.

---

## Why I Chose This Issue

The July checklist identified one clear question: could a user create a review for a profile they did not own? This fit my Python/FastAPI experience and gave me a way to practice following an API request into a service. The existing get_review and list_reviews functions already checked ownership, so there was an example to follow.

The original choice came from that checklist, not the November scout. A fresh contributor should not read this as an available issue: there are already claims and PRs. I am continuing my existing contribution, and the maintainer's thread explicitly allows existing claimants to continue.

**Claim comment:** claim pending (2026-11-11). No comment by Damola-png was found in the issue's 17-comment thread. A current-date continuation draft is prepared locally; it is not posted or backdated.

**Toolkit**

- Tool: issue-scout, with the existing fit profile, two GitHub searches and written ranking rules.
- What it produced: 200 unique results and two ungraded candidates, not an accept verdict.
- Difficulties: the ranking favored unrelated tasks, and Claude login blocked grading.
- What changed: the scope check now reports difficulty for open edges instead of rejecting them; the sandbox's automatic claim exception was removed. The patched rubric has not had a new regression eval.

---

## Understanding the Issue

**Problem Description**

POST /reviews passes the authenticated user ID to create_review. The original service did not use it to check who owned the profile.

**Expected Behavior**

A user can create a pending review for their own profile. Another user's profile, or a missing profile, must be rejected before any database write.

**Current Behavior**

The original reproduction reaches db.add() for another user's profile. The fixed service looks up the profile, compares its user_id with the caller, and returns 404 with "Profile not found" when the profile is unavailable.

**Affected Components**

The route is in api/routes/reviews.py. The guard is in core/services/review_service.py. Profile ownership comes from core/models/profile.py, and the tests are in tests/unit/test_review_service.py.

**Toolkit**

- Tool: source inspection, checked against the original plan.
- What it produced: the route-to-service path and the required 404/no-write behavior.
- Difficulties: service tests with session mocks do not prove a deployed HTTP flow.
- What changed: the explanation now says the service looks up the profile by ID and then compares its owner. It does not claim the SQL query filters on both IDs.

---

## Reproduction Process

**Environment Setup**

The November checks used Windows PowerShell, Python 3.12.14, pytest 9.1.1, SQLAlchemy 2.0.54 and FastAPI 0.142.2. Ruff was 0.16.10, Black 26.10.0 and mypy 2.4.0.

The existing development environment was reused. Separate checkouts kept the reproduction and main unchanged. Make was unavailable, so the underlying Python tools were called directly. A first restricted invocation stalled; it was stopped and rerun with the required filesystem access.

A new setup needs the project's Python development dependencies. These runs used C:/Users/damol/OneDrive/Documents/pathreview/.venv/Scripts/python.exe.

**Steps to Reproduce**

1. Check out reproduction commit 13aa66480b8fd02bed5ed4e4a26f43efc2a7306e.
2. Run the cross-user test below. It calls the real service with a mocked database session and checks that no review is added.
3. Run the same selector on the fixed branch and compare the result.

```powershell
& 'C:/Users/damol/OneDrive/Documents/pathreview/.venv/Scripts/python.exe' -X utf8 -m pytest tests/unit/test_review_service.py::TestReviewService::test_create_review_rejects_profile_owned_by_another_user -q --tb=short
```

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/Damola-png/pathreview/commit/13aa66480b8fd02bed5ed4e4a26f43efc2a7306e
- **Screenshots/logs:** the November rerun at that commit reported `Expected 'add' to not have been called. Called 1 times.` It ended with `1 failed, 1 warning in 5.86s`.
- **My findings:** the fixed-branch selector reported `1 passed, 1 warning in 5.06s`. The current test also checks 404, "Profile not found", no add and no commit. This is a service reproduction, not a two-user HTTP integration test.

**Toolkit**

- Tool: pytest and separate Git checkouts.
- What it produced: a failing no-write assertion before the fix and a passing result after it.
- Difficulties: the old journal was not enough to establish today's behavior.
- What changed: the actual reproduction commit was rerun and the output saved.

---

## Solution Approach

**Analysis**

The create path accepted user_id but did not use it before writing. The read paths already checked Profile.user_id. Their 404 behavior was the convention to follow.

**Proposed Solution**

Fetch the profile and compare its owner with the authenticated user before constructing or adding a Review. Keep valid-owner reviews pending. Do not change schemas, dependencies, unrelated services or baseline bugs.

**Implementation Plan**

1. Trace profile_id and user_id from the route into the service.
2. Check ownership before add/commit.
3. Test valid-owner creation, cross-user rejection and a missing profile.
4. Rerun the original reproduction and the review-service suite.
5. Compare full-repo check failures with unchanged main.

**Deviations**

The August PR included PLAN.md and JOURNAL.md as well as the two Python files. After feedback, those personal notes were archived outside the fork and removed from the current diff in c70b13d. Their original history is still available.

The November run also found six failing success tests. Their mock profiles had no user_id matching the caller. Commit cee1b85 fixed those fixtures and added a missing-profile control; it did not weaken or change the production guard.

**Toolkit**

- Tool: the original plan, source comparison and targeted tests.
- What it produced: a small pre-write ownership check and tests for both allowed and rejected requests.
- Difficulties: the journal said 20 tests passed, but a fresh run found 6 failed and 14 passed.
- What changed: the fixtures were repaired. The personal notes were later removed from the PR rather than left for the reviewer to sort through.

---

## Testing Strategy

The tests must distinguish an owned profile from another user's profile. Rejection must happen before add/commit, and the valid-owner path must still create a pending review. The missing-profile case checks the same 404 contract.

The full-repo comparison used main d5f196d and the Python changes at cee1b85 on November 11. The later cleanup changed only the personal notes; Git confirmed the two Python files were unchanged.

The commands below are arguments to the same Python interpreter used above.

| Check | Command arguments | Main | Tested branch |
|---|---|---|---|
| Focused service suite | `-m pytest tests/unit/test_review_service.py -q --tb=short` | Not used as the before reproduction | 21 passed |
| Full unit suite | `-X utf8 -m pytest tests/unit -q -m unit --tb=short` | 53 failed, 375 passed | 40 failed, 390 passed |
| Full Ruff | `-m ruff check . --output-format concise` | 182 findings, exit 1 | 173 findings, exit 1 |
| Full Black | `-m black --check .` | 52 files would reformat, exit 1 | 50 files would reformat, exit 1 |
| Full mypy | `-m mypy api/ core/ ingestion/ rag/ agent/ safety/` | 103 errors in 26 files | 103 errors in 26 files |
| Integration | `-m pytest tests/integration -v -m integration` | Not rerun | 0 collected, exit 5 |
| Changed-file Ruff | `-m ruff check core/services/review_service.py tests/unit/test_review_service.py` | Not claimed | Pass |
| Changed-file Black | `-m black --check core/services/review_service.py tests/unit/test_review_service.py` | Not claimed | 2 files unchanged |

All 40 failing branch test IDs also failed on main. Comparing file names, messages and error codes found no new Ruff or mypy findings. This does not turn the full checks into passes. Zero integration tests is not integration coverage.

After the notes cleanup, the focused suite was rerun and saved: `21 passed, 1 warning in 3.35s`. No CI success is claimed for #699; the green checks on sandbox PR #137 are unrelated.

**Toolkit**

- Tool: pytest, Ruff, Black, mypy and the ship manifest.
- What it produced: before/after output and baseline-versus-branch results.
- Difficulties: the repo has existing failures, and whole-repo formatting would add unrelated changes.
- What changed: focused checks stay required. Full checks remain visible with dated reasons for their exclusion.

---

## Implementation Notes

**2026-07-21**

Commit 9794364 records the original selection checklist in JOURNAL.md. This was before the AI301 scout was installed.

**2026-07-26**

Commit 13aa664 adds the failing ownership reproduction. The plan follows in 1f55a1c and aa76df5.

**2026-08-03 to 2026-08-13**

PR #699 opened on August 3. Later commits removed lockfile noise, updated ownership behavior and added the earlier reflection. Those are historical events, not November tool runs.

**2026-11-11**

The story was still blank. A fresh run found the six fixture failures. Follow-up cee1b85 repaired them, added the missing-profile control and passed 21 focused tests.

The scout, voice guard, ship-gate and monitor were installed. Free search/ranking and monitor-state checks worked. The gate's three project checks passed, but Claude login blocked the model stages. I chose to continue with the available evidence and confirmed instructor approval to reuse the earlier PR.

**2026-11-11 - after grading feedback**

The feedback awarded 204/250. Its deductions were for the missing claim, disclosure/evidence in the PR description, and personal notes in the PR.

PLAN.md and JOURNAL.md were copied outside the fork and hash-checked before removal. Cleanup c70b13d leaves only the service and tests in the current PR diff. A shorter description with captured output, AI disclosure and a current-date continuation claim was prepared locally. The voice guard still could not run, so neither text was posted.

These are follow-up improvements. They do not change what the PR contained when it opened, and no revised grade is assumed.

**Toolkit**

- Tool: Git history, saved command output and the grading feedback.
- What it produced: a dated record that separates the original work, the November repair and the later cleanup.
- Difficulties: the missing tool runs cannot be reconstructed as earlier successes.
- What changed: the record keeps the failed checks and dates, but uses shorter explanations.

---

## Pull Request

**PR Link:** https://github.com/ascherj/pathreview/pull/699

**PR Description:** the posted body still has the older summary and general test claims. The revised body prepared locally adds the exact reproduction, observed output, check results and AI disclosure. It is not yet published because the voice check cannot run.

**Maintainer Feedback:**

- 2026-11-11: none yet. The PR timeline checked during the audit had no comments or reviews. The grading feedback above is course feedback, not a maintainer review.

**Status:** Awaiting review. The PR is open but still draft, with REVIEW_REQUIRED and no recorded review. It has not been marked ready or sent a new review request.

The latest cleanup is [c70b13d](https://github.com/Damola-png/pathreview/commit/c70b13d6ea6ec2ef907f722e20bc0fb1c5508a55). The current diff contains only core/services/review_service.py and tests/unit/test_review_service.py. The original PR had four files; that history is not being rewritten.

**Toolkit**

- Tool: ship-gate, pr-precheck, the monitor's free state mode and the local playbook.
- What it produced: three passing project checks and an authentication stop, not a final precheck verdict or monitor draft.
- Difficulties: Claude login is unavailable, and the repo has only one merged PR in the response-time search.
- What changed: the playbook's 14-day wait is provisional and does not permit nudging a self-held draft. Claim and PR text stay private until reviewed and checked.

---

## Learnings and Reflections

**Technical Skills Gained**

Passing an authenticated user ID into a function is not enough. The function must use it before writing. The fixture failure also taught me to give success and rejection cases explicit owner IDs. A non-null mock is not automatically a valid owner.

**Challenges Overcome**

The hardest part in my earlier journal was matching the repo's access and error conventions. The fresh run exposed another problem: the saved 20-pass claim did not match the branch. Fixing six fixtures and comparing main with the branch gave a result I could support. Claude login is still unresolved; documenting that is not the same as fixing it.

**What I'd Do Differently Next Time**

I would build valid-owner and cross-user fixtures together, run the smallest test first and save its output before recording a result. I would check every file in the PR before opening it and keep personal notes outside the fork. I would also finish the testing evidence and AI disclosure before asking for review.

---

## Resources Used

- [Issue #163 and the maintainer's reproduction](https://github.com/ascherj/pathreview/issues/163#issuecomment-5037759852).
- [Original reproduction commit](https://github.com/Damola-png/pathreview/commit/13aa66480b8fd02bed5ed4e4a26f43efc2a7306e), rerun for the before result.
- [Historical journal](https://github.com/Damola-png/pathreview/blob/9bac5ae8bb731d928dde1bd2219dbaf1e155e649/JOURNAL.md) and [plan](https://github.com/Damola-png/pathreview/blob/9bac5ae8bb731d928dde1bd2219dbaf1e155e649/PLAN.md), used for the chronology and earlier reflection.
- [Contributor guide](https://github.com/ascherj/pathreview/blob/main/docs/CONTRIBUTING.md), [Makefile](https://github.com/ascherj/pathreview/blob/main/Makefile) and [PR template](https://github.com/ascherj/pathreview/blob/main/.github/PULL_REQUEST_TEMPLATE.md).
- Official [Unit 5](https://github.com/codepath/ai301-unit5-starter), [Unit 6](https://github.com/codepath/ai301-unit6-starter), [Unit 7](https://github.com/codepath/ai301-unit7-starter) and [Unit 8](https://github.com/codepath/ai301-unit8-starter) materials.
- [PR #176](https://github.com/ascherj/pathreview/pull/176), the only merged PR returned by the sample search. Its roughly 0.96-day open-to-merge time is one observation, not a five-PR average.
- Assignment 5 feedback supplied in this chat, used to identify the four scored gaps.

**AI assistance and remaining work:** OpenAI Codex helped inspect the code, repair fixtures, run commands and draft this story. Claude's orientation, precheck and voice check did not complete. Scout grading, a full monitor draft and upstream text remain pending. I still need to review this draft in my own words. The full portal rubric and exact deadline could not be retrieved; no unseen rubric check or portal submission is claimed.
