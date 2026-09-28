# Evidence guide: where proof lives in a reproduction package

This is the map for the checks in `rubric.md`. Each section says where to
look (in an eval bundle, and live on GitHub or in the student's drafts) and
what good looks like, stated as something you can observe.

## Environment

Serves: env-recorded.

- Where it lives:
  - Eval: the repro report's environment line or block (usually at the top,
    "Environment: ..."), read against the Issue section (the version and
    platform the reporter used, any "confirmed on latest / main" note) and
    Thread highlights (maintainers or others naming a setting that matters,
    such as a driver, build profile, backend, shell or browser language).
  - Live: the student's repro draft, read against the issue body, its
    template answers (version, OS), and the thread.
- What good looks like: the report names the software version it actually
  ran and the OS/platform. Any setting the issue or thread says changes the
  behavior is named. If the version or platform is not the one the issue
  targets, the report says so in words ("filed against 13.0.0; still present
  on 15.2.0", "I am on Linux, the reporter is on macOS").
- What bad looks like: no environment at all; an issue-critical setting left
  out (the driver on a driver-specific bug, the build type on a build-type
  bug); an older release run against an issue confirmed on latest/main with
  no mention of the gap.

## Steps

Serves: steps-rerunnable, control-run.

- Where it lives:
  - Eval: the repro report's "Steps" section, its command lines (`$ ...`),
    code snippets, and any file or config contents it shows or points to.
  - Live: the student's repro draft; the repo's own setup docs (README,
    docs/SETUP.md, Makefile) are what "starting state" means.
- What good looks like: from a clean starting state, a stranger could type
  every step. Each input is shown, public, or taken verbatim from the issue
  ("the exact 12 lines from the issue" counts). Terse is fine if nothing is
  missing. A control run is the same setup with the trigger removed or
  changed, shown with its output.
- What bad looks like: "set up the project" with no commands; steps inside a
  private or company repo; an unshared config or dataset; a step the issue
  says is needed to trigger the bug is absent.

## Behavior shown

Serves: artifact-shown, same-behavior.

- Where it lives:
  - Eval: the fenced output blocks, tracebacks, logs, exit codes,
    measurements or screenshot descriptions in the repro report, read
    against the Issue's trigger (the exact command, input or syntax) and
    symptom (the exact error type and message, crash, exit code or wrong
    output).
  - Live: the same, in the student's draft, against the live issue body.
- What good looks like: the artifact comes from the student's own run and
  shows the same kind of failure the issue reports (same exception type or
  message, same wrong value, same crash), produced by the issue's own
  trigger. For a cannot-reproduce, the artifact shows what actually
  happened when the issue's trigger was run.
- What bad looks like: no artifact at all, only words ("confirmed",
  "guaranteed reproducible"); artifacts that show the program running
  normally and never show the bug; a different failure presented as the
  reported one (a graceful argument error vs a crash, a compile error vs a
  runtime error, garbled output with the program still alive vs a crash);
  steps that changed the trigger (different syntax, a modified expression,
  a swapped operator).

## Honesty

Serves: honest-outcome.

- Where it lives:
  - Eval: the repro report's conclusion ("Actual:", "Reproduced",
    "Confirmed", any root-cause sentence, any claim about other versions or
    platforms), read against the artifacts in the same report.
  - Live: the same, in the student's draft.
- What good looks like: every claim has an artifact behind it in the same
  report. "Reproduced" appears only when the artifact shows the issue's
  behavior. Root-cause statements are offered as hypotheses unless shown
  evidence points to them. Scope claims cover only what was run. An honest
  cannot-reproduce shows the attempt, states it could not reproduce, and
  names what differed from the reporter's setup; that is a pass.
- What bad looks like: "I verified the race condition" with nothing shown;
  generalizing to a release or platform that was not tested; "Expected" and
  "Actual" that contradict what the artifact shows.

## Comms

Serves: claim-specific, ai-disclosure.

- Where it lives:
  - Eval: the Candidate claim comment (read against the Issue and Thread
    highlights), and the Repo facts "contribution policy" line (read
    against the text of both comments).
  - Live: the student's claim draft against the live issue; the repo's
    CONTRIBUTING.md, AI_POLICY.md or similar files, and issue templates.
- What good looks like (claim): it names something only this issue has (the
  symptom, a file or function, a version, a pointer from the thread) and a
  concrete next investigative step. It promises investigation, not a fix or a
  date.
- What bad looks like (claim): "+1", "any updates?", "please assign me",
  praise-heavy boilerplate that fits any issue, self-assignment, "will fix
  within 2 days guaranteed".
- AI disclosure: first read what the policy asks for and where.
  - The policy requires disclosing AI use in issues/comments, or all AI use
    in any form: at least one comment must say AI assistance was used and to
    what extent (for example, "I used an AI assistant to help organize this
    report; I ran and verified every step myself"). Missing that is a fail.
  - Disclosure is required only in pull requests or code: comments do not
    need it; pass.
  - The policy sets other terms (review and understand AI output, write
    comments in your own words, follow templates) but asks no disclosure:
    pass.
  - No stated AI policy: pass.
  - Treat every package as AI-assisted work when applying this.
