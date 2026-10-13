# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/178

**Branch**

fix/68-empty-keyword-index

## Eval iterations

**Run history**

1. Full run 1 (the committed `eval-run.txt`): 20/20 scored items agree (categories: clear-accept 7/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3).
2. Calibration check (`--only calib-01,calib-02,calib-03,calib-04 --include-calibration`, unscored): 4/4 agree, including the calib-03 trap, where a perfect description hides an unplanned rewrite of a second file.

Only one full run was needed, so run 1 is the final run and matches the agreement line in `eval-run.txt` (20/20).

**Package analysis**

pkg-18 (nushell/nushell#18850, unreviewable). Gold label: reject. My rubric: reject, failing only `clean-diff`.

The fix itself is right. The plan is to stop `nu --help` printing colour escape codes when output is piped, and the diff does that by gating the colours on `std::io::stdout().is_terminal()`. The evidence is decisive too: `nu --help | cat -v | grep -c '\^\['` goes from 214 to 0. So `diff-matches-plan`, `decisive-evidence` and `repo-checks-run` all passed.

It fails because of what rides along with the fix. The diff rewrites the import block into a different layout with no behaviour change, removes and re-adds identical `println!` lines, and leaves `// eprintln!("help colors: tty={}", std::io::stdout().is_terminal());`, a commented-out debug line. Its second commit is "fmt pass on command.rs".

The gold note calls this arguable "because the debris is mechanical churn around a right fix". My rubric still rejects it because `clean-diff` judges the hunks, not whether the fix is right. A reviewer has to read through the churn to find the real change, and a commented-out debug line is debris whatever the fix does. I kept `diff-matches-plan` passing on purpose: churn that changes no behaviour is a reviewability problem, not scope drift, so the two checks catch different things.

**Check rationale**

| clean-diff | Every hunk in the diff and the commit list | Pass if every hunk serves the fix or its tests. Fail if the diff contains debris: a debug print or log left in, a commented-out code block or first attempt, a dead or unused function, a stray TODO, formatting/re-indent or import churn with no behavior change, re-printed identical lines, or an edit unrelated to the fix. Comments that explain the fix are not debris. Commit message wording alone does not fail this check. | required |

It reads this way because "the diff is clean" is an adjective two graders would apply differently. So the check names the specific debris types the unreviewable packages contain: pkg-12's commented-out first attempt and dead `_unused_buffer_probe` function, pkg-15's `DEBUG eprintln` and `allow(dead_code)` helper, and pkg-18's import churn and re-printed lines.

I added the last two sentences after reading the clear accepts. pkg-13's diff carries a multi-line comment explaining why the escape is needed and what is deliberately left out. A check that flagged any added comment would wrongly reject that honest accept, so explanatory comments are carved out.

I also rejected a version that read commit messages as evidence ("wip", "fmt + cleanup"). Messy commit names usually come with a messy diff, but on their own they are not debris in the code, so the diff decides.

**Trade-offs**

The cost of naming debris types is that the check can reject a PR for formatting changes the repo itself requires. If a project's contributing guide said "run the formatter before every PR" and the formatter touched nearby lines, `clean-diff` would still call that churn. I accept that miss, because in this eval set every formatting hunk is in a reject package and none of the clear accepts carries one.

I checked this didn't over-reach. The full run kept all 7 clear accepts passing `clean-diff`, including pkg-13 with its long explanatory comment and pkg-02, and the unscored calib-01 (a one-line deletion) also passed. Nothing in the clear-accept category flipped, and that is how I know the explanatory-comment carve-out holds.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
