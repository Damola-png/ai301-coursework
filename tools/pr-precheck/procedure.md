# Procedure: how this tool grades a PR package

## Read order

1. Determine mode. In live mode perform SKILL.md's scope check first, then read the voice guide; in eval mode use only the bundle.
2. Read the rubric and evidence guide, then the full issue and thread and repo requirements. Record explicit maintainer instructions, template sections, required checks and disclosure conditions. Do not convert a silence into a policy.
3. Read the posted plan before the candidate: list intended behaviors, permitted files/operations, out-of-scope behavior, test scenarios, and explicit deviations. For a house chain use the supplied house plan and reproduction.
4. Read all candidate commits and diff hunks, then test evidence, then title/description. In live mode collect `git diff main...HEAD`, `git diff main...HEAD --stat`, `git log main..HEAD --oneline`, `git branch --show-current`, and `git rev-parse main HEAD`. Read the named local files and fetch the full issue thread and target-repo documents. If a read fails, record the unavailable source, do not reconstruct it.
5. Compare the outgoing live text with the voice guide and record any broken rule with its exact wording. Keep these observations separate from rubric grades unless a rubric condition also applies.

## Evidence gathering

1. Plan fidelity: pair each changed hunk and delivered behavior with the plan or an explicit deviation. Separately list planned behaviors absent from the diff and look for their disclosure in both plan and description. Record unrelated changes even if explained.
2. Test evidence: make a scenario ledger with trigger, failing-before result, expected-after, actual-after and exercised implementation. Pair it with the effective test plan after explicit narrowing. Distinguish real boundary/handler execution from a mock of the affected behavior. Pair every repo-requested check with its exact invocation, output/outcome, limitation and PR checkbox. Use N/A only with an applicable reason.
3. Diff quality: inspect all hunks and the commit list for unnecessary edits, debug statements, dead/commented code, temporary files and formatting churn. Necessary fixtures, regression tests and dependencies are not debris merely because they add files.
4. Standards and claims: pair every template/policy/maintainer ask with the relevant candidate text. Pair each title/body scope and success claim with the actual diff and outputs. Record AI disclosure only when explicitly required in eval; live course mode always requires tool and extent. Record fork, branch and existing-own-PR compliance in live mode, using an issue-thread or GitHub search if needed.

## Check execution

1. Execute every row in rubric order, using the gathered pairings. Assign pass only after naming the deciding fact; fail when the pass condition is contradicted or required evidence is absent; unclear when an unavailable source prevents verification.
2. Re-read a source only to resolve a specific mismatch; do not import unstated requirements, fetch outside an eval bundle or inspect the answer key. Where the procedure has no step for a necessary action, report the gap rather than improvising.
3. Check honest narrowing against both plan and description before treating a missing original scenario as drift. Do not let a disclosure excuse unrelated edits, nonexistent proof or an unsupported all-green claim.
4. Emit an evidence line for each row, including preferred rows. Quote a relevant trigger, assertion, hunk or unmet ask rather than adjectives. Retain negative outcomes and source-read limitations.

## Verdict assembly

1. Apply the rubric's full verdict rule: all required pass means accept; any required fail or unclear means reject. Preferred rows never gate the verdict.
2. For reject, quote the deciding evidence of the first non-passing required row in rubric order in the summary; list other failures too. For accept, summarize the delivered behavior and any disclosed limitations without inventing conditional acceptance.
3. Use the supplied bundle ID or PR URL as item; for a live unopened draft use the target repo and issue draft ID defined in SKILL.md. Print all row grades, then the exact contract JSON as the last fenced block, with nothing after it.
