# Evidence guide: where evidence lives in a plan package

Eval mode: a package file has these sections, in order: Repo facts,
Issue, Thread highlights, Repro evidence, Candidate plan, Candidate
plan comment. Live mode: the same evidence lives on the issue page,
in the student's posted repro comment, in the repo's CONTRIBUTING /
AI_POLICY files, and in the drafts `plan.md` and `comment.md`.

## Diagnosis and grounding

- Where it lives: the cause is the plan's "Diagnosis" or "Cause"
  line (live: the Diagnosis section of `plan.md`). The behavior it
  must explain is in the Repro evidence block: the numbered steps, the
  artifact (output, stack trace, timings), and especially the
  "Control" runs (live: the student's posted repro comment on the
  issue).
- What good looks like: the stated cause predicts every control. If a
  control removes or bypasses the blamed component and the bug is
  still there, or a step shows the failure already present before the
  blamed code runs, the diagnosis is wrong however confident it reads.
  A diagnosis copied from the thread is only good if the repro agrees
  with it.

## Scope

- Where it lives: the plan's "Change" / "Scope" / "Proposed changes"
  list, its "In scope" / "Not in scope" lines, its "Files" list, and
  any promises in the plan comment ("I'll also...", "while I'm
  here").
- What good looks like: one bounded change at the site the repro
  isolates, plus regression tests. Larger related work is named as
  deferred. A drive-by rewrite looks like: migrations, upgrades, new
  settings or props, UI work, refactors, or a framework added "while
  touching" the area, even when the core fix inside it is right.

## Executability

- Where it lives: the plan's "Files" / "Approach" / numbered steps,
  and named functions or line numbers in the Diagnosis.
- What good looks like: a named file, function, or code site, and one
  committed change there, so a stranger could open the file and start.
  Not executable: "profile", "investigate", "look into", "somewhere",
  "maybe", or an unchosen list of options. A named, bounded unknown
  ("the exact clamp site may move one layer") is still executable.

## Test plan

- Where it lives: the plan's "Test plan" / "Test" section, read
  against the Repro evidence steps and their Expected / Actual lines.
- What good looks like: re-running the repro and naming the exact
  observable that must change (a specific output, exit 0, the color
  flipping at step 3, a printed path, a test that now passes), plus
  the controls staying unchanged. Vague: "run the full test suite",
  "nothing regresses", "should feel fast".

## Honesty

- Where it lives: the plan's "Risk" / "Unknowns" lines and, after a
  build, the `## Deviations` section of `plan.md`; in the comment,
  any "flagging that..." sentence.
- What good looks like: specific risks with what the author will do
  about them. False confidence: claims of certainty the repro does not
  support, or no risk named at all on a non-trivial change.

## Comms

- Where it lives: the Candidate plan comment read against (1) the
  Thread highlights, looking at each comment's role label (OWNER,
  MEMBER, COLLABORATOR, CONTRIBUTOR) and (2) the Repo facts' "bug
  reports" and "contribution policy" lines, including any AI policy.
  Live: the issue thread on GitHub and the repo's CONTRIBUTING.md /
  AI_POLICY.md.
- What good looks like: if a maintainer isolated code, proposed or
  rejected an approach, or posted a patch, the comment names that
  direction and follows it or explains why not. If the repo's AI
  policy requires disclosure in comments or "all AI usage in any
  form", the comment states the tool used and how much it helped. A
  policy that only governs pull requests does not require disclosure
  in the plan comment. Boilerplate looks like: a comment that would
  read the same on any issue.
