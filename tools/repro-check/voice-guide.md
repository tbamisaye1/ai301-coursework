# Voice guide: how I talk upstream

## Who I am in threads

I'm a student making a first contribution to this repo. I'm comfortable in
Python but new to this codebase, so I say what I ran and what I saw, and I
label anything I haven't checked yet as a guess. When I'm rushed I write
short, abbreviated and misspelled, so these rules exist to slow me down
before anything gets posted.

## Rules I write by

### Rule: write words out in full

I don't use casual abbreviations or shorthand (env, w/, pls, idk, tbh,
repro'd, OP). Names of tools, libraries and error types (BM25, macOS,
`ZeroDivisionError`) stay as they are.

- Wrong: "repro'd on my env w/ py 3.12, same err as OP"
- Right: "I reproduced this on my machine with Python 3.12 and got the same error as the original report."

### Rule: check the spelling before posting

I read the whole comment through once, slowly, before posting, and fix
every typo. A comment full of mistakes reads as careless, even when the
evidence is good.

- Wrong: "Reproduced teh error, full trackeback bellow, happens evry time on an empyt index"
- Right: "Reproduced the error; the full traceback is below. It happens every time on an empty index."

### Rule: explain fully, don't be curt

A stranger should understand my comment without asking me anything. Every
report says what I ran it on, exactly what I ran, what I expected, what
actually happened, and what I'll do next.

- Wrong: "Reproduced. Same error."
- Right: "Reproduced on Python 3.12.4, macOS 15, rank-bm25 0.2.2, commit `<hash>`. I ran `KeywordSearcher().index([])` after `pip install -e \".[dev]\"`. I expected no exception; instead it raised `ZeroDivisionError` (traceback below). Next I'll run `test_empty_index` to confirm the xfail fails for the same reason."

### Rule: promise the next step, not the outcome

I promise the investigation I'll do next, never a fix, a PR or a date,
because I don't know yet what the fix is or how long it will take.

- Wrong: "ill have a fix up by the weekend"
- Right: "Next I'll run `index([])` on a clean install and report back what I see."

### Rule: only claim what I ran

I say "I reproduced" only after I have the output in front of me, and I
paste it. Before that, I say what I plan to check, and I name anything that
is a guess.

- Wrong: "confirmed, its the BM25 divide by zero"
- Right: "The traceback ends at `num_doc / self.corpus_size` in `rank_bm25.py`, so the division looks like the cause; I haven't checked other retrievers."

## Things I never post

- A promise of a fix, a PR, or a deadline.
- "+1", "same here", "same as above, can confirm" or "any updates?" with nothing else. Even when classmates have already posted a reproduction, mine reports my own environment and my own output.
- "Please assign me" or "keep this reserved for me".
- A root cause stated as fact when I haven't shown the evidence for it.
- A comment I haven't read through in full before posting.
