# Toolkit Portfolio

**Student:** Tobi Bamisaye

## Featured Tools

Issue-scout and the voice guard changed the most during my contribution. The scout needed better filters after its first scan. The voice guard exposed a problem with how comments were reviewed before posting. Plan-check helped me turn a vague test plan into specific checks. Ship-gate made me generate real evidence before opening the PR.

**issue-scout**

**What it is, and the judgment hole I wrote:** I built issue-scout around my Unit 1 rubric. My search strategy focused on Python work in established projects, especially agent and LLM tooling.

**How it evolved, dated:** The first Unit 5 scan ranked five issues that were already taken. I added `-linked:pr` to exclude issues with linked pull requests. The candidate pool dropped from 153 to 96. I also tightened two filters after finding incorrect matches. One was treating “ui” inside “build” as a match.

In Unit 6, I replaced my original scope check with the scope-feasible patch. Accepted issues now received a difficulty rating of straightforward, moderate, or ambitious. This gave me more useful information about the work involved.

**Evidence:** The scout accepted moss #426 despite warning that three people had already asked to work on it. It ranked nomnom #228 second and rated it moderate. These results still needed manual review. I should have ruled out the contested issue after reading its comments. I also missed NomNom’s restriction on AI-drafted contributions.

**Voice guard**

**What it is, and the judgment hole I wrote:** The voice guard checked upstream comments against the rules I wrote in my voice guide. These covered how I wanted to sound when asking questions or claiming work in someone else’s repo.

**How it evolved, dated:** On 1 November, I added a rule to `HANDOFF.md` requiring review after a guard stop. Claude Code could no longer silently rewrite and post the comment. A later false positive involving `--help` led to a question for me instead of an automatic workaround.

The guard caught the wording it was meant to catch. The posting process needed an additional review step.

**Evidence:** It blocked a sentence in my claim draft: “I then plan to add a page…” Claude Code rewrote the claim and posted it without showing me the revision. I had not reviewed the wording that went out under my name.

**Ship-gate**

**What it is, and the judgment hole I wrote:** I configured ship-gate around a manifest of checks for the contribution. This included the documentation build and ruff. I recorded a reason for skipping pytest.

**How it evolved, dated:** On 1–2 November, the first run stopped on a malformed manifest row. I had supplied three columns instead of four. The next run stopped because the required evidence had not been generated.

**Evidence:** After correcting the manifest and creating `test_evidence.md` from the actual checks, the gate passed. MkDocs completed successfully in three seconds. Ruff passed, along with all eight pr-precheck checks.

**plan-check**

**What it is, and the judgment hole I wrote:** Plan-check reviews a proposed fix and testing plan against my criteria for clear steps and specific, verifiable outcomes.

**How it evolved, dated:** On 1 November, I revised the plan to name the outcomes I would check. The revised version passed and became the basis of my Testing Strategy section. It gave me a clearer record of what a successful result should look like.

**Evidence:** My first testing plan said, “Build the docs and run the checks.” Plan-check rejected it for being too vague.

## Inventory

- **repro-check:** Accepted my claim draft. The reproduction checks did not apply to the documentation gap.
- **pr-precheck:** Ran eight checks through ship-gate and flagged two weak lines in my PR.
- **PR monitor:** Reported no activity on PR #528 and drafted a note about waiting. The maintainer closed the PR two days later.
- **CLAUDE.local.md and HANDOFF.md:** Stored repo context and instructions for later sessions.

## The Arc

**Beat 1**
Date: Late October 2026
Friction: My first scout scan ranked issues that were already taken.
Change: I added `-linked:pr` and tightened the ranking filters.
Result: The candidate pool fell from 153 to 96. The results still required a manual check of each issue’s comments.

**Beat 2**
Date: 1 November 2026
Friction: Moss #426 had several people asking to work on it. I then considered CSET #926 and dropped it.
Change: I reviewed another candidate and chose nomnom #228.
Result: I selected an issue the scout had ranked second and rated moderate. I missed the project’s restriction on AI-drafted work.

**Beat 3**
Date: 1 November 2026
Friction: The voice guard blocked my claim. Claude Code rewrote and posted it without showing me.
Change: I added the no-silent-rewrite rule to `HANDOFF.md`.
Result: A later stop involving `--help` was brought back to me for review.

**Beat 4**
Date: 1 November 2026
Friction: Plan-check rejected my generic testing plan.
Change: I named the outcomes I expected from the checks.
Result: The revised plan passed. I used those outcomes in my Testing Strategy.

**Beat 5**
Date: 1–2 November 2026
Friction: Ship-gate stopped on the manifest format and then on missing evidence.
Change: I corrected the row and generated the evidence from actual check results.
Result: The gate passed. I opened PR #528.

**Beat 6**
Date: 3 November 2026
Friction: The maintainer closed the PR because the documentation was mostly written by an LLM.
Change: I recorded the closure and the reason in my contribution story.
Result: I identified a missed step in my issue selection. Unit 6 required reading the contribution rules, including the AI policy, before claiming the issue. I should have checked this before starting the work.

## Limitations and What's Next

My tools did not check whether a project accepted AI-drafted contributions. I also failed to catch this during my own review. This was the most costly gap in the process.
The scout’s initial ranking could not reliably identify claims in issue comments. Excluding linked PRs improved the search, but it did not establish that an issue was available.
Plan-check could not find `voice-guide.md` and skipped the voice rules. The voice guard also produced a false positive on `gh ... --help`. Both need attention before I rely on them again.
The silent rewrite showed how easily my review could be skipped. The new handoff rule addresses that incident. I still need to read the final wording before anything is posted under my name.
The PR monitor’s playbook was drafted by Claude. I need to review its rules and decide which ones I want to keep.

My next change would be an AI-policy check before issue grading or claiming. I would record the relevant policy and check whether it allowed my planned use of AI before claiming the issue. I would ask the maintainer about anything unclear.

**Tool limits.**

issue-scout: The scout's initial ranking could not reliably identify claims in issue comments.
Voice guard: The voice guard also produced a false positive on `gh ... --help`.
Ship-gate: Ship-gate only ran the checks I listed in its manifest. It could not run pytest without a database. I recorded the reason for skipping it, leaving that part of the work untested. Passing the gate also gave me no assurance that the contribution met the project’s rules. The maintainer still closed the PR because of the AI-drafted documentation.
plan-check: Plan-check could not find `voice-guide.md` and skipped the voice rules.
