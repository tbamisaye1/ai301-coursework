---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer exactly one question about exactly one PR package: **is this
pull request ready to submit?** Do not answer any other question (is the
fix elegant, would I have done it differently, is the issue worth
fixing), and never grade more than one package per run.

A PR package is five artifacts, read against two references:

- The artifacts: the PR title, the PR description, the commit list, the
  diff, and the test evidence.
- The references: the plan the PR claims to implement (including its
  deviation notes and the repro evidence it was built on) and the issue
  that plan belongs to (its thread and the repo's stated PR template,
  contribution guide and policy).

Every judgment compares an artifact against a reference: the diff against
the plan, the test evidence against the plan's test plan, the
description against the diff, the description against the repo's stated
asks.

## Inputs and modes

**Live mode** grades the student's own PR before it is opened. Read
exactly these inputs:

1. `plan.md` in the current working directory, including its
   `## Deviations` section.
2. The branch's diff: run `git diff main...HEAD` (three dots) from the
   working copy, and `git log --oneline main..HEAD` for the commit list.
   Only committed changes count; ignore uncommitted files and never read
   other files in the working directory as part of the PR.
3. The draft PR in `pr_draft.md`: its first line is the title, the rest
   is the description.
4. The test evidence in `test_evidence.md`.
5. The issue named by the URL the student gives: its body and thread
   (via `gh issue view <n> --repo <owner/repo> --comments`, the GitHub
   API, or the web).
6. The repo's stated asks: `.github/PULL_REQUEST_TEMPLATE.md` and
   `docs/CONTRIBUTING.md` (or `CONTRIBUTING.md`) in the working copy.

A student on the house chain reads the house plan and the house repro
pack in place of their own plan and repro; every check grades the same
things there. If one of the six inputs is missing, say which one in the
summary and grade the checks that depend on it as `unclear`.

**Eval mode** grades a package bundle (a markdown file with Repo facts,
Issue, Thread highlights, Plan context, and Candidate PR sections). The
bundle is the whole world: take every fact from the bundle text, fetch
nothing, run no commands, read no other file. Eval mode always grades a
complete package: every check in the rubric, and the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this directory before anything else.

- If its `Repo:` line still holds a placeholder (anything in angle
  brackets such as `<ORG>/<PATH-REVIEW-REPO>`), stop without grading and
  tell the student to fill the `Repo:` line in `scope.md` with their
  section's Path Review repo. Never guess a scope.
- If the issue URL is not in the scoped repo, refuse to grade and say
  why.
- Apply the house rules it lists when reading evidence (for example, a
  classmate's PR on the same issue does not block this one, and the
  branch must be named `<type>/<issue-number>-<slug>`).

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` after the scope. Hold the outgoing
text, the PR title and the PR description, against each of its rules.
In the summary, list every rule the draft breaks, quoting the rule's
heading and the offending phrase. The voice guide never changes a check
grade or the verdict on its own; it is the student's personal style
gate, and only rubric checks decide readiness.

In eval mode, ignore `voice-guide.md` entirely.

## Component reads

- `rubric.md` defines the checks (name, evidence, pass condition,
  weight) and the verdict rule. It is the only source of what counts as
  ready.
- `references/evidence-guide.md` is the map: for each evidence family,
  where it lives in a bundle and in live mode, and what good looks like.
- `procedure.md` is how you work. Execute it as written, step by step,
  in its order. Where it is silent on a step you need, do not improvise:
  note the gap in the summary ("procedure gap: ...") and grade only
  from what the steps tell you to gather.

Refusal rule: if `rubric.md` has no filled check rows, or
`procedure.md` has no written steps (instruction comments do not count
as content), stop and say the tool cannot grade without both. Never
invent checks at runtime.

## Verdict and output

The verdict is binary: `accept` means ready to submit, `reject` means
hold. There is no third verdict and no score; reservations go in the
evidence lines.

Before the JSON block, print a short readable summary: one line per
check with its grade and deciding fact, then any procedure gaps, then
(live mode) any voice-guide notes. End your reply with this fenced JSON
block, filled in, valid, and last, with nothing after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

Use the bundle id (for example `pkg-07`) as `item` in eval mode and the
issue or PR URL in live mode. Include one entry per rubric check, in
rubric order.

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: a terse complete PR can be ready, and
  a confident, well-formatted one can hide drift. Read the diff itself,
  never only the description's claims about it.
- The rubric decides: if a check passes by its stated condition but
  feels wrong, it still passes. Mention the tension in the summary; do
  not change the grade.
- The procedure decides how: follow it as written and report its gaps.
- Honest shortfalls are allowed: a PR that does less than everything
  can be ready when the plan's deviation notes and the description both
  say so.
- `unclear` follows the rubric's verdict rule. Where that rule is
  silent, treat `unclear` as `fail`: a claim you cannot verify from the
  package is not ready to submit.
