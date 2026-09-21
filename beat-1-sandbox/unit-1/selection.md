# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68

**Verdict output**

Live-mode run of `issue-select` on two candidates (#72 and #68), Sun 20 Sep 2026. Pasted verbatim; the chosen issue, #68, is ranked first with verdict `accept`.

````
Summary

Both candidates are in the scoped repo (codepath/pathreview-ai301-fa26-s3) — no house-rule-blocked claims here since Path Review claim comments never block. Repo-level facts (checked once, apply to both): not archived, no releases, last push 2026-09-16 (4 days before today, 2026-09-20) by a human (Andrew Burke) — satisfies both repo-alive and maintainer-alive. No CONTRIBUTING.md/AI-policy file states any AI restriction (docs/CONTRIBUTING.md only covers branch naming, commits, and CI — no AI clause) — ai-policy passes by silence.

Ranked (both accepted):

1. #68 — Keyword search ZeroDivisionError on empty index (rag/retriever/keyword_search.py). Fits best: it's RAG/retrieval work, directly on your stated goal of getting better at agentic AI systems. A student commented "I'd like to take this on," but no assignee and no open PR exist, so per the Path Review house rule it doesn't block you.
2. #72 — verify_password raises instead of returning False on malformed hash (core/security.py). Solid backend Python bug fix, no claims at all, but it's general security/backend rather than your ML/agentic-AI growth area.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Last default-branch commit 2026-09-16 by Andrew Burke (human), within 30 days of 2026-09-20"},
      {"name": "repo-alive", "grade": "pass", "evidence": "Not archived, no releases, last push 2026-09-16 within 30 days"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "Single bounded bug (index() should not raise on empty corpus), opened 2026-09-10, no umbrella/debate/core-internals language"},
      {"name": "not-claimed", "grade": "pass", "evidence": "assignees: [], no linked/cross-referenced PRs in timeline; sole comment is a student's 'I'd like to take this' with no assignee/PR, which per Path Review house rule and rubric does not count as a claim"},
      {"name": "ai-policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI-use clause; repo states no policy"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Last default-branch commit 2026-09-16 by Andrew Burke (human), within 30 days of 2026-09-20"},
      {"name": "repo-alive", "grade": "pass", "evidence": "Not archived, no releases, last push 2026-09-16 within 30 days"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "Single bounded bug (fail-closed on UnknownHashError), opened 2026-09-10, no umbrella/debate/core-internals language"},
      {"name": "not-claimed", "grade": "pass", "evidence": "assignees: [], zero comments, no linked/cross-referenced PRs in timeline"},
      {"name": "ai-policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI-use clause; repo states no policy"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

1. Run 1: smoke run (`--limit 3`), rubric v1: **2/3** scored items
2. Run 2: full run, rubric v2: **18/20** scored items. This is the run committed in `eval-run.txt`:

   > agreement: 18/20 scored items  (bar: 18/20: PASS)

My first run marked conda and pylint as abandoned even though both of them had just released new versions. To solve this I added an additional rule to the rubric, so that it also counts a person who had committed within 30 days. Also, on issue-01, a single documentation job got read as an umbrella instead of multiple different tasks, so I tightened the wording there as well.

**Issue analysis**

`issue-09` (conda/conda, opened 2018-08-03). My rubric's decision: **reject**. Gold label: **accept**.

The check that rejected it was `bounded-scope`. The skill's evidence from my run:

> Issue opened 2018-08-03 (>365 days before 2026-08-05 capture) AND linked PR conda/conda#11627 is closed, not merged

The clause of my rubric that fired:

> (5) the issue was opened more than 365 days before the capture date AND has at least one closed, unmerged PR (an abandoned attempt)

Everything passed except the history rule. It rejected issue-09 because the issue was open since 2018 with one abandoned fix attempt. Even though the evidence guide says several abandoned attempts, my rule had only one.

**Check rationale**

`maintainer-alive`, quoted as currently written in `tools/issue-select/rubric.md`:

> Passes if EITHER (a) at least one of the sampled issues got its first OWNER, MEMBER or COLLABORATOR comment within 30 days, OR (b) at least one of the last 5 default-branch commits is dated within 30 days of the capture date and was authored by a human (an author name that does not end in "[bot]"). "No maintainer comment in thread" counts as no response for that issue. Fails only if neither (a) nor (b) holds.

I changed my rationale in maintainer-alive so that it would count replies or commits as being active, because both of those indicate that the project is still active.

**Trade-offs**

I made it pass on replies or commits, which is more forgiving. Something that might still get passed is a project where people are writing code but never answer anyone from the outside.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. I chose #68 because it aligned with my interests in building agentic AI systems.
2. The verdict got the fact that it was an active repo, that it was a single bug, that nobody was assigned to it, and that there was no generative AI coding ban. What it could not weigh was whether I actually understood retrieval code, which I do. My rubric's five checks only ask whether an issue is safe to take, never whether it suits me; fit lives in scope.md and only ranks issues the rubric has already accepted.
3. It will be hard to claim #68 because a classmate has already commented "I'd like to take this on". The rules I've implemented don't block on comments like that, only on an assignee or an open PR, so we could both end up working on it at the same time.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
