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

[The agreement score of each run you did, in order. A single run is a complete answer if
only one run occurred. **The last score in your list must match the agreement line in the
`eval-run.txt` you committed** — that file is the record of your final run.]

**Package analysis**

[Pick one scored package (`pkg-01` through `pkg-20` — the four `calib-` packages are never
scored). Name it by id, say what your rubric decided and what the gold label said, and
explain why your rubric read it that way.]

**Check rationale**

[Quote one check from the `rubric.md` you uploaded to `tools/repro-check/`, exactly as it reads now.
Then say why it reads that way — what you revised to get there, or what you rejected in
favour of it.]

**Trade-offs**

[Every check gives something up. Any one of these is a complete answer: a package whose
result it changes, a canary you re-ran with `--only`, a case you accept it will miss, or a
stated reason nothing changed elsewhere. "Nothing changed, and here is how I know" earns
the point in full when the reason follows.]

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
