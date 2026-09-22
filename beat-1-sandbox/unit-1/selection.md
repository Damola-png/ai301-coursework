# Unit 1 — Issue Selection

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72

**Verdict output**

Pending a live run of the installed issue-select skill. Do not present this manual assessment as the required verbatim skill output. The issue is open and specifies a concrete fix in `core/security.py` and a marked test in `tests/unit/test_security.py`. Before recording an `accept` verdict, verify repository activity, contribution policy, assignees, and linked pull requests with the live skill.

## Eval iterations

**Run history**

No evaluation run was completed in this workspace. The provided harness requires the Claude Code CLI, which is not installed here. Run the full harness with `--save-run eval-run.txt` and record its actual agreement line here.

**Issue analysis**

Pending the scored eval output. Record one actual scored issue id, this rubric's decision, the gold label, and the evidence that led to the decision. Do not substitute a prediction for an observed run.

**Check rationale**

"Repo is not archived and has either a release or a default-branch human commit within 365 days. A repo without formal releases may pass on commits alone; stars are not a minimum." This check guards against spending the first contribution on a dormant project while allowing active projects that do not publish formal releases.

**Trade-offs**

The 365-day threshold could admit a project with only one recent human commit but little review activity. It also avoids rejecting a small, actively developed project solely for lacking releases or stars.

## Selection rationale

**Selection rationale**

1. The issue involves a bounded Python authentication behavior and one existing unit test, which fits my Python and backend work and the stated one-to-two-hour estimate.
2. The rubric should recognize the named error, expected `False` result, files, and test as concrete scope. I also weigh the security behavior and whether I can personally understand the failing test before changing it.
3. I need to confirm the issue remains available and follow the course's Unit 2 claim instructions. I have not posted a claim comment.
