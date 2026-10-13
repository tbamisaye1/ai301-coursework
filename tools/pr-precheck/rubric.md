# Rubric: is this pull request ready to submit?

Every check compares an artifact against a reference. None judges
length, formatting, or how many commits there are. A PR that does less
than everything can pass every check if the plan's deviation notes and
the description both disclose the shortfall.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diff-matches-plan | Every hunk in the diff (each changed file and what the hunk does) read against the plan's scope, its not-in-scope line, and its deviation notes | Pass if every behavior change in the diff is something the plan's scope or a deviation note names, AND every change the plan commits to appears in the diff or is recorded as deferred in a deviation note. Fail if the diff adds anything the plan does not name (a new option, flag or setting, a rewrite of another function or file, a renaming pass) or something the plan explicitly scopes out, or if a planned change is missing with no deviation note. Test files for the fix and docs the plan names are in scope. | required |
| description-matches-diff | The description's claims about what the PR changes ("implements the plan exactly", "no other changes", "docs now document X", the Changes list) read against the actual diff | Pass if every claim the description makes about the change is true of the diff, and any shortfall or deviation in the diff is stated in the description. Fail if the description claims something the diff does not deliver (e.g. a docs update absent from the diff) or claims fidelity/no other changes while the diff contains unplanned changes. | required |
| decisive-evidence | The test evidence read against the plan's test plan and the repro's own steps | Pass if the evidence re-runs the plan's repro (the failing scenario the issue and repro describe, not a control or a different path) and shows the observable before and after (an output, exit code, error, or behavior), AND covers every failure scenario the plan's test plan names. Fail if the evidence is only a claim ("tested locally", "works now", "verified for a day", "tests pass" with nothing shown), exercises only a control or unchanged path, or skips a scenario the plan's test plan named. | required |
| repo-checks-run | The test evidence and description read against the repo's own suite or checks named in the plan, template, or contributing guide | Pass if the outcome of the repo's own tests or checks is reported (a named command with its result, including an honestly reported failure with a reason). Fail if no repo test, suite, or check outcome appears anywhere in the PR. | required |
| clean-diff | Every hunk in the diff and the commit list | Pass if every hunk serves the fix or its tests. Fail if the diff contains debris: a debug print or log left in, a commented-out code block or first attempt, a dead or unused function, a stray TODO, formatting/re-indent or import churn with no behavior change, re-printed identical lines, or an edit unrelated to the fix. Comments that explain the fix are not debris. Commit message wording alone does not fail this check. | required |
| template-honored | The repo-facts block's stated PR template / required checklist and contributing asks, read against the description and diff | Pass if the repo states no template or checklist, OR every section and item it says is required is present with real content (e.g. "closes #N", a filled checklist, a changelog/whatsnew entry in the diff when the template requires one for bug fixes). Fail if a required section, checklist item, issue link, or required file entry is visibly missing. | required |
| ai-disclosure | The repo's stated AI policy (repo-facts contribution policy; live: CONTRIBUTING / AI_POLICY) read against the description. In eval mode treat every package as AI-assisted work; in live mode the course always requires disclosure. | Pass if the repo states no AI disclosure requirement (eval mode), OR the description contains a disclosure of how AI was used that meets the policy (naming the tool and the extent when the policy asks). Fail if the policy requires disclosing AI use and the description contains none. In live mode, fail if the description has no AI-use disclosure, because the course requires it. | required |
| thread-engaged | Thread highlights (maintainer comments) read against the description | Pass if the thread has no explicit maintainer question or direction, or the description engages it. | preferred |

## Verdict rule

Accept (submit) only if every `required` check passes. Any required
check graded `fail` means reject (hold). `preferred` checks are
reported but never change the verdict.

`unclear` handling: before grading a required check `unclear`, reread
the exact package part its Evidence column names. If it is still
undecidable, grade `unclear` and count it as `fail`. Never grade
`unclear` when the check's trigger is absent (no stated template, no AI
policy in eval mode, no maintainer direction): those pass.
