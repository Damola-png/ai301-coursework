# Rubric: first contribution issue selection

Measure dates relative to the bundle's capture date in eval mode and today's date in live mode. Evaluate only the named evidence. A `good first issue` label is context, not a substitute for a check.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer activity | Repo facts: archived flag, last five default-branch commit dates and authors, last push, latest release, maintainer first-response sample; live equivalents in `references/evidence-guide.md`. | Repo is not archived, and at least one human-authored default-branch commit or release is within 365 days. A bot merge of a human PR counts as human activity. If dates are absent, a maintainer reply in the issue within 180 days can establish activity. Do not require every issue to get a maintainer reply. | required |
| Repo in use | Repo facts: archived flag, last push, latest release, last five default-branch commits; live repo front page and Releases. | Repo is not archived and has either a release or a default-branch human commit within 365 days. A repo without formal releases may pass on commits alone; stars are not a minimum. | required |
| Bounded contribution | Issue body and maintainer comments in the bundle, or live issue body and thread. | The request names a specific behavior, document, UI element, or output and an observable result, or a maintainer has narrowed it to one change. Reject a support question, explicit umbrella or tracking issue, codebase-wide migration, unresolved design debate, or a feature wish lacking an observable target or acceptance behavior. An old issue can pass when a maintainer has explicitly reopened it to new takers and the scope is still specific. | required |
| Available to start | Repo facts for assignees and linked PR state; issue comments for claim dates and maintainer responses. In live mode inspect the Assignees and Development panels and thread. | No open linked PR and no active assignee or active claim. A claim is stale if more than 180 days old without follow-up, or a maintainer explicitly invites new takers. A closed unmerged PR alone is not an active claim. In scoped Path Review live mode, apply `scope.md` house rule: student claim comments do not block the issue. | required |
| AI contribution permitted | Repo facts contribution-policy line; in live mode `CONTRIBUTING.md`, `.github/CONTRIBUTING.md`, dedicated AI policy and linked contributor guidance. | No explicit ban on AI-generated code or documentation. Silence passes. Disclosure, review, understanding, or testing conditions pass if the contributor follows them. | required |
| Testable path | Issue body and thread for reproduction steps, named files, example inputs and outputs, or a docs location; live equivalents. | At least one concrete reproduction step, expected output, affected file/page, or acceptance example is present. | preferred |

## Verdict rule

Accept only when every required check passes. Reject when any required check fails or is unclear after examining its named evidence. Preferred checks never alter the verdict; use them to rank accepted issues, followed by the fit profile in `scope.md` in live mode. Report evidence for every check and output `accept` or `reject` in the skill's required JSON format.
