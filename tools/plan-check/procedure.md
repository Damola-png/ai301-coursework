# Procedure: how this skill grades a plan package

## Read order

1. Identify live or eval mode. In live mode, read `scope.md` first, verify the issue repository exactly, then read `voice-guide.md`. Stop before grading for a placeholder or out-of-scope repo. In eval mode, ignore both files and never fetch live sources.
2. Read `rubric.md` and `references/evidence-guide.md`; list every check, its weight, evidence locations, and the verdict rule. Stop if the checks or procedure are empty.
3. Read the issue and repo facts, then thread highlights (the full thread and linked contributor guidance in live mode). Record the requested behavior, explicit maintainer direction, and actual posting requirements before considering the candidate's claims.
4. Read the repro evidence before the candidate plan. Record the environment, trigger, observed artifact, expected result, and any control that rules a theory in or out. In live mode locate the student's own posted repro and the passages the drafts quote or link; another student's repro is not a substitute. An instructor-provided house pack is usable only when the drafts quote its relevant evidence.
5. Read the entire candidate plan, including scope, operations, tests, assumptions, and any deviations. Read the entire candidate plan comment last. Compare each one's commitments with the source facts recorded first; do not let confident wording overwrite an observation.

## Evidence gathering

1. Diagnosis and grounding: pair each proposed cause with the exact repro result and relevant control. Record agreement, contradiction, or a stated hypothesis plus its validation step.
2. Scope: map each proposed operation and promised behavior to the issue's goal. Record named code areas and explicit boundaries; flag unrelated work and a narrow-scope claim whose operations are actually broad.
3. Executability: record the starting file/function/area, the concrete change, necessary dependencies or fixtures, and the next action. Record missing information that a contributor would need to invent before starting.
4. Test plan: match the proposed check to the original trigger and relevant environment/options. Record the expected after result and how it will be observed. Separately record any neighboring control or regression coverage.
5. Honesty: compare stated certainty with what the repro actually proves. Record material assumptions, their validation steps, and whether deviations describe the change and reason rather than only asserting success.
6. Comms: compare the draft comment with the plan, repro, maintainer direction, and every explicit contribution condition. In live mode inspect contributor guidance linked from the README, templates, and any dedicated AI policy, and check the student's voice rules separately.
7. Use only evidence in the eval bundle, or contained/quoted/linked in the live drafts and their scoped issue sources. Do not rescue an incomplete draft with unquoted logs or code found elsewhere in the working directory. Read the named source before calling evidence absent.

## Check execution

1. Execute the rubric rows in table order: Evidence-grounded diagnosis, Cause-directed change, Bounded scope, Executable approach, Decisive verification, Honest uncertainty, Thread and conventions, then Regression coverage.
2. For each row, compare the gathered facts with that row's pass condition. Set `pass` when the condition is met and cite a deciding fact; set `fail` for a specific contradiction or unmet requirement; set `unclear` when a necessary fact remains absent after its source was examined.
3. Quote the relevant source text or give an exact observable fact in one evidence line per grade. Name both sides when a plan contradicts a repro control, comment, or maintainer direction.
4. Re-read the local passage if the gathered facts do not settle a check. In eval mode never use today's GitHub to fill a gap. In live mode re-check the source request if an API/page failed and disclose any remaining access limitation.
5. Continue through every check even when a required failure has already settled the verdict. Do not add requirements for prose length, section counts, labels, or exact line numbers.
6. In live mode report each violated voice-guide rule with its wording in the summary. Voice rules do not independently alter the rubric verdict; Thread and conventions reads actual stated posting conditions.

## Verdict assembly

1. Apply the verdict rule exactly: all required checks must pass; a required fail or unclear means reject; preferred checks do not gate acceptance.
2. Before emitting the verdict, verify that every rubric row has one grade and evidence line, and that the deciding failure names the source fact that caused the hold.
3. Write a short readable summary of the check results and deciding condition. State any genuine evidence or procedure gap; do not silently invent missing steps or facts.
4. Emit the single fenced JSON object from `SKILL.md`, with the issue URL in live mode or package id in eval mode, the complete checks array, and verdict `accept` or `reject`. Write nothing after that block.
