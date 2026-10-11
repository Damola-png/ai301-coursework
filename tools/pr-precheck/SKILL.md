---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Decide exactly one question about one PR package: is this ready to submit? Read the candidate title, description, commits, diff, and test evidence against its posted plan and the issue that plan belongs to. Do not grade the attractiveness of the proposal or promise that the change will merge.

## Inputs and modes

Use eval mode when supplied a frozen package bundle. Treat its issue/thread, repo facts, plan context, candidate PR, and test evidence as the whole world. Read no other artifacts and fetch nothing. Ignore scope.md and voice-guide.md. Grade every rubric row and apply the entire verdict rule.

Otherwise use live mode. From the fork's working-copy root read plan.md including Deviations, pr_draft.md (first line is the title; the rest is the description), and test_evidence.md. Read the entire committed branch diff produced by `git diff main...HEAD`, its stat, commit list, branch name, and base/head IDs. Do not substitute unstaged changes or a two-dot diff. Fetch the issue's complete thread, the target repo's .github/PULL_REQUEST_TEMPLATE.md and docs/CONTRIBUTING.md, and any policy those documents explicitly reference. A house-chain student supplies the assigned house issue, plan, reproduction pack, and branch instead; apply the same checks.

A missing artifact is missing evidence, not permission to invent a result. If a source cannot be retrieved, name it and grade the affected check unclear. Never run or edit code, post comments, or open a PR as part of grading. Before a PR URL exists, identify the draft as `draft:<target-repo>#<issue-number>`.

## The scope seam (live mode only)

Read scope.md before any other live input. Stop without grading if its Repo line is an unfilled placeholder; tell the user to fill it with the section's Path Review repo. Stop without grading if the issue or proposed PR targets another repo. Do not infer the target from the fork's origin. Read and enforce the scoped house rules when assessing live standards. Eval mode ignores this file entirely.

## The voice seam (live mode only)

After the scope check, read voice-guide.md and compare every outgoing claim in the title and description with its rules. In the summary quote the exact broken rule and the draft phrase that breaks it. Voice findings alone never alter the verdict: only an applicable rubric check can do that. Eval mode ignores this file entirely.

## Component reads

Read rubric.md for the checks and verdict rule, references/evidence-guide.md for evidence locations, then procedure.md for the execution steps. Refuse to grade if rubric.md or procedure.md has no authored content; template comments and empty table rows do not count. Report a missing component or a procedural gap rather than inventing substitute checks or steps. Execute the authored procedure in order.

## Verdict and output

Use only accept (ready to submit) or reject (hold). Present a per-check summary with a deciding fact or quote for every row, including preferred rows, and identify the first non-passing required check when holding the PR. Disclosed reservations belong in evidence, not in a third verdict. End with one valid fenced JSON block matching the schema below exactly: preserve field order, include each check once, and put nothing after it.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

Name evidence before assigning each grade. Read behavior, scope, and repo requirements, not length, tone, confidence, commit count, or file count. Apply a check's written condition even if intuition disagrees; revise the rubric later, never during a run. Follow the procedure and report gaps. Distinguish evidence that a check failed from absence of evidence that it ran. Do not infer AI non-use from silence or demand unstated policy. Use the rubric's explicit unclear treatment; if a future rule is silent, unclear blocks acceptance.
