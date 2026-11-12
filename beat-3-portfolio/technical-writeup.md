# Opening a profile-ownership fix for PathReview

> AI-assisted sample draft based on saved project evidence. Rewrite the narrative in your own words before submitting.

## Overview

I opened a fix for review creation in PathReview, a Python/FastAPI application that reviews portfolios. The service accepted an authenticated user ID but did not check whether that user owned the requested profile before creating a review. My contribution adds that check and tests the allowed and rejected paths. A later verification found that six success-test fixtures were wrong, so I repaired them rather than weaken the guard. The focused suite now passes 21 tests. As verified on November 12, [PR #699](https://github.com/ascherj/pathreview/pull/699) is still an open draft, not a merged or deployed change.

## What happened

### The original reproduction showed an unauthorized write

The contribution began in July. The route passed both the requested profile ID and the authenticated user ID into create_review, but the service did not use the latter to authorize the write. The read paths already checked profile ownership.

The original regression was committed as `13aa664` on July 26. It called the real service with a mocked session and asserted that another user's profile must not cause a write. The following excerpt is from a November 11 rerun of that original commit, not a claim that a new live HTTP test ran in July.

Command, from the reproduction checkout:

```powershell
& 'C:/Users/damol/OneDrive/Documents/pathreview/.venv/Scripts/python.exe' -X utf8 -m pytest tests/unit/test_review_service.py::TestReviewService::test_create_review_rejects_profile_owned_by_another_user -q --tb=short
```

Observed output:

```text
mock_db_session.add.assert_not_called()
AssertionError: Expected 'add' to not have been called. Called 1 times.
1 failed, 1 warning in 5.86s
```

That failure showed the specific mistake: the service reached add() when it should have rejected the request.

### The guard went before review creation

The fix looks up the profile and compares its owner with the authenticated user before constructing a Review. An absent profile and an owner mismatch both receive 404 with "Profile not found", following the existing inaccessible-resource convention.

This excerpt comes from the actual service diff:

```diff
+    stmt = select(Profile).where(Profile.id == profile_id)
+    result = await db.execute(stmt)
+    profile = result.scalars().first()
+
+    if profile is None or profile.user_id != user_id:
+        raise HTTPException(
+            status_code=status.HTTP_404_NOT_FOUND,
+            detail="Profile not found",
+        )
+
     review = Review(
```

The check belongs before the write. A valid owner should still get the normal pending review; changing that behavior would not solve the authorization problem.

The PR opened on August 3. The next important revision came during the November audit.

### A fresh run contradicted the old journal

The saved journal reported 20 passing review-service tests. On November 11, the existing branch instead produced:

```text
6 failed, 14 passed, 1 warning in 6.45s
```

All six failures were supposed to exercise valid-owner behavior, but their mocked profiles had no user ID matching the caller. The guard correctly rejected that setup. Returning success for an unspecified mock would have weakened the very condition the fix needed to enforce.

Commit `cee1b85` changed those fixtures. One actual hunk shows the important difference:

```diff
-        mock_db_session.execute = AsyncMock(return_value=self._mock_execute_result(first=Mock()))
+        mock_db_session.execute = AsyncMock(
+            return_value=self._mock_execute_result(first=Mock(id=profile_id, user_id=user_id))
+        )
```

The same follow-up added a missing-profile control. Both rejection cases assert 404 and verify that add/commit do not run. The success cases now state the ownership condition explicitly.

### The next run passed, but the broader limits stayed visible

The same cross-user selector passed on the fixed branch: `1 passed, 1 warning in 5.06s`. After the later notes-only cleanup, the full focused suite was rerun:

```powershell
& 'C:/Users/damol/OneDrive/Documents/pathreview/.venv/Scripts/python.exe' -X utf8 -m pytest tests/unit/test_review_service.py -q --tb=short
```

```text
.....................                                                    [100%]
21 passed, 1 warning in 3.35s
```

That is the result of the fixture repair: the owner controls and rejection tests pass without changing the production guard.

The full project was not green. On the same environment, unchanged main had 53 failing unit tests and the tested branch had 40; all 40 branch failures also appeared on main. Ruff, formatting and type checks also retained baseline findings. I kept those outcomes separate from the focused success.

Course feedback then identified personal PLAN.md and JOURNAL.md files in the PR. They were preserved outside the fork and removed in `c70b13d`. The current diff contains only the service and its test file. This cleanup did not change either Python file or rewrite the original PR history.

## Limitations and what's next

These are service-level tests with mocked session results. They show the pre-write decision and expected calls, but they do not prove an authenticated two-user HTTP flow or a live database integration. The integration folder collected zero tests, so its exit code 5 is not a pass.

The PR remains a draft with no maintainer review recorded at the November 12 check. There is no merge or deployment outcome to claim. The next validation step is an authenticated test using two users and a real test database, covering both cross-user rejection and valid-owner creation.

Before requesting review, I also need to finish the PR description and the pending voice-check workflow. Next time, I would build the owner and non-owner fixtures together, save output when it runs, and inspect every changed file before opening the PR.

OpenAI Codex assisted with source inspection, fixture repairs, command execution and preparation of this sample draft. This is not a claim that I independently executed every command or wrote this draft without assistance.
