# Unit 1 — Issue Selection

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61

Confirmed choice: `#61`, selected by the student on October 5, 2026. In the live candidate comparison, `#61` ranked first, `#36` was also accepted, and the previous candidate `#72` was rejected because it had an open linked PR. The personal reflection below still needs the student's own answers before submission.

**Verdict output**

**Issue #61 is accepted: every required check passes.** I couldn't use `curl` because Bash was denied in this session. I ran the same read-only GETs through WebFetch, which summarizes responses. The comments, timeline and policy documents therefore reached me as summaries and not raw JSON or text. No request failed, so no retries were needed. Nothing was edited, posted or claimed.

**Scope:** The issue is in `codepath/pathreview-ai301-fa26-s1`, the only repo `scope.md` allows. The Path Review house rule applies: other students' claim comments don't block the issue.

**Required checks**
- **Maintainer activity: pass.**
  - The repo is not archived: https://api.github.com/repos/codepath/pathreview-ai301-fa26-s1
  - The newest `main` commits are by Aburke225, a human, on 2026-09-16 (`f89c06f`, `2b8855f`, `32240e0`), 18 days ago: https://api.github.com/repos/codepath/pathreview-ai301-fa26-s1/commits?sha=main
  - There are no releases, so the commits decide it.
- **Repo in use: pass.** Same evidence: not archived, human commits within 365 days, and the last push was 2026-09-16. A repo may pass on commits alone.
- **Bounded contribution: pass.**
  - The title and body name one bug: `api/routes/health.py` runs the raw string `"SELECT 1"` where SQLAlchemy 2.x needs `text()`.
  - The opener, Aburke225, is a COLLABORATOR, and the issue is a single call-site fix with a labeled `bug`, `good first issue`, `api` and `tier-1`.
  - It was opened 2026-09-10, so it is far under 730 days old. No linked PRs have closed unmerged. All 21 comments are in one page and none is a maintainer disagreement.
  - https://api.github.com/repos/codepath/pathreview-ai301-fa26-s1/issues/61
- **Available to start: pass.**
  - Assignees are `[]`, and the 23-event timeline has no connected PR.
  - The only cross-references are issue #62 (a different bug, the Redis probe), the bot's daily digest (issue #6) and issue #26.
  - There are 5 open PRs (#86–#90). Only #86 touches health checks, and it targets Redis (#62), not #61.
  - Eleven or more students have posted claim comments, including smtanaka00, RadEagle, ONESO-goat, DiveshIO, tureh1, AbduTello, andrewskoblov and scorp6on. The house rule says these don't block the issue.
  - https://api.github.com/repos/codepath/pathreview-ai301-fa26-s1/issues/61/timeline and https://api.github.com/repos/codepath/pathreview-ai301-fa26-s1/pulls?state=all
- **AI contribution permitted: pass.**
  - The repo root has no `CONTRIBUTING.md`, and `.github/` holds only `ISSUE_TEMPLATE/`, `PULL_REQUEST_TEMPLATE.md` and `workflows/`. There is no AI policy file.
  - I read `docs/CONTRIBUTING.md`, the README, `docs/SETUP.md`, `docs/ARCHITECTURE.md` and the PR template.
  - None bans or conditions AI use. The only AI mentions are the product's own features, such as `OPENROUTER_API_KEY`.
  - The conditions that do exist are workflow rules: green CI on all five jobs, never run `ruff --fix --unsafe-fixes`, remove the `xfail` marker when you fix the bug, and fill in the PR template. These are terms to follow, not bans.
  - https://raw.githubusercontent.com/codepath/pathreview-ai301-fa26-s1/main/docs/CONTRIBUTING.md, https://raw.githubusercontent.com/codepath/pathreview-ai301-fa26-s1/main/README.md, https://raw.githubusercontent.com/codepath/pathreview-ai301-fa26-s1/main/docs/SETUP.md, https://raw.githubusercontent.com/codepath/pathreview-ai301-fa26-s1/main/docs/ARCHITECTURE.md

**Preferred check**
- **Testable path: pass.** The body gives a reproduction (`GET /health`), the exact `ArgumentError` text and the affected file. Many commenters reproduced it on `f89c06f`.

**Fit:** This matches your profile: a bounded Python/FastAPI backend bug with a reproducible failure, and a unit test is easy to write for it.

**Caveats**
- The thread is crowded, with 8 or more claimants and several already posting plans. That doesn't change the verdict under the house rule, but you'd be competing for the same one-line fix, and credit attaches to your PR.
- Timestamps on 2026-10-05 UTC, such as the latest comment at 04:37Z, fall on 2026-10-04 local time. No check turns on them.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61",
  "checks": [
    {"name": "Maintainer activity", "grade": "pass", "evidence": "Repo not archived; human commits by Aburke225 on main on 2026-09-16 (f89c06f), within 365 days; no releases."},
    {"name": "Repo in use", "grade": "pass", "evidence": "Not archived; last push 2026-09-16 and human default-branch commits within 365 days; commits alone suffice."},
    {"name": "Bounded contribution", "grade": "pass", "evidence": "Names one bug: api/routes/health.py runs raw 'SELECT 1' instead of text(); opened 2026-09-10 by a COLLABORATOR; no umbrella, support question, disagreement, or closed unmerged PRs."},
    {"name": "Available to start", "grade": "pass", "evidence": "No assignees and no linked PR (open PRs #86-#90 do not target #61); many student claim comments are ignored under the Path Review house rule."},
    {"name": "AI contribution permitted", "grade": "pass", "evidence": "No root or .github CONTRIBUTING or AI policy; docs/CONTRIBUTING.md, README, SETUP.md, ARCHITECTURE.md and PR template contain no AI ban or disclosure rule."},
    {"name": "Testable path", "grade": "pass", "evidence": "Body gives repro (GET /health), exact ArgumentError text, and file api/routes/health.py; reproduced on f89c06f by several commenters."}
  ],
  "verdict": "accept"
}
```

## Eval iterations

**Run history**

Three eval runs were completed on October 4, 2026 (America/Chicago), using the official Unit 1 harness and its pinned `sonnet` model:

1. Full run of the original rubric: `agreement: 15/20 scored items  (bar: 18/20: below the bar)`. Every category had at least one match. The disagreements were `issue-01`, `issue-04`, `issue-15`, `issue-19`, and `issue-20`; all five involved the Bounded contribution check.
2. After revising that check, a targeted `--only issue-01,issue-04,issue-15,issue-19,issue-20` run: `agreement: 4/5 scored items`. The three clear accepts passed, and the old issue with two unsuccessful linked PRs was rejected. `issue-20` still differed from gold. This partial run did not replace the submitted transcript.
3. Confirming full run of the same revised rubric: `agreement: 19/20 scored items  (bar: 18/20: PASS)`. This is the harness-written run in `eval-run.txt`, generated with `--save-run`. Its category line is `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 3/4`. The four calibration issues were not scored.

Both full runs and the targeted run used the installed rubric at `~/.claude/skills/issue-select/rubric.md`. The uploaded rubric and `SKILL.md` match the fingerprints in the final run's header.

**Issue analysis**

In the final run, `issue-20` received `accept` from this rubric, while the gold label was `reject`. The snapshot requests a company-logo toolbar shape that users can place, resize, move, and export; it also says, "Out of scope for v1: custom logo upload / branding settings." The revised Bounded contribution check accepts a named UI element with an observable target, so the harness recorded that check as `pass`. The other five checks also passed: the repository had recent human activity, no assignee or linked PR, no AI ban, and a concrete acceptance example.

The issue also says, "Logo asset TBD." A named outcome does not establish how much editor, element, and export work the change will require. That is a limit of this rubric's scope test and explains why its acceptance can be more permissive than the gold verdict. The rubric was kept at this wording after the targeted run; the complete confirming run still passed the 18/20 bar and category floor.

**Check rationale**

The current Bounded contribution pass condition in `tools/issue-select/rubric.md` is:

> The title or body identifies a specific behavior, document, UI element, or output to add or fix. Related edits across several documentation pages, several examples of the same missing behavior, and alternative implementation suggestions for one named bug can all pass. A terse maintainer-authored report naming affected rules or UI behavior does not need a second maintainer comment or reproduction steps. Reject a support question, explicit umbrella or tracking issue collecting separate requests, codebase-wide migration, unresolved disagreement over the desired behavior, or a feature wish lacking an observable target. Also reject an issue open for more than 730 days with at least two closed unmerged linked PRs unless a human maintainer has explicitly invited a fresh attempt within 180 days. Measure both dates against the capture date in eval mode.

The first run treated related documentation edits (`issue-01`), named missing rule previews (`issue-04`), and suggested causes of one UI-freeze bug (`issue-19`) as unbounded. The revision distinguishes one outcome with several related edits from an umbrella collecting unrelated requests. The 730-day, two-PR threshold adds the history signal missing from the first acceptance of `issue-15`, without rejecting every old issue or treating one closed PR as an active claim.

**Trade-offs**

The broader scope wording accepts the concrete logo feature in `issue-20` even though its asset and implementation work remain uncertain; both the targeted and confirming runs showed that same disagreement. Requiring detailed implementation plans for every feature might catch it, but would also risk rejecting terse, bounded reports such as `issue-04`. The history threshold can reject an old issue whose two failed attempts do not reflect its current difficulty unless a recent human maintainer invitation is visible. These are accepted limits; the confirming run matched all eight clear accepts and at least one issue in every category.

## Selection rationale

**Selection rationale**

1. [In your own words: why does the SQLAlchemy health-check fix in issue #61 fit your interests and available time?]
2. [In your own words: what did the live verdict identify correctly, and what did you consider that the rubric could not? The independent Redis-probe bug in issue #62 may keep the overall health endpoint at 503.]
3. [In your own words: how difficult do you expect claiming this issue to be? The Path Review house rule allows shared student claims; no claim was posted during these runs.]
