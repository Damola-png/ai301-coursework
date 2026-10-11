# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

In an eval bundle, read the plan-context scope pair, intended behavior, test plan and deviation notes alongside the candidate unified diff and description. In live mode read plan.md including Deviations, every hunk from `git diff main...HEAD`, and pr_draft.md; the issue thread establishes which issue the plan belongs to.

Good evidence pairs each delivered behavior/hunk with the original scope or an explicit issue-related deviation. A narrower delivery can be ready when the plan and description both identify the omitted behavior and the evidence proves the delivered portion. Unmentioned expansion, missing promised behavior or description claims contradicted by the diff are silent drift.

## Test evidence (harness category: not-tested)

In eval, pair the plan-context test plan and reproduction steps with the candidate test-evidence section and actual assertions in the diff. Repo-facts lists requested commands. In live, pair plan.md and the posted reproduction with test_evidence.md, changed tests and the template/CONTRIBUTING check list; compare these with Testing in pr_draft.md.

Good evidence records the actual trigger and observable before/after through the affected implementation, not only a test name or suite total. Each requested repo check has a command and observed outcome; a disclosed failure or expected empty suite is evidence of an attempt, not a green check. Missing output, an unrun proposal or a mock replacing the very boundary under repair cannot prove the fix. Relevant unchanged/error-path controls satisfy the preferred regression row.

## Diff quality (harness category: unreviewable)

In eval, inspect the full candidate diff and commit list against plan context and the issue. In live, inspect the entire committed three-dot diff, stat and commit list; uncommitted drafting files are not part of that diff.

Good means the fix and necessary supporting tests/dependencies/docs are visible and issue-bound. Debug prints, commented-out alternatives, dead scaffolding, scratch artifacts, drive-by cleanup and unrelated formatting hunks are observable debris. Several related files or commits are not independently a failure.

## Standards and comms (harness category: standards-wall)

In eval, repo-facts supplies the template asks, CONTRIBUTING instructions and stated AI policy; the issue thread supplies maintainer direction. Compare each ask with the candidate title and description. In live, read the target .github/PULL_REQUEST_TEMPLATE.md, docs/CONTRIBUTING.md, scope.md, issue thread and pr_draft.md; voice-guide.md supplies separately reported personal rules.

Good means every applicable explicit section and request has factual content or a reasoned N/A, the issue is named, and a required disclosure states tool and extent (or explicit no-AI use if a conditional eval policy permits it). A missing statement is not evidence of no AI. Never invent a template or policy for an eval repo that states none. Live course Notes for Reviewers always disclose AI assistance. Compare outcome claims with actual diff/tests under Faithful claims; polite writing alone never substitutes for compliance.
