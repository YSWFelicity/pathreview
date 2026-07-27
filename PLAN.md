# Issue #159 Solution Plan

## Problem

The application emits warnings through structlog, but pytest's `caplog` fixture does not capture them as standard logging records. The focused unit test confirms that the warning appears under captured stdout while both `caplog.text` and `caplog.records` remain empty.

## Reproduction

Run:

```bash
.venv/bin/pytest tests/unit/test_batch_processor.py::TestBatchEmbeddingProcessor::test_empty_chunks_list_returns_empty -q
```

The test fails at `tests/unit/test_batch_processor.py:42` even though `BatchEmbeddingProcessor.process([])` returns `[]` and prints the expected warning.

## Proposed approach

1. Add test-only structlog configuration in `tests/conftest.py` that routes events through Python's standard logging system.
2. Preserve pytest logging capture and isolate or reset global structlog state so the configuration does not leak between tests.
3. Rerun the focused failing test to confirm the warning appears in `caplog`.
4. Run the related batch processor tests, then the full unit-test suite, to detect regressions.

## Expected files

- `tests/conftest.py`
- Tests only if additional coverage is needed to verify configuration isolation

## Risks

Structlog configuration is global and loggers may be cached. The main risk is changing logging behavior for unrelated tests or allowing test configuration to persist beyond its intended scope.

## Definition of done

- The focused `caplog` assertion passes.
- Existing application behavior is unchanged.
- Related tests and the full unit-test suite pass without new logging-related failures.
