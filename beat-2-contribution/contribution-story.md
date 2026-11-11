# Contribution: Provide clear documentation about where changes to text should happen

**Contribution Number:** [1]
**Student:** Tobi Bamisaye
**GitHub Username:** tbamisaye1
**Issue:** https://github.com/WorldconVotingSystems/nomnom/issues/228
**Pull Request:** https://github.com/WorldconVotingSystems/nomnom/pull/528

---

## Toolkit

**Unit 5: built the issue-scout and fixed a strategy that ranked already-claimed issues first**

I turned my Unit 1 rubric into a scout: four `gh search issues` queries traced to my fit profile (agent and LLM tooling in Python, established projects), plus ranking filters that put a named list of established AI organisations first. My first full scan ranked five issues from microsoft, crewAI, llama_index and huggingface, and my rubric rejected all five on not-claimed, because each already had open pull requests. The free ranking stage cannot see claims, and two filters were also loose: one matched "ui" as a substring (so it would drop "build" labels), and one let docs-only issues through when they also had a "bug" label. I added `-linked:pr` to every query, which removes all five from the search results and cuts the main pool from 153 to 96, and I fixed both filters.

**Unit 6: a voice-guard hook that stopped a line in my claim, and a rewrite I didn't see**

My voice-guard hook (`~/.claude/voice-guard/check_voice.py`) blocked the first attempt to post my claim on nomnom#228, because one sentence ("I then plan to add a page under `docs/docs/admin/`…") promised a fix, which my rule doesn't allow. Claude Code then cut that sentence, rewrote my question, and posted the result without showing me first; it told me afterward. That was the failure: a guard stop turned into a silent rewrite. In part 2 I changed the setup so a hook stop cannot trigger a rewrite. A later wiring test showed the guard also blocks `gh issue comment --help`, because the hook stops any `gh issue comment` call without a readable body, and Claude Code stopped there and asked me instead of trying `gh help issue comment`. `HANDOFF.md` now carries the rule "Report voice guard stops and do not rewrite silently."

**Unit 7: a ship-gate that stopped twice on my real branch before it passed**

The ship-gate ran on `docs/228-where-text-changes-go` against `main`. It first stopped on a malformed row in my gate manifest (its excluded table had three columns, and the gate reads four; Claude drafted that file), then on pr-precheck's decisive-evidence check, because my evidence wasn't real yet. It passed once I generated real evidence (`test_evidence.md`). The passing run showed the manifest's two repo checks, `uv run mkdocs build -q -f docs/mkdocs.yml -d /tmp/nomnom-site` (ok, 3s) and `uv run ruff check` (ok, 0s), then a live pr-precheck with all eight checks at pass. The gate then allowed a 30-minute window to open the PR without changing anything.

**Unit 8: a PR-monitor that read a quiet PR and drafted a wait note, plus a playbook**

I installed the PR-monitor (the install log shows it cloning `ai301-unit8-starter`) and ran it against PR #528 on 2026-11-01 at 23:54 local (2026-11-02T04:54Z). The state-only run read the PR as QUIET: opened 2026-11-02T04:43Z, no checks reported, mergeable but with merge state BLOCKED, no review decision, 0 comments or reviews. The full run drafted a move through the polite nudge form, "No nudge. Wait note only.", citing the playbook rule "First response: 14 days of quiet is normal here," so a nudge becomes allowed on 2026-11-16T04:43Z if the thread stays empty. The thread didn't stay empty: on 2026-11-03 the maintainer, offbyone, closed PR #528 without merging, commenting that he isn't currently accepting documentation changes that are mainly written by an LLM. The monitor run came before that, so its QUIET read and wait note were overtaken.

---

## Why I Chose This Issue

I looked for a first issue in the wild. I first claimed `usemoss/moss#426`, then found three people had already asked for it. I considered `CSET #926`, built a fix that passed tests locally, then dropped it because the issue was missing from the Issue Selection site; that commit was never pushed. `mo_pack` and a Linux-only lock file were setup problems on the CSET work, so they're context for that dropped work and have nothing to do with NomNom. I also rejected other issues because they were already taken or already fixed, among them Beets #4872, #5411 and #2836 and scaler #202.

I chose `WorldconVotingSystems/nomnom#228`, "Provide clear documentation about where changes to text should happen", for its simplicity. I ran the scout on moss#426 and nomnom#228 as direct URLs, so there was no query or ranking signal. It accepted both. It rated nomnom#228 moderate: the issue links a few examples, and I'd have to decide where the doc belongs and where the line sits between NomNom and convention-specific templates. It ranked nomnom#228 second, below moss#426, because the task is documentation only and has nothing to do with agents or LLMs, and I'd told the scout I'd rather fix a bug or add a feature than write only docs. The repo passed the scout's checks: a human commit by Chris Rose on 2026-10-18, a release (v2027.0.0) on 2026-10-17, no assignee, no comments, no linked PRs, and no contribution or AI policy file. The scout accepted this issue, so I didn't choose against a reject. I did choose the lower-ranked of its two accepts. I hoped to learn how to use AI to assess, develop and improve a contribution, and how to build it into each step of the PR cycle, from choosing an issue through to answering a maintainer.

Claim comment: https://github.com/WorldconVotingSystems/nomnom/issues/228#issuecomment-6569007345

**Toolkit**

- Tool: issue-scout, on both issue URLs, then repro-check on my claim-only draft, then Claude Code to post the claim. I chose to claim nomnom#228, and the draft claim was mine. Claude Code posted the claim comment, though, without showing me the text first (see Unit 6).
- What it produced: the scout's accept on both issues with the ranking above. repro-check graded my draft claim as accept: claim-specific and ai-disclosure both pass, and the six repro-report checks were marked "not yet applicable: claim-only draft". It noted two strains against my voice rules: I asked the admin-section question before saying what I'd checked, and "I then plan to add a page…" sat close to promising a deliverable. The posted comment keeps my first step, the report-back date of Saturday 7 November, and the Claude Code disclosure.
- Difficulties: my voice-guard hook blocked the first post. Claude Code cut the "I then plan to add a page" sentence, rewrote my question as "Is `docs/docs/admin/` the right home for a page on this, given that the Hugo team are the main readers?", saved it to `claim-draft.md` and posted it without checking with me. So the comment on the issue differs from the draft repro-check graded.
- What changed: the hook-stop rule from part 2 (Unit 6), so a stop can't trigger a rewrite.

---

## Understanding the Issue

**Problem Description**

The NomNom docs don't say whether a change to a site's text belongs in NomNom or in a convention's own repository. The issue, opened by the maintainer and labelled good first issue, says it's not obvious. It links three examples: two instruction changes in `seattle-2025` (#25, removing a non-linking underline from the nomination instructions, and #24, a sub-list in the nomination instructions) and NomNom #220 (the time zone on timestamps in generated emails).

**Expected Behavior**

The docs tell an admin which repository owns a given piece of text, and how to override a NomNom template from a convention.

**Current Behavior**

On `main` at commit `22d531f`, `docs/docs/admin` held seven pages (`advisory_votes.md`, `canonicalization.md`, `getting_started.md`, `hugo_packet.md`, `nomination.md`, `paper_ballots.md`, `voting.md`), none about this, and `changing_text` appeared in the docs 0 times.

**Affected Components**

- `docs/docs/admin/`, the admin docs, and `getting_started.md` in it (the place to link from).
- `convention-template/`, including `convention-template/config/settings.py.jinja` (the override order) and `convention-template/{{ app}}/templates` (the templates a new convention starts with).
- `src/nomnom/<app>/templates/`, NomNom's default templates.

**Toolkit**

- Tool: issue-scout's read-out of the issue, then plan-check, whose grounded-cause check graded my stated cause against the issue body. Claude also read the issue and the template folders for me and summarised them.
- What it produced: the scout's note that the task needs a decision about where the doc belongs and where the NomNom/convention line sits. plan-check passed the cause ("the docs do not say whether a text change belongs in NomNom or in a convention's repository") as matching the issue's "it's not obvious".
- Difficulties: none recorded in the sources.
- What changed: nothing recorded.

---

## Reproduction Process

**Environment Setup**

macOS 26.4.1 on Apple silicon (the uv build string reads `aarch64-apple-darwin`), Python 3.13.11, uv 0.12.12, NomNom checked out at commit `22d531f`, branch `docs/228-where-text-changes-go`. The docs build runs through `uv run mkdocs build -f docs/mkdocs.yml`, and lint through `uv run ruff check`. The docs build used mkdocs 1.6.1. Setup was `gh repo fork WorldconVotingSystems/nomnom --clone=false`, `git clone` of my fork, and `uv sync --dev`, all run by `run-228.sh`. `pytest` needs `NOM_DB_*` settings and a database, so I didn't run it. The sources record no setup problems on #228. The setup problems I hit (`mo_pack`, the Linux-only lock file) belonged to the dropped CSET work.

**Steps to Reproduce**

This is a documentation issue, so there's no bug to run. The reproduction is a before-state check:

1. Check out `main` at `22d531f`.
2. List the pages in `docs/docs/admin`.
3. Observed result: seven pages, none about where text changes belong, and 0 mentions of `changing_text`.

**Reproduction Evidence**

- **Commit showing reproduction:** the base commit is `22d531f` on `main`. No separate reproduction commit exists, because a docs gap has no failing run to commit. The fix commit is `e311ee9`. The commit is on my fork: https://github.com/tbamisaye1/nomnom/commit/e311ee9
- **Screenshots/logs:** the "Before" block of `test_evidence.md` (the file listing above and `Mentions of changing_text in main: 0`).
- **My findings:** the gap is real on `main`. The plan-check run recorded no repro controls for this issue ("Docs issue with no repro controls") and graded the repro-dependent checks as "trigger absent". `run-228.sh` searched `docs/docs` for override guidance on `main` and printed `(none)`, which backs the PR's line on this. That search output is in my run log but not in `test_evidence.md`. pr-precheck flagged the sentence for that reason.

**Toolkit**

- Tool: shell commands on my Mac, and pr-precheck's decisive-evidence check to grade the evidence. Claude Code drafted the scripts, and I ran them on my Mac.
- What it produced: `test_evidence.md`, with a before block and an after block.
- Difficulties: the ship-gate stopped on decisive-evidence until the evidence was real (see Unit 7).
- What changed: I generated real evidence, and the gate then passed.

---

## Solution Approach

**Analysis**

Cause, in the plan's words: the docs don't say whether a text change belongs in NomNom or in a convention's repository, or how template overrides work. The mechanism the page documents: `convention-template/config/settings.py.jinja` lists the convention app before NomNom's apps, so Django uses the convention's copy of a template when both have the same path.

**Proposed Solution**

Add `docs/docs/admin/changing_text.md` and link it from Getting Started. The page gives one test for deciding ("should this change appear on every convention's site, or only on yours?"), lists the templates a convention starts with, and explains how to override any other template. Not changing: templates, settings, code or the template project.

**Implementation Plan**

1. Confirm the override order in `convention-template/config/settings.py.jinja`.
2. Confirm each listed template exists in `convention-template/{{ app}}/templates`.
3. Write the page and link it from Getting Started.
4. Check these outcomes:
   - `uv run mkdocs build -f docs/mkdocs.yml` exits 0.
   - The build contains `admin/changing_text.html`, which didn't exist before.
   - The built Getting Started page contains a link to `changing_text`.
   - Every template path named on the page exists in the repository.
   - Applying the page's test to the issue's examples places the two seattle-2025 instruction changes in the convention repository and leaves #220 as an unclear case, which the page's last section covers.

Deviations and open items:

- plan-check noted the plan's steps were already done when it graded the plan: `changing_text.md` existed and commit `e311ee9` had added it. The page came before the checked plan.
- plan-check said the plan was ready to build from but not ready to post, because my scope file keeps the plan comment off the issue thread. It also said `plan-comment.md` mostly repeated my claim comment and never mentioned the new page.
- The plan lists three risks: no maintainer has confirmed `docs/docs/admin/` as the home (the page can move); the template list can go stale if `convention-template` changes; and "file unclear cases in the convention repository first" is my suggestion, not an existing project rule.

**Toolkit**

- Tool: plan-check, which graded the plan package against the issue. Claude drafted `plan.md` and `plan-comment.md`.
- What it produced: accept. All six required checks and the preferred honest-unknowns check passed. It also gave a list of fixes that didn't change the verdict, including the point above about `plan-comment.md`. Plan-check first rejected a generic test plan, and it accepted the revised one. The rejected plan's last step only said "Build the docs and run the checks." The revised step named the outcomes to check: build exit code, the built page, the Getting Started link, every template path, and the page's test applied to the issue's examples. It also added a risks section.
- Difficulties: plan-check couldn't read `voice-guide.md`, because the file doesn't exist in the skill directory, so it couldn't check the comment against my voice rules. It also noted the procedure doesn't say what to do when a docs issue has no repro evidence.
- What changed: the revised test plan. Neither file changed after that list, because the plan comment stays local and the PR had already passed the gate.

---

## Testing Strategy

The observable checks are the plan's step 4, run against commit `e311ee9` and compared with `main` at `22d531f`:

- **Docs build:** `uv run mkdocs build -f docs/mkdocs.yml` exit code 0, and `changing_text.html` in the output. Before: 0 mentions of `changing_text` on `main`, and no such page in the listing.
- **Link:** the built Getting Started page contains `href="changing_text.html">Changing the Text on Your Site`, from the source line `- Review [Changing the Text on Your Site](changing_text.md) to find where your wording belongs`.
- **Paths:** every template path named on the page exists in the repository ("Missing paths: 0").
- **The page's test on the issue's examples:** seattle-2025#25 and #24 go to the convention repository, and #220 lands in the page's last section as unclear. That matches the issue. pr-precheck called this the closest call, because it's a judgment applied by hand and not command output.
- **Repo checks:** `uv run ruff check` passed, and the pre-commit file checks (fix end of files, trim trailing whitespace) passed. The ship-gate also ran the docs build and lint itself (ok in 3s and 0s).
- **Not run:** `pytest`. It needs `NOM_DB_*` settings and a database, and the change only touches Markdown. The manifest records the skip with a date.

One claim on the page isn't checked by any evidence: it says a comment above the convention's entry in `config/settings.py` marks the ordering. pr-precheck flagged this, and plan step 1 confirmed `settings.py.jinja` but not the comment. The comment is in `convention-template/config/settings.py.jinja`, which generates that file, so the page is accurate for a new convention.

**Toolkit**

- Tool: `uv run mkdocs build`, `uv run ruff check` and pre-commit on my Mac, then the ship-gate and pr-precheck.
- What it produced: `test_evidence.md`, and a ship-gate PASS with all eight pr-precheck checks at pass.
- Difficulties: the first two ship-gate runs stopped (Unit 7). pr-precheck also flagged that the description names `ruff check` and the pre-commit file checks without the exact commands.
- What changed: I generated real evidence for the gate. I did not add the exact commands to the PR description.

---

## Implementation Notes

**2026-11-01**

Scout ran on moss#426 and nomnom#228 and accepted both, ranking nomnom#228 second as a docs-only issue. I chose nomnom#228. My claim comment landed on the issue after the voice guard stopped one sentence and Claude Code rewrote and posted it (details in Unit 6 and under Why I Chose This Issue).

**2026-11-01**

I wrote `docs/docs/admin/changing_text.md` and linked it from Getting Started (commit `e311ee9`, local branch `docs/228-where-text-changes-go`). Claude Code drafted the page; I read it. The page has an intro, a decision table, a section on how a convention overrides NomNom, a list of six starter templates (the evidence lists nine path lines, one of them repeated), instructions for copying a NomNom template, and an "If you are not sure" section. Plan-check then graded the plan as accept, noting the build was already done. Wrong turns: I wrote the page before checking the plan, so plan-check ended up grading steps that were already done. My first test plan only said "Build the docs and run the checks", and plan-check rejected it as too generic until I named the outcomes to check. Before NomNom I had also claimed moss#426, which turned out to be contested, and built a CSET #926 fix that I dropped.

**2026-11-01**

The ship-gate stopped on a malformed manifest row, then on pr-precheck's decisive-evidence check. I generated real evidence (`test_evidence.md`) and the gate passed: docs build ok, lint ok, pr-precheck accept. pr-precheck left tensions that didn't change a grade: the PR description's disclosure says "Claude Opus 5.5" while this session's commit attribution says Claude Sonnet 5.5, the settings comment is unchecked, and the template list can go stale.

**2026-11-01 (opened 2026-11-02T04:43Z)**

PR #528 opened ("Document where changes to site text belong"), through Claude Code, with no hook stopping the command and no files edited. I recorded the monitor's state at 23:54 local (QUIET, nothing to address).

**2026-11-03**

offbyone closed PR #528 without merging. His comment said he appreciated the wish to contribute but isn't currently accepting documentation changes that are mostly written by an LLM. The PR's AI disclosure had said Claude Code drafted the page and the description. Branch `docs/228-where-text-changes-go` and commit `e311ee9` are still on my fork.

**Toolkit**

- Tool: Claude Code drafted the page, the PR description, the playbook and the scripts. I ran the scripts on my Mac, read the page and made the final calls to post, except that Claude Code posted the claim comment's rewrite without showing it to me first.
- What it produced: the page (`changing_text.md`), the link in `getting_started.md`, the PR description and the scripts, including `overnight-228.sh`, which sets up the monitor and writes `story-draft.md`.
- Difficulties: the silent rewrite of my claim; ship-gate stops; a disclosure naming a model that the session attribution contradicts.
- What changed: the hook-stop rule from part 2 (Unit 6), real evidence before the gate, and a model-name check on the disclosure still open above.

---

## Pull Request

**PR Link:** https://github.com/WorldconVotingSystems/nomnom/pull/528

**PR Description:** The PR is titled "Document where changes to site text belong", closes #228, and is based on commit `22d531f`. It says it wasn't obvious whether a text change belongs in NomNom or in a convention's own repository, and that the PR adds an admin page with one test for deciding, a list of the templates a convention starts with, and how to override any other template; Getting Started now links to it. Its evidence section reports the environment (macOS 26.4.1, Python 3.13.11, uv 0.12.12), the before and after checks from the Testing Strategy, and that `pytest` wasn't run. Its AI disclosure says Claude Code drafted the new page and the description, and that I read the page and ran the documentation build and the checks on my machine. That text comes from `pr-draft.md`, the draft pr-precheck graded, and the sources don't show the posted version.

**Maintainer Feedback:**

- 2026-11-01 (monitor run, 23:54 local): none yet. The thread has 0 comments or reviews, no review decision, and no checks reported; the PR is mergeable with merge state BLOCKED. The monitor reads this as quiet, and the playbook treats 14 days of quiet as normal for a first response.
- 2026-11-01: nothing to address yet.
- 2026-11-03: offbyone closed the PR without merging, with one comment: he appreciates the desire to contribute but is not currently accepting primarily LLM-authored documentation changes. There was no review of the content itself.

**Status:** Closed without merging (2026-11-03)

**Toolkit**

- Tool: Claude Code drafted the PR description and opened the PR through its command. pr-precheck, inside the ship-gate, graded the package. The PR-monitor read the PR afterward. I read the page and made the call to open the PR.
- What it produced: PR #528, a pr-precheck accept with eight passes, and a monitor report with a drafted "wait note only" move.
- Difficulties: pr-precheck's tensions listed under Implementation Notes (model name, an unsupported "search found nothing" sentence, and commands named loosely). The monitor shows no checks and a BLOCKED merge state, which is all it reports.
- What changed: the gate and pr-precheck checked that the PR was honest and evidenced, but none of my checks asked whether this project accepts AI-drafted work. The repo had no contribution or AI policy file, which the scout recorded, so the only signal was the maintainer himself.

---

## Learnings and Reflections

**Technical Skills Gained**

**Challenges Overcome**

**What I'd Do Differently Next Time**

---

## Resources Used

- Issue #228, with its three examples: https://github.com/WorldconVotingSystems/nomnom/issues/228
- My claim comment: https://github.com/WorldconVotingSystems/nomnom/issues/228#issuecomment-6569007345
- `convention-template/config/settings.py.jinja` in the NomNom repository, which confirmed the override order the page describes.
- PR #528 and offbyone's closing comment: https://github.com/WorldconVotingSystems/nomnom/pull/528
