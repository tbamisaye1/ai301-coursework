# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

tbamisaye1

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68#issuecomment-5872174052

> Hi, I'd like to work on this as my first contribution to Path Review. I haven't run anything yet, so this is my plan rather than a result.
>
> Next, I will:
>
> 1. Set up the repository from its own documentation (`docs/SETUP.md` and the install line in the Makefile) in a fresh virtual environment.
> 2. Call `KeywordSearcher().index([])` directly to see whether it raises `ZeroDivisionError`, and capture the full traceback to find where the division by zero happens.
> 3. Run `pytest tests/unit/test_keyword_search.py -k test_empty_index` to check whether the `xfail` test (manifest H-01) fails for the reason this issue describes and not for some other reason.
> 4. Compare `index()` with `search()`, which already returns an empty list when nothing has been indexed.
>
> I'll post my environment, the exact commands I ran, and the output I get, including if I can't reproduce it.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68#issuecomment-5872690654

> I reproduced this on `main` at commit `2f4e82f`.
>
> **Environment**
>
> - Python 3.11.8 (CPython), in a fresh virtual environment
> - macOS 26.4.1 on a MacBook Air (arm64)
> - rank-bm25 0.2.2 (the repository requires `rank-bm25>=0.2.2`), structlog 26.1.0, pytest 9.1.1
> - Repository: my fork of `codepath/pathreview-ai301-fa26-s3`, cloned at commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088` (the same commit as upstream `main`)
>
> **Setup**
>
> From an empty folder:
>
> ```bash
> git clone https://github.com/codepath/pathreview-ai301-fa26-s3.git
> cd pathreview-ai301-fa26-s3
> python3 -m venv .venv
> .venv/bin/pip install --upgrade pip
> .venv/bin/pip install -e ".[dev]"
> ```
>
> This is the install line from the repository's setup documentation. I did not start the database, Redis or the API, because this code path does not use them.
>
> **Run 1: the call from the issue**
>
> ```bash
> .venv/bin/python -c "from rag.retriever.keyword_search import KeywordSearcher; KeywordSearcher().index([])"
> ```
>
> ```
> Traceback (most recent call last):
>   File "<string>", line 1, in <module>
>   File ".../rag/retriever/keyword_search.py", line 25, in index
>     self.bm25 = BM25Okapi(tokenized_corpus)
>                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^
>   File ".../.venv/lib/python3.11/site-packages/rank_bm25.py", line 83, in __init__
>     super().__init__(corpus, tokenizer)
>   File ".../.venv/lib/python3.11/site-packages/rank_bm25.py", line 27, in __init__
>     nd = self._initialize(corpus)
>          ^^^^^^^^^^^^^^^^^^^^^^^^
>   File ".../.venv/lib/python3.11/site-packages/rank_bm25.py", line 52, in _initialize
>     self.avgdl = num_doc / self.corpus_size
>                  ~~~~~~~~^~~~~~~~~~~~~~~~~~
> ZeroDivisionError: division by zero
> ```
>
> **Run 2: control with one document**
>
> ```bash
> .venv/bin/python -c "
> from rag.retriever.keyword_search import KeywordSearcher
> s = KeywordSearcher()
> s.index([{'id': 1, 'text': 'python programming'}])
> print(s.search('python', top_k=10))
> "
> ```
>
> ```
> 2026-09-28 10:45:22 [info     ] keyword_index_built            chunk_count=1
> 2026-09-28 10:45:22 [info     ] keyword_search_complete        query_len=1 results_count=1
> [{'id': 1, 'text': 'python programming', 'bm25_score': -0.2746530721670274}]
> ```
>
> With one document, `index()` builds the index and `search()` returns the result, so the failure only happens when the list of chunks is empty.
>
> **Run 3: the covering test, with the `xfail` marker ignored**
>
> The test is marked `xfail(strict=True)`, so pytest normally hides its error. `--runxfail` runs it as a normal test so the real failure is visible:
>
> ```bash
> .venv/bin/pytest tests/unit/test_keyword_search.py -k test_empty_index --runxfail
> ```
>
> ```
> tests/unit/test_keyword_search.py F                                    [100%]
>
>     def test_empty_index(self, searcher):
>         """Test searching on empty index."""
> >       searcher.index([])
>
> tests/unit/test_keyword_search.py:140:
> rag/retriever/keyword_search.py:25: in index
>     self.bm25 = BM25Okapi(tokenized_corpus)
> .venv/lib/python3.11/site-packages/rank_bm25.py:52: ZeroDivisionError
>
> self = <rank_bm25.BM25Okapi object at 0x1038330d0>, corpus = []
> >       self.avgdl = num_doc / self.corpus_size
> E       ZeroDivisionError: division by zero
>
> FAILED tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index - ZeroDivisionError: division by zero
> ====================== 1 failed, 16 deselected in 1.52s ======================
> ```
>
> **Expected:** `index([])` finishes without an exception, and a later `search()` returns `[]`, the same way `search()` already returns `[]` when `index()` was never called.
>
> **Actual:** `index([])` raises `ZeroDivisionError: division by zero`. The traceback shows the division happens inside rank-bm25 (`rank_bm25.py`, line 52, `num_doc / self.corpus_size`), where `corpus_size` is 0 because the corpus is empty. `KeywordSearcher.index()` passes the empty corpus straight to `BM25Okapi` at `keyword_search.py` line 25, with no check for the empty case like the one `search()` has. The covering test `test_empty_index` fails at the same line (`test_keyword_search.py:140`) with the same error, so the `xfail` is failing for the reason this issue describes.
>
> I did not change any source code or test markers to get these results. Next I'll look at how `index()` should handle an empty list so that it matches `search()`, and I'll share what I find here before opening a pull request.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Run 1: full run of all 20 scored packages with my first rubric: **18/20** scored items. This is the run committed in `eval-run.txt`:

   > agreement: 18/20 scored items  (bar: 18/20: PASS)

   > categories: clear-accept 6/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4

The first full run cleared the bar with every category matched, including the single disclosure package, so I kept the rubric as it was instead of spending more credit on revisions. The two disagreements (pkg-03 and pkg-05) were both good reports that my rubric rejected; I explain pkg-05 below.

**Package analysis**

`pkg-05` (conda/conda#16543). My rubric's decision: **reject**. Gold label: **accept**.

The check that rejected it was `steps-rerunnable`. The report says it "wrote a minimal `env.yml` containing a valid `dependencies:` list plus a `category:` section" but never pastes the file itself. My pass condition says every input must be "shown, public, or taken verbatim from the issue", so my grader treated the file as a missing input.

The gold label accepts it because the description is enough to rebuild the file: any valid dependencies plus one unrecognized `category:` section. The output shown proves the bug: the `EnvironmentSectionNotValid` warning is printed to stdout above the JSON, and piping it into `python3 -m json.tool` fails with "Expecting value: line 1 column 1 (char 1)". My rule treats "described precisely" the same as "not shown", which is stricter than this package needed.

**Check rationale**

`artifact-shown`, quoted as it currently reads in `tools/repro-check/rubric.md`:

> The report shows at least one artifact from its own run that relates to on the behavior the issue describes. Fails if the report only asserts ("I verified", "reproducible", "+1, seeing this too"), or if its artifacts only show the program running normally and say nothing about the reported behavior.

A reproduction is only as good as the output behind it. If someone says they can see the bug but shows nothing, like pkg-04's "+1 also seeing this!!", a maintainer has no way to check the claim. So this check asks for real output from the person's own run. Any output is not enough, though: pkg-14 shows zellij running normally (a version banner and a session list), which proves nothing about a blank pane, so the second clause catches artifacts that are real but say nothing about the bug.

I deliberately kept this check narrow. It only asks whether there is real evidence at all. Whether that evidence is the right bug is a separate check, `same-behavior`, which catches packages like pkg-02, where the output is real but shows a graceful argument error instead of the crash the issue reports. Splitting the two means a failed grade tells me which problem the report has.

**Trade-offs**

`steps-rerunnable` is strict: it wants every input shown, not only described. That strictness is what rejects pkg-18, whose steps only work inside a private monorepo with an unshared config. The cost is pkg-05, a good report that describes its `env.yml` instead of pasting it, which my rubric rejects against a gold accept. I accept that miss. Loosening the rule to "described precisely enough to rebuild" would ask the grader to judge how detailed a description is, and pkg-18 could pass that way too ("a large Go monorepo with a custom config" is also a description). If I did loosen it, I would re-run pkg-05 with pkg-18 and pkg-06 as canaries using `--only` before spending a confirming full run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
