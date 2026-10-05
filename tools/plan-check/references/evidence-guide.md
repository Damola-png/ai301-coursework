# Evidence guide: where evidence lives in a plan package

In eval mode, the sections named below belong to the frozen package and are the entire evidence set. In live mode, the plan and comment drafts must contain or quote/link the student's reproduction evidence; inspect only their scoped issue sources and repository guidance. Facts in unrelated local files do not repair an incomplete package.

## Diagnosis and grounding

Where it lives: `Issue`, `Thread highlights`, and `Repro evidence` supply the behavior, expected result, environment, observed artifacts, and controls. `Candidate plan` and `Candidate plan comment` state the proposed cause. Live equivalents are the issue body/full thread, the student's posted repro permalink and quoted evidence, and the diagnosis passages in the two drafts.

What good looks like: the proposed mechanism explains the exact reproduced trigger and result, including controls and platform constraints. A control that shows the failure without the suspected component contradicts blaming that component. A thread theory is evidence that someone proposed it, not proof that it is correct. A labeled hypothesis needs a concrete validation step before the implementation depends on it.

## Scope

Where it lives: the issue's requested outcome and maintainer direction; the candidate plan's scope, exclusion, files/areas, and approach; and any extra commitments in the candidate comment. In live mode read these in `plan.md`, `comment.md`, and the full issue thread.

What good looks like: every proposed operation serves one issue outcome and stays inside the stated boundaries. Several related files, a helper, tests, and docs can form one change. A claim of narrow scope does not excuse an actual redesign, additional feature, changed public semantics, or unrelated cleanup.

## Executability

Where it lives: the candidate plan's named code sites, operation descriptions, sequence, dependencies, fixture references, and unknowns. Read those against the issue and repro environment. Live equivalents are the corresponding draft passages and any source evidence they actually quote or link.

What good looks like: a contributor can name where to start and what operation to perform, with public/available inputs and a concrete direction. Exact line numbers or shell commands are not always needed. "Explore and fix it" without a starting mechanism, a required private file, or an unresolved core algorithm prevents starting the promised implementation.

## Test plan

Where it lives: `Repro evidence` steps, options, artifacts, timing measurements, and controls; the candidate plan's verification steps and expected after result; and the candidate comment's validation promise. In live mode compare the drafted test plan with the student's posted reproduction excerpt.

What good looks like: the proposed check exercises the original failure through the affected behavior, retaining decisive inputs/options and naming an observable passing result. Explicitly referencing supplied repro steps is enough. A performance test holds workload and relevant options fixed; a UI test names the changed visual behavior. A full suite alone does not show this issue is fixed. A neighboring control is useful regression coverage, not a substitute for the decisive test.

## Honesty

Where it lives: causal claims, risk/unknown/assumption passages, validation steps, and deviations in the candidate plan, plus assertions and promises in the candidate comment. Check them against the issue, thread, and repro artifacts. Live `plan.md` includes `## Deviations` after a build.

What good looks like: observations stay separate from hypotheses, material unknowns have bounded checks, and a deviation states what changed and why. A short plan need not invent risks not supported by its evidence. A confident statement contradicted by an artifact or an unacknowledged behavior change is a failing condition.

## Comms

Where it lives: `Thread highlights` gives maintainer direction; `Repo facts` gives template asks, contributor conventions, and AI-use policy; `Candidate plan comment` gives the outgoing text. Compare that text with the candidate plan and repro. Live sources are the full issue thread, README-linked contributor guidance, root/`.github/`/`docs/` CONTRIBUTING files, PR/issue templates, dedicated AI policies, `comment.md`, and the installed voice guide.

What good looks like: the comment communicates the contributor's own grounded cause, bounded change, and verification, rather than a generic promise or copied classmate approach. It follows explicit direction and stated posting conditions, including AI disclosure or human-authored-comment requirements when present. Silence creates no additional restriction. In scoped Path Review, other students' claim or plan comments do not block an independent plan. Personal voice rules are reported in live mode; eval does not read the voice guide.
