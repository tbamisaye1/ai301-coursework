# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

---

## Posted upstream

**GitHub username**

tbamisaye1

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68#issuecomment-6012022449

**Plan for #68**, built from my reproduction above (https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68#issuecomment-5872690654).

**Diagnosis.** My Run 1 traceback goes from `keyword_search.py` line 25 (`self.bm25 = BM25Okapi(tokenized_corpus)`) to `rank_bm25.py` line 52 (`self.avgdl = num_doc / self.corpus_size`), where `corpus_size` is 0. My one-chunk control indexes and searches normally, and `search()` already returns `[]` when nothing is indexed. So the empty list looks like the only trigger, and the missing piece looks like an empty-corpus guard in `index()`, the same check `search()` already has. I have not tested whether rank-bm25 is meant to accept an empty corpus, so I am fixing it on the Path Review side.

**Change.** One method, `KeywordSearcher.index()` in `rag/retriever/keyword_search.py`: when `chunks` is empty, store the empty list, set `self.bm25 = None`, log `keyword_index_built` with `chunk_count=0`, and return before building `BM25Okapi`. Setting `bm25` to `None` also covers re-indexing a searcher that already had documents, so no stale index is left behind. In `tests/unit/test_keyword_search.py` I will remove the strict xfail marker (manifest H-01) from `test_empty_index`, as the issue and CONTRIBUTING both ask, and add one test for the re-index case.

**Not touching:** `search()`, tokenization, BM25 scoring, `hybrid.py` or other retrievers, or the rank-bm25 dependency.

**Test.** I will re-run my three runs. Run 1 should exit without a traceback instead of raising `ZeroDivisionError`. The one-chunk control should print the same result as before (`bm25_score` about -0.2747). `test_empty_index` should go from failing to passing with the marker removed, and the whole file should end at `18 passed`.

**What I checked and what I have not.** The only caller outside the class is `rag/retriever/hybrid.py` line 66, which goes through `search()`, and nothing outside `keyword_search.py` reads `self.bm25`, so setting it to `None` should be safe. I have only tested on Python 3.11 on macOS. The `18 passed` count is my prediction from my Run 3 (17 tests in the file, plus the new one); I will confirm it with the real run.

I have seen the other plans and pull requests here (#74, #83, #89); this one is from my own reproduction. Next I will build it on `fix/68-empty-keyword-index` in my fork and post the before and after output.

---

## Your branch

**Branch**

fix/68-empty-keyword-index

**Evidence**

Before (on `main` at `2f4e82f`, Python 3.11.8, macOS, rank-bm25 0.2.2):

```
$ git rev-parse --abbrev-ref HEAD && git rev-parse --short HEAD
main
2f4e82f

# Run 1: the call from the issue
$ .venv/bin/python -c "from rag.retriever.keyword_search import KeywordSearcher; KeywordSearcher().index([])"; echo "exit $?"
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "/Users/tobibamisaye/ai301-unit3/pathreview-ai301-fa26-s3/rag/retriever/keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/Users/tobibamisaye/ai301-unit3/pathreview-ai301-fa26-s3/.venv/lib/python3.11/site-packages/rank_bm25.py", line 83, in __init__
    super().__init__(corpus, tokenizer)
  File "/Users/tobibamisaye/ai301-unit3/pathreview-ai301-fa26-s3/.venv/lib/python3.11/site-packages/rank_bm25.py", line 27, in __init__
    nd = self._initialize(corpus)
         ^^^^^^^^^^^^^^^^^^^^^^^^
  File "/Users/tobibamisaye/ai301-unit3/pathreview-ai301-fa26-s3/.venv/lib/python3.11/site-packages/rank_bm25.py", line 52, in _initialize
    self.avgdl = num_doc / self.corpus_size
                 ~~~~~~~~^~~~~~~~~~~~~~~~~~
ZeroDivisionError: division by zero
exit 1

# Run 2: control with one document
$ .venv/bin/python -c "...index one chunk, then search python..."
2026-10-06 04:10:29 [info     ] keyword_index_built            chunk_count=1
2026-10-06 04:10:29 [info     ] keyword_search_complete        query_len=1 results_count=1
[{'id': 1, 'text': 'python programming', 'bm25_score': -0.2746530721670274}]

# Run 3: the covering test, with any xfail marker ignored
$ .venv/bin/pytest tests/unit/test_keyword_search.py -k test_empty_index --runxfail
E       ZeroDivisionError: division by zero

.venv/lib/python3.11/site-packages/rank_bm25.py:52: ZeroDivisionError
=========================== short test summary info ============================
FAILED tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index
======================= 1 failed, 16 deselected in 0.17s =======================

# Whole file
$ .venv/bin/pytest tests/unit/test_keyword_search.py -q
........x........                                                        [100%]
16 passed, 1 xfailed in 0.17s
```

After (on `fix/68-empty-keyword-index`, same environment):

```
$ git rev-parse --abbrev-ref HEAD && git rev-parse --short HEAD
fix/68-empty-keyword-index
19c7db8

# Run 1: the call from the issue
$ .venv/bin/python -c "from rag.retriever.keyword_search import KeywordSearcher; KeywordSearcher().index([])"; echo "exit $?"
2026-10-06 04:10:32 [info     ] keyword_index_built            chunk_count=0
exit 0

# Run 2: control with one document
$ .venv/bin/python -c "...index one chunk, then search python..."
2026-10-06 04:10:32 [info     ] keyword_index_built            chunk_count=1
2026-10-06 04:10:32 [info     ] keyword_search_complete        query_len=1 results_count=1
[{'id': 1, 'text': 'python programming', 'bm25_score': -0.2746530721670274}]

# Run 3: the covering test, with any xfail marker ignored
$ .venv/bin/pytest tests/unit/test_keyword_search.py -k test_empty_index --runxfail
configfile: pyproject.toml
collected 18 items / 17 deselected / 1 selected

tests/unit/test_keyword_search.py .                                      [100%]

======================= 1 passed, 17 deselected in 0.13s =======================

# Whole file
$ .venv/bin/pytest tests/unit/test_keyword_search.py -q
..................                                                       [100%]
18 passed in 0.15s
```

## Eval iterations

**Run history**

1. Full run 1: 19/20 (categories: clear-accept 6/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4). The one miss was pkg-14, failed on `executable`.
2. Partial run (`--only pkg-14,pkg-10,pkg-17,pkg-18`) after loosening `executable`: 4/4. pkg-14 flipped to accept; the three unbuildable canaries stayed reject.
3. Full run 2 (the committed `eval-run.txt`): 20/20 (categories: clear-accept 7/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4).

**Package analysis**

pkg-14 (zellij-org/zellij#5174, clear-accept). Gold label: accept. My rubric's first version rejected it on `executable`, and the final version accepts it.

The plan fixes OSC color-query responses leaking into a pane on reattach. It names the reattach path in `zellij-server`'s client connection handling and commits to one change: drain pending OSC responses before pane input is wired. But it also says "exact functions to be pinned in the PR after tracing the query issuance with debug logs". My first `executable` check read any unpinned location as a deferred decision, the same as pkg-18's "recover() somewhere", so it failed the package.

That was the wrong line to draw. In pkg-18 and pkg-17 the open question is WHAT to do or WHICH layer to change. In pkg-14 the what and the layer are both decided, and only the exact function is left to find, which a stranger could do by reading that one module. The test plan (5 consecutive SSH reattach cycles with no rgb strings) and the stated Windows deferral were already strong, so `executable` was the only thing holding it.

**Check rationale**

| executable | The plan's files/areas, approach, and order of work | Pass if a stranger could start work today without asking the author anything: the plan names where the change goes (a file, function, or code site the package identifies) AND commits to one concrete change there. Fail if the key decisions are deferred to build time: no chosen approach ("investigate", "profile and optimize", "look into"), the location left open ("somewhere", "gocui? tcell?"), or alternatives left unchosen ("upstream or vendored, whichever is easier"). An unknown that is named and bounded (e.g. "the fix site may move one layer") does not fail this check, and neither does naming the module or code path (e.g. "the reattach path in zellij-server") with one committed change while leaving the exact function to be pinned during the build. What fails is an open choice of WHAT to do or WHICH layer/approach, not an unpinned line number. | required |

It reads this way because of pkg-14. The first version ended at the sentence about a "named and bounded" unknown, so a plan that named a module but not a function looked the same as one that said "somewhere". I added the last two sentences to separate an undecided approach (pkg-17's "gocui? tcell? not sure", pkg-18's "whichever is easier") from an unpinned line number. I kept the quoted phrases from the unbuildable packages inside the check on purpose, so the grader has concrete examples of what still fails and doesn't read the loosening as "any named area passes".

**Trade-offs**

Loosening `executable` risked flipping the unbuildable category, so I re-ran pkg-10, pkg-17 and pkg-18 as canaries with `--only` alongside pkg-14. All three stayed reject: pkg-10 names no files and no chosen approach, pkg-17 leaves the layer open, and pkg-18 leaves both the location and the approach open. The confirming full run then held every category at full marks.

What the check gives up: a plan that names a module and a vague change ("improve error handling in the sync module") could now pass `executable` if the grader reads "improve error handling" as one committed change. I accept that risk because `decisive-test` and `grounded-cause` still have to pass, and a plan that vague usually also has a vague test plan, which is how pkg-10 and pkg-17 fail twice.
