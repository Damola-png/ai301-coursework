# Unit 4 - Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

## Your pull request

**Pull request**

[Issue #61 PR](https://github.com/codepath/pathreview-ai301-fa26-s1/pull/137)

Opened October 11, 2026 at `2026-10-11T22:00:19Z`.

The PR targets `codepath/pathreview-ai301-fa26-s1:main` from `Damola-png/pathreview-ai301-fa26-s1`. Its description, including before/after and check outputs, is preserved in `pr_draft.md`; full captured evidence is in `test_evidence.md`.

**Branch**

`fix/61-sqlalchemy-health-check`

Tested head: `14792e0cd6f28b62abb4b52646407442eaa2a5da`.
Base: `f89c06fc3ff292df2a04a39ac51319d32a76b779`.
The committed diff contains only `api/routes/health.py`, `pyproject.toml` and `tests/unit/test_health.py`. The Unit 4 marker/comment changes are recorded in `../unit-3/plan.md` under Deviations. Drafting files were not committed to the fix branch.

## Eval iterations

**Run history**

One full scored run, October 11, 2026, using the official Unit 4 harness and its pinned Sonnet model. No partial evals, canary retries or post-eval skill revisions were run. The four calibration packages were not scored.

From the official materials clone's `eval/` directory:
```powershell
& 'C:\Users\damol\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe' -X utf8 run_eval.py --skill 'C:\Users\damol\.claude\skills\pr-precheck' --save-run eval-run.txt --out unit4-full-1.json
```

Full run 1, quoted from the submitted harness-written transcript:
```text
categories: clear-accept 5/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3
agreement: 18/20 scored items  (bar: 18/20: PASS)
```

That reaches 18/20 and at least one match in every category. The transcript header records `2026-10-11T21:42:23Z`; its four file fingerprints match the uploaded frame, rubric, procedure and evidence guide. The saved file was copied without hand-editing.

Live prechecks are separate from these scored harness runs: the first held because read-only permissions blocked the combined Git commands and the complete thread was unavailable. The second accepted after separate read-only Git commands and the complete GitHub-fetched issue thread. Publishing then revealed that the original fork was in the ascherj/pathreview network, not the cohort network. The old fork was preserved and the exact tested head was pushed to a new fork of the cohort repo. The third live run accepted the corrected plan/draft and verified Makefile equivalents; it is the final precheck. All three live outputs are retained, rather than described as scored eval retries.

**Package analysis**

`pkg-08`: the skill decided `reject`; the gold label is `accept`. Only Repo check evidence failed. The package fixes the untracked-only stash prompt by checking tracked changes first; its diff, toast observation, retained untracked file and tracked-change control support the bounded plan.

The repo-facts template asks for "code formatted". The description says "code formatted", but the test-evidence section records the integration test, `go test ./...` and `go generate ./...`, not a formatting command or outcome. The grader's exact deciding line was:

> Template asks for formatted code; only the description says 'code formatted', and test evidence has no formatter command or outcome (go generate, go test and the integration test are recorded).

The submitted row treats a requested check's recorded attempt/outcome as necessary and does not accept that checkbox assertion alone. Gold accepts the package, so this is a conservative false rejection, not proof that the stash implementation is wrong. The full result table preserves the disagreement.

**Check rationale**

Current Repo check evidence row, quoted exactly from `tools/pr-precheck/rubric.md`:

```markdown
| Repo check evidence | Repo-facts required checks or live template/CONTRIBUTING check list, paired with commands, recorded output/outcomes and description checkboxes. | Every explicitly requested applicable repo check has a recorded attempt and outcome. An equivalent invocation must be explained. An honestly reported failure or expected no-tests result can pass this evidence check when its reason and remaining limitation are disclosed; an omitted check or unsupported all-green claim cannot. Do not impose checks the repo never asks for. | required |
```

This separates missing attempts from observed failures. A check that actually ran and failed, with its outcome and limitation disclosed, is evidence; an unchecked command or unsupported "all green" statement is not. This is important for Path Review's empty integration directory and for the local frontend timeout: both results are disclosed in the PR rather than ticked as passes. The equivalent-invocation clause lets the Windows Python entry points implement the Makefile without pretending make was installed. The last sentence avoids imposing Path Review's four commands on frozen packages from unrelated repos.

There was no revision after the full run; the wording quoted here is the wording fingerprinted by that run. Its conservative interpretation of formatting evidence caused the pkg-08 disagreement.

**Trade-offs**

This threshold can reject a correct change whose formatting claim is true but whose package does not record a formatter outcome, as pkg-08 shows. The Issue-specific proof row also rejected `pkg-02` (gold accept): the three save_* methods have the same fix shape, but only save_text has an explicit failed-write regression/reproduction. Requiring direct evidence for every delivered path can miss when one representative reproduction plus identical sibling hunks and the suite is sufficient. Both misses are clear-accept false rejections; the submitted tool matched all 13 reject packages, including both standards-wall cases.

No check was loosened, so no canary retry was required. The single full run provides the category evidence above. Keeping the passing 18/20 version limits additional course-credit spending but retains those two known conservative limitations; it is not a claim of perfect discrimination. In the actual #61 PR, both the handler and router are tested through a live SQLAlchemy session and the negative error path is observed, so the evidence does not rely on an untested analogous sibling.

## Submission notes

AI assistance: OpenAI Codex helped author the installed tool, execute the harness and local checks, and draft this factual write-up and PR description. Claude Code produced the actual harness and live grades. This account describes those recorded runs, not unperformed personal work or invented retries. Review the explanation and AI disclosure before submitting the course repo through the portal.

CI is pending at PR opening, and the default local frontend run has one disclosed timeout; the longer-timeout run is diagnostic only. Redis #62 and deployment integration remain outside this fix. The Unit 4 assignment allows submitting the honestly disclosed PR as opened; all five green CI jobs remain a maintainer-review condition.

The portal submission must be the entire repo: https://github.com/Damola-png/ai301-coursework

Related paths: `eval-run.txt`, `precheck-live-1.txt`, `precheck-live-2.txt`, `precheck-live-3.txt`, `pr_draft.md`, `test_evidence.md`, and `tools/pr-precheck/`.
