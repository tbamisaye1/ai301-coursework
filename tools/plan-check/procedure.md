# Procedure: how this skill grades a plan package

These steps grade a plan someone else wrote. They do not help write
one. Follow them in order.

## Read order

1. Read the **repro evidence** first: environment, every numbered
   step, every artifact, and every control run. Write down (a) the
   exact broken behavior, (b) what each control run changes and
   whether the bug survives it, and (c) any step that shows WHERE the
   failure already exists. The repro is read first so the plan's
   diagnosis cannot anchor you.
2. Read the **issue** (title and body) next. Note what the reporter
   asked to have fixed, and nothing more; this bounds the scope check.
3. Read the **thread highlights**. For each comment, note the author's
   role. List any maintainer (OWNER, MEMBER, COLLABORATOR, or a
   CONTRIBUTOR who is clearly the project lead) direction: isolated
   culprit, proposed approach, rejected approach, posted patch/test
   build, or requested fix shape. If none, write "no maintainer
   direction".
4. Read the **repo facts**. Copy the contribution policy's AI rule
   word for word, and note WHERE it requires disclosure (comments,
   issues, "any form", or only PRs). If none, write "no AI policy".
5. Only now read the **candidate plan**, then the **candidate plan
   comment**.

## Evidence gathering

For each check, pull exactly these facts before grading anything:

1. grounded-cause: the plan's one-sentence cause, plus the list of
   control runs and steps from Read order step 1. For each control,
   write whether it is consistent with the plan's cause.
2. bounded-scope: list every change the plan commits to (numbered
   steps, "while touching" items, comment promises). Mark each one
   "needed for the reproduced bug" or "extra". Separately list what
   the plan explicitly defers.
3. executable: list the named files / functions / code sites, and the
   single change the plan commits to at each. Note any deferral words:
   investigate, look into, profile, somewhere, maybe, whichever, or a
   question mark about which layer.
4. decisive-test: copy the test plan's success signals. For each,
   write whether it is a specific observable (output, exit code,
   color, value, behavior at a named step) or a generic one (suite
   passes, feels fast, nothing breaks).
5. thread-direction: from Read order step 3, the maintainer direction
   list; from the plan comment, the sentence (if any) that follows,
   builds on, or explains divergence from each direction.
6. ai-disclosure: from Read order step 4, the policy text; from the
   plan comment, any sentence disclosing AI use.
7. honest-unknowns: the plan's risk / unknown / deviation sentences.

If a part of the package is missing (for example no thread, or no
repo-facts AI rule), record "absent" for that part instead of
searching elsewhere. In eval mode, never use anything outside the
bundle.

## Check execution

1. Run the checks in rubric table order: grounded-cause,
   bounded-scope, executable, decisive-test, thread-direction,
   ai-disclosure, honest-unknowns.
2. Grade each check only from the facts gathered for it in Evidence
   gathering, applying the rubric's pass condition literally. Do not
   let one check's result change another's: a plan with a wrong cause
   can still be bounded; an excellent plan can still fail
   ai-disclosure.
3. Every grade gets one line of evidence: the quote or fact that
   decided it (for a fail, the specific control run, extra change,
   deferral phrase, generic test line, ignored maintainer comment, or
   missing disclosure).
4. If a check's trigger is absent (no maintainer direction, no AI
   policy requiring comment disclosure), grade it `pass` and say why.
5. If the evidence for a check is present but genuinely ambiguous,
   reread only the package part named in that check's Evidence
   column once. If still ambiguous, grade `unclear`.
6. Grade all checks even after a required one fails, so the output
   shows every problem at once.

## Verdict assembly

1. Count the required checks graded `fail` or `unclear`.
2. If that count is 0, the verdict is `accept`. Otherwise it is
   `reject`.
3. Preferred checks are reported but never counted.
4. In the readable summary, name the deciding check(s) first and quote
   the evidence line for each. For an accept, say which fact made the
   closest call pass.
5. Emit the JSON block exactly as SKILL.md specifies, with one entry
   per check in rubric order, and put nothing after it.
