# Evidence guide: where evidence lives in a PR package

An eval bundle has these sections: Repo facts, Issue, Thread
highlights, Plan context (repro evidence plus the plan), and Candidate
PR (Title, Description, Commits, Diff, Test evidence). In live mode the
same evidence lives in `plan.md`, `git diff main...HEAD`,
`git log --oneline main..HEAD`, `pr_draft.md`, `test_evidence.md`, the
issue thread on GitHub, and the repo's `.github/PULL_REQUEST_TEMPLATE.md`
and `docs/CONTRIBUTING.md`.

## Plan fidelity (harness category: silent-drift)

- Where it lives: the plan's scope, files, not-in-scope line, and
  deviation/deferral notes (bundle: Plan context; live: `plan.md`
  including `## Deviations`). What actually changed is in the Diff
  (live: `git diff main...HEAD`). The description's fidelity claims
  ("implements the plan exactly", "no functional changes outside X",
  "docs now document Y", the Changes list) are in the Description
  (live: `pr_draft.md`).
- What good looks like: every hunk does something the plan or a
  deviation note names, every planned change is in the diff or deferred
  in a note, and the description says exactly what the diff does,
  including any shortfall. Drift in the "more" direction: a new config
  option, flag, rename pass, or rewrite of a file the plan never
  mentions. Drift in the "less" direction: a planned change (e.g. a
  docs update) missing from the diff, especially while the description
  claims it is there.

## Test evidence (harness category: not-tested)

- Where it lives: the Test evidence section (live: `test_evidence.md`
  and the description's Testing section), read against the plan's test
  plan and the repro's failing command in Plan context (live: the repro
  comment on the issue and the plan's Test plan).
- What good looks like: the repro's failing scenario re-run with the
  before output and the after output both shown, every scenario the
  test plan names covered, and the repo's own suite or checks reported
  by command with their result. Not good: "tested locally", "works on
  my machine", "cargo test passes" with nothing shown, running only the
  control or an unchanged path (e.g. a GET when the bug is in POST), or
  re-running one of two scenarios the plan named.

## Diff quality (harness category: unreviewable)

- Where it lives: the unified Diff and the Commits list (live:
  `git diff main...HEAD` and `git log --oneline main..HEAD`).
- What good looks like: only the fix and its tests, with any comments
  explaining the fix. Debris tells: `print`/`eprintln!`/`console.log`
  debug lines, commented-out code or a commented-out first attempt, a
  dead or `allow(dead_code)` helper, a stray `TODO`, re-indent or
  import-reordering hunks with no behavior change, duplicated lines,
  and edits to unrelated files. Commit names like "wip" support but do
  not by themselves prove debris; the diff decides.

## Standards and comms (harness category: standards-wall)

- Where it lives: the Repo facts "pull requests" line (template
  sections or required checklist) and "contribution policy" line (AI
  policy), read against the Description and the Diff; the Thread
  highlights for maintainer direction. Live: the repo's
  `.github/PULL_REQUEST_TEMPLATE.md` sections (Summary, Issue with
  `Closes #`, Changes, Testing, Screenshots / Demo, Notes for
  Reviewers) and `docs/CONTRIBUTING.md`; the course always requires an
  AI-use disclosure, which goes in Notes for Reviewers.
- What good looks like: every required section has real content, the
  issue is linked ("Closes #N"), any file the template requires for bug
  fixes (e.g. a changelog or whatsnew entry) is in the diff, and when
  the policy requires AI disclosure the description says which tool was
  used and how much. Not good: a template checklist visibly absent, no
  issue link, or no disclosure under a policy that requires it.
