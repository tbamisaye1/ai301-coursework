# Plan: issue #68, `KeywordSearcher.index([])` raises `ZeroDivisionError`

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68
Repro: my Unit 2 report on the issue,
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68#issuecomment-5872690654

## Diagnosis

My Run 1 traceback puts the division inside rank-bm25, reached from
`index()`:

```
  File ".../rag/retriever/keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
  ...
  File ".../rank_bm25.py", line 52, in _initialize
    self.avgdl = num_doc / self.corpus_size
ZeroDivisionError: division by zero
```

`KeywordSearcher.index()` always builds `BM25Okapi(tokenized_corpus)`.
With `chunks == []` the corpus is empty, `corpus_size` is 0, and
rank-bm25 divides by it.

My controls rule out everything except the empty list:

- Run 2 (one chunk) indexes and searches normally:
  `[{'id': 1, 'text': 'python programming', 'bm25_score': -0.2746530721670274}]`
- `search()` already guards the empty case (`if not self.bm25 or not
  self.chunks: return []`), so the read path handles "nothing indexed";
  only the write path, `index()`, is missing the guard.
- Run 3 shows the covering test `test_empty_index` fails at
  `test_keyword_search.py:140` with the same error, so the strict xfail
  (manifest H-01) is failing for the reason the issue describes.

So the evidence points at the missing empty-corpus guard in `index()`:
the failure happens only with the empty list, and only on the path that
builds `BM25Okapi`. I have not tested whether rank-bm25 is meant to
accept an empty corpus, so I am fixing this on the Path Review side
rather than treating it as a library bug.

## Thread and repo context

- The issue was opened by Aburke225 (COLLABORATOR), whose only direction
  is in the issue body: guard `index()` like `search()` and "remove the
  marker as part of the fix". This plan does both. No maintainer comment
  in the thread proposes or rejects any other approach; the other
  comments are classmates' reproductions, plans, and PRs (#74, #83, #89).
- `docs/CONTRIBUTING.md` has no AI-use policy and no disclosure
  requirement. It asks for `<type>/<issue-number>-<description>` branch
  names, Conventional Commits, and removal of a seeded bug's strict xfail
  marker in the fixing PR.

## Scope

In scope, one change in one method: when `chunks` is empty,
`KeywordSearcher.index()` stores the empty list, sets `self.bm25 = None`,
logs the same `keyword_index_built` event with `chunk_count=0`, and
returns without constructing `BM25Okapi`. Setting `bm25` to `None`
(rather than just returning) also covers re-indexing a searcher that
already had documents, so no stale index survives.

Not in scope: `search()`, `_tokenize()`, BM25 scoring, `hybrid.py` or any
other retriever, and the rank-bm25 dependency.

## Files

- `rag/retriever/keyword_search.py`: the guard at the top of `index()`.
- `tests/unit/test_keyword_search.py`: remove the strict
  `@pytest.mark.xfail` marker (issue #68, manifest H-01) from
  `test_empty_index`, as CONTRIBUTING requires for seeded bugs, and add
  one test, `test_reindex_with_empty_clears_previous_index`, for the
  re-index case.

## Approach

1. Branch `fix/68-empty-keyword-index` from `main` on my fork.
2. Add the empty-corpus guard to `index()` before the tokenize-and-build
   lines; leave the non-empty path untouched.
3. Remove the xfail marker from `test_empty_index`; add the re-index
   test.
4. Run the test plan below, then `make check && make test-unit` before
   the pull request in Unit 4.

## Test plan

Re-run my three Unit 2 runs against the change:

1. Run 1, `.venv/bin/python -c "from rag.retriever.keyword_search import
   KeywordSearcher; KeywordSearcher().index([])"`: before, it raises
   `ZeroDivisionError: division by zero`; after, it exits 0 with no
   traceback (only the `keyword_index_built chunk_count=0` log line).
2. Run 2, the one-chunk control: the output must be unchanged, the same
   single result with `bm25_score` of about -0.2747.
3. Run 3, `pytest tests/unit/test_keyword_search.py -k test_empty_index`:
   before, `1 failed` with `ZeroDivisionError` (under `--runxfail`);
   after, with the marker removed, `1 passed`.
4. The whole file, `pytest tests/unit/test_keyword_search.py -q`: before,
   16 passing tests plus the xfailed `test_empty_index` (my Run 3 showed
   17 tests: `1 failed, 16 deselected`); after, `18 passed` (the 16, the
   un-marked `test_empty_index`, and the new re-index test).

## Risks and unknowns

- I checked who depends on `self.bm25`: the only caller outside the
  class is `rag/retriever/hybrid.py` line 66, which calls
  `keyword_searcher.search()`, and nothing outside `keyword_search.py`
  reads `self.bm25` directly. `search()` already treats `None` as an
  empty index, so setting it to `None` should not break a caller. I have
  not checked code outside `rag/` or the frontend, which do not import
  this class.
- I have only reproduced on Python 3.11 on macOS. The fix is plain
  Python with no platform-specific code, but I have not tested other
  versions.
- The "after" counts in the test plan are my prediction: my Run 3 showed
  17 tests in the file (`1 failed, 16 deselected`), and the change adds
  one, so I expect 18 passed. I will confirm with the real run.
- Several classmates have plans and pull requests on this issue (#74,
  #83, #89). This plan is built from my own reproduction and my own
  read of the code.

## Deviations

Nothing changed between the plan and the build. The guard went into
index() as planned, the xfail marker came off test_empty_index, and the
one re-index test was added. The after-run matched the test plan:
Run 1 exits without a traceback, the one-chunk control printed the same
result, and the whole file ended at 18 passed. The only detail settled
during the build was logging keyword_index_built with chunk_count=0 for
the empty case, so both paths log the same event.
