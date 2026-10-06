# Rubric: is this plan ready to post and build from?

Every check below judges the plan itself against the issue, the repro
evidence, the thread, and the repo facts. None of them judges length,
headings, or polish: a terse plan can pass every check, and a long
confident one can fail.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| grounded-cause | The plan's stated cause (its Diagnosis / Cause line) read against every step, artifact, and control run in the repro-evidence block | Pass if the stated cause explains what the repro shows AND no control run or step contradicts it. Fail if any control or step rules the blamed component out (e.g. the bug still appears with that component removed or bypassed, or the evidence shows the failure already present before the blamed code runs), or if the plan adopts a diagnosis from the thread or issue that the repro evidence contradicts. Agreeing with the thread is not enough; the repro decides. | required |
| bounded-scope | The plan's list of changes and its in-scope / not-in-scope lines, read against the behavior the issue and repro describe | Pass if every change the plan commits to is needed to fix the reproduced behavior (the fix, its regression tests, and directly required docs for that fix). Explicitly deferring larger or related work ("not in scope: X") is good and still passes, even if the deferred part is arguably needed long term. Fail if the plan commits to ANY extra work the issue did not ask for: a refactor or rewrite, a dependency migration or upgrade, a new option/setting/feature, UI changes, a new framework or abstraction, CI changes, or "while I'm in the area" fixes. Offering to split the extras into a PR series does not rescue it. | required |
| executable | The plan's files/areas, approach, and order of work | Pass if a stranger could start work today without asking the author anything: the plan names where the change goes (a file, function, or code site the package identifies) AND commits to one concrete change there. Fail if the key decisions are deferred to build time: no chosen approach ("investigate", "profile and optimize", "look into"), the location left open ("somewhere", "gocui? tcell?"), or alternatives left unchosen ("upstream or vendored, whichever is easier"). An unknown that is named and bounded (e.g. "the fix site may move one layer") does not fail this check, and neither does naming the module or code path (e.g. "the reattach path in zellij-server") with one committed change while leaving the exact function to be pinned during the build. What fails is an open choice of WHAT to do or WHICH layer/approach, not an unpinned line number. | required |
| decisive-test | The plan's test plan, read against the repro evidence's steps and artifacts | Pass if the test plan names at least one specific observable outcome that differs between the broken and fixed code and is tied to the reproduced behavior: an exact output, exit code, color, value, or behavior at a named repro step. Fail if the only success signals are generic ("run the full test suite", "nothing regresses", "should feel fast", "looks better") or cannot distinguish fixed from broken. | required |
| thread-direction | The plan comment (and plan) read against the thread highlights, especially OWNER / MEMBER / COLLABORATOR / maintainer comments | Pass if the thread has no explicit maintainer direction, OR the plan comment visibly engages that direction: follows it, builds on it, or explicitly explains why it diverges. "Explicit direction" means a maintainer isolated the culprit code, proposed or rejected an approach, posted a patch or test build and asked for testing, or stated what fix they want. Fail if the comment proposes a different kind of change (e.g. a docs-only workaround when the owner isolated code and posted a patch) without acknowledging that direction, or pursues an approach a maintainer already rejected. Non-maintainer opinions in the thread do not trigger this check. | required |
| ai-disclosure | The repo-facts block's contribution / AI policy, read against the plan comment. In eval mode, treat every package as AI-assisted work. | Pass if the repo states no AI policy, or its policy does not require disclosure in issue comments (e.g. it only asks for disclosure in the pull request, or only requires that the contributor understands the code), OR the comment contains the disclosure the policy requires. Fail if the policy requires disclosing AI use in comments or in "any form" / "all AI usage" and the plan comment contains no disclosure (naming the tool and the extent of help). | required |
| honest-unknowns | The plan's risk / unknowns / deviations section | Pass if the plan names at least one real risk or unknown, or records deviations honestly, rather than presenting everything as certain. | preferred |

## Verdict rule

Accept (ready) only if every `required` check passes. One `fail` on any
required check means reject (hold). `preferred` checks never change the
verdict; report them in the summary only.

`unclear` handling: before grading a required check `unclear`, reread
the specific package part that check's Evidence column names. If it is
still genuinely undecidable from the package, grade it `unclear` and
treat it as `fail` for the verdict, because a plan you cannot verify
from the package is not ready to build from. Never use `unclear` for a
check whose trigger is absent (no maintainer direction in the thread,
no AI policy): those checks `pass`.
