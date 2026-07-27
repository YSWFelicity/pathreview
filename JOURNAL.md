# Development Journal

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/159

**Issue title:** structlog output is not captured by pytest caplog — log assertions fail suite-wide

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The application uses structlog, but the test configuration does not route its output through Python's standard logging system. As a result, pytest's `caplog` fixture cannot capture emitted log events, causing log assertions to fail even when the expected messages are printed to standard error. This affects shared test configuration in `tests/conftest.py` and multiple unit tests that rely on `caplog`. A successful fix will configure logging during tests so that `caplog` reliably captures structlog events without changing application behavior.

### Selection notes — "Is this right for me?"

- **Understanding:** I can explain the failure and the expected result: structlog currently prints the warning, but pytest does not receive it as a standard logging record; after the fix, `caplog` should capture the same event and the assertion should pass.
- **Tier fit:** This is a realistic Tier 1 issue for my first contribution because the change should be localized to one or two test-related files and does not require modifying the application's business logic.
- **Codebase readiness:** I located and read the shared fixtures in `tests/conftest.py`, the logging setup in `core/logging.py`, the `BatchEmbeddingProcessor` code that emits the warning, and the complete `test_empty_chunks_list_returns_empty` test in `tests/unit/test_batch_processor.py`.
- **Scope and validation:** My rough plan is to add a test-only structlog configuration, reproduce the current failure with the single test named in the issue, and then run the related unit tests to check for regressions. This is a bounded change that I expect to complete within the Tier 1 estimate of 3–6 focused hours.
- **Claims and blockers:** I checked the issue and the cohort ledger, recorded my claim, and found no listed blockers or dependencies. I am comfortable proceeding with the current number of claims.

**Branch name:** fix/159-structlog-caplog

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** Pending — add the GitHub commit URL after the reproduction commit is created and pushed.

**Reproduction summary:**
I reproduced the issue by running `.venv/bin/pytest tests/unit/test_batch_processor.py::TestBatchEmbeddingProcessor::test_empty_chunks_list_returns_empty -q`. The processor returned the expected empty list and printed the warning, but pytest captured it as stdout while `caplog.text` and `caplog.records` remained empty, causing the assertion at `tests/unit/test_batch_processor.py:42` to fail.

**PLAN.md link:** https://github.com/YSWFelicity/pathreview/blob/fix/159-structlog-caplog/PLAN.md

**Walkthrough video (recommended):** Not recorded yet.

**Blockers or open questions:**
I still need to confirm whether a test-only `structlog.stdlib` configuration or structlog's test capture utilities provide the best integration with the existing `caplog` assertions while avoiding global logging state leaking between tests.
