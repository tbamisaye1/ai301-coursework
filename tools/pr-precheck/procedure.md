# Procedure: how this tool grades a PR package

These steps grade a PR someone else prepared. Follow them in order.

## Read order

1. Read the **plan** first (eval: the Plan context block; live:
   `plan.md`). Write down: (a) the one change it commits to, (b) the
   files it names, (c) its not-in-scope line, (d) every scenario in its
   test plan, and (e) every deviation or deferral note. The plan is
   read first because it is the yardstick everything else is measured
   against.
2. Read the **repro evidence** inside the plan context (live: the
   student's repro on the issue). Note the exact failing command or
   scenario and its broken output.
3. Read the **diff and the commit list** next, before the description,
   so the description's claims cannot tell you what to see. List every
   hunk: file, and one line saying what it does.
4. Read the **test evidence**.
5. Read the **repo facts** (live: the PR template and CONTRIBUTING.md):
   copy every required template section or checklist item, and the AI
   policy wording, word for word. If none, write "no template" / "no AI
   policy".
6. Read the **thread highlights** (live: the issue thread) and note any
   maintainer question or direction.
7. Read the **title and description** last.

## Evidence gathering

For each check, gather exactly these facts before grading any check:

1. diff-matches-plan: put the hunk list (Read order step 3) side by side
   with the plan notes (step 1). Mark each hunk "planned", "deviation
   note", or "unplanned". Mark each planned change "present",
   "deferred with note", or "missing".
2. description-matches-diff: list each claim the description makes
   about the change (including "exactly as planned", "no other
   changes", and each Changes bullet). Mark each claim true or false
   against the hunk list.
3. decisive-evidence: put the test evidence side by side with the
   plan's test-plan scenarios and the repro's failing command. For each
   scenario, write whether the evidence shows it run, with a before and
   an after. Note whether the evidence runs the failing path or only a
   control.
4. repo-checks-run: find any repo test/suite/check command and its
   reported result anywhere in the evidence or description.
5. clean-diff: scan every added and removed line for debris: debug
   prints/logs, commented-out code, dead or unused functions, TODOs,
   re-indent or import churn, duplicated lines, edits unrelated to the
   fix. Quote the first instance found.
6. template-honored: check each required section or item from Read
   order step 5 against the description and diff; mark present or
   missing.
7. ai-disclosure: find any sentence in the description disclosing AI
   use.
8. thread-engaged: find whether the description answers each maintainer
   note from Read order step 6.

If a package part is absent, record "absent" and do not search
elsewhere. In eval mode never use anything outside the bundle.

## Check execution

1. Grade checks in rubric table order.
2. Grade each check only from the facts gathered for it, applying its
   pass condition literally. Keep checks independent: a correct,
   well-tested fix can still fail clean-diff or ai-disclosure, and a
   messy diff can still pass decisive-evidence.
3. An honest shortfall is not drift: a planned change that is missing
   but deferred in a deviation note AND restated in the description
   passes diff-matches-plan and description-matches-diff.
4. Give every grade one evidence line: the quote or fact that decided
   it (for a fail, the unplanned hunk, the false claim, the missing
   scenario, the debris line, the missing section, or the missing
   disclosure).
5. If a check's trigger is absent, grade it pass and say why.
6. If evidence is present but ambiguous, reread only the package part
   named in the check's Evidence column once; if still ambiguous, grade
   `unclear`.
7. Grade every check even after one fails.

## Verdict assembly

1. Count the required checks graded `fail` or `unclear`.
2. If the count is 0, the verdict is `accept`; otherwise `reject`.
3. Never count preferred checks.
4. In the summary, name the deciding check(s) first with their evidence
   line; for an accept, name the closest call and why it passed.
5. Emit the JSON block exactly as SKILL.md specifies, one entry per
   check in rubric order, with nothing after it.
