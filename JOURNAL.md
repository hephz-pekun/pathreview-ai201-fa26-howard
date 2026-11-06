## Unit 7 — Issue selection

**Issue link:** (https://github.com/codepath/pathreview-ai201-fa26-howard/issues/64)

**Issue title:** Relevance scorer “partial overlap” test fixture actually has full query overlap

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Selection notes (Issue checklist reasoning):**
This issue is a good fit because it is limited in scope and affects a specific test case rather than a large architectural component. The issue description provides clear reproduction steps and a likely root cause, making it straightforward to verify and fix. I already have experience reading Python test files and running pytest, so I can confidently reproduce the failure and validate the solution. The expected change is small and well-defined, which makes it appropriate for a Tier 1 contribution.

**Problem summary:**
The issue is in the relevance scorer test suite, specifically test_query_with_partial_overlap. The test is intended to verify behavior when a query only partially overlaps with a text chunk, but the current fixture contains all query terms. Because the scorer correctly identifies full keyword coverage, it returns a score of 1.0 and causes the test assertion to fail. A successful fix will update the test data so it represents true partial overlap and accurately validates the intended scoring behavior.

**Branch name:** fix/64-partial-overlap-test-fixture

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

## Unit 8 — Reproduction & solution planning

**Reproduction commit link:** (https://github.com/codepath/pathreview-ai201-fa26-howard/commit/8300055894fda78b1ee828a65a3ea5f33e854118)

**Reproduction summary:**

I ran:

pytest tests/unit/test_relevance_scorer.py -q

The test suite completed with 18 passing tests and 1 expected failure (xfail). Although the specific assertion described in the issue was not reproduced as an active failing test, inspection of the issue and test code indicates that the fixture used by `test_query_with_partial_overlap` may still not accurately represent partial keyword overlap. Further investigation will focus on the test fixture and its expected behavior.

**PLAN.md link:** [link to PLAN.md in your fork]

**Blockers or open questions:**
None at the moment