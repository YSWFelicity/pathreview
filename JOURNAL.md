# Development Journal

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/159

**Issue title:** structlog output is not captured by pytest caplog — log assertions fail suite-wide

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The application uses structlog, but the test configuration does not route its output through Python's standard logging system. As a result, pytest's `caplog` fixture cannot capture emitted log events, causing log assertions to fail even when the expected messages are printed to standard error. This affects shared test configuration in `tests/conftest.py` and multiple unit tests that rely on `caplog`. A successful fix will configure logging during tests so that `caplog` reliably captures structlog events without changing application behavior.

**Branch name:** fix/159-structlog-caplog

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [ ] Issue added to cohort ledger
