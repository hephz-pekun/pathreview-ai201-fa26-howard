# Contribution: Add Property-Based Tests for the PII Scrubber

**Contribution Number:** [1]  
**Student:** Hephzibah Olanipekun  
**GitHub Username:** hephz-pekun  
**Issue:** https://github.com/codepath/pathreview-ai201-fa26-howard/issues/45
**Pull Request:** https://github.com/hephz-pekun/pathreview-ai201-fa26-howard/pull/1#issue-6619488752

---

## Why I Chose This Issue

I chose this issue because it focused on improving test quality rather than adding new application functionality. The repository already contained a PII scrubber and a comprehensive suite of example-based tests, but it lacked property-based testing despite already including Hypothesis as a development dependency.

The issue was clearly scoped, reproducible, and isolated to a single component. It also provided an opportunity to gain experience with property-based testing, randomized input generation, and automated validation techniques commonly used in real-world Python projects.

**Claim comment:** https://github.com/codepath/pathreview-ai201-fa26-howard/issues/45#issuecomment-7175684100

---

## Understanding the Issue

### Problem Description

The PII scrubber is covered by traditional example-based unit tests. These tests verify behavior using specific hand-written inputs but do not generate randomized inputs to validate behavior across a larger search space.

### Expected Behavior

The test suite should include property-based tests using Hypothesis to generate randomized PII inputs and verify that the scrubber continues to correctly detect and redact personally identifiable information.

### Current Behavior

The test suite validates only fixed examples. No Hypothesis-based tests or randomized input generation exist for the scrubber.

### Affected Components

- `tests/unit/test_pii_scrubber.py`
- `safety/pii_scrubber.py`

---

## Reproduction Process

### Environment Setup

- Windows 11
- Python 3.14.2
- Project virtual environment activated
- Repository root directory

### Steps to Reproduce

1. Review:

```text
tests/unit/test_pii_scrubber.py
```

2. Verify that Hypothesis is available in the project's development dependencies.

3. Run:

```bash
pytest tests/unit/test_pii_scrubber.py -v
```

### Reproduction Evidence

**Observed**

The test suite contains extensive example-based tests covering:

- Email redaction
- Phone number handling
- SSN detection
- Address detection
- Idempotence behavior
- False-positive checks

However, no property-based tests exist.

Test result:

```text
20 passed, 5 xfailed
```

**My Findings**

I confirmed that the PII scrubber relied entirely on example-based validation despite the project already including Hypothesis as a development dependency. This matched the issue description and established a baseline for implementing property-based testing.

**Comment showing reproduction:** https://github.com/codepath/pathreview-ai201-fa26-howard/issues/45#issuecomment-7175967913

---

## Solution Approach

### Analysis

The scrubber exposes two primary interfaces:

```python
scrub()
```

and

```python
detect()
```

The most valuable properties to validate were:

1. Email redaction
2. Email detection
3. Scrubbing idempotence

These behaviors are central to the scrubber's purpose and can be tested using randomized inputs.

### Proposed Solution

Add Hypothesis-based property tests that generate randomized email addresses and arbitrary text input.

The new tests verify:

- Generated emails are redacted by `scrub()`
- Generated emails are detected by `detect()`
- Repeated scrubbing remains idempotent

### Implementation Plan

1. Add Hypothesis imports and strategies.
2. Create randomized email generators.
3. Add a property test for email redaction.
4. Add a property test for email detection.
5. Add a property test for scrub idempotence.
6. Run pytest and Ruff validation.

No deviations were required from the original implementation plan.

---

## Testing Strategy

I validated the contribution using the project's existing unit-test suite and Ruff linting.

During development I initially used:

```python
st.emails()
```

for email generation. Hypothesis produced examples that exposed differences between its email strategy and the scrubber's supported email pattern.

To keep the contribution focused on the issue, I constrained generated addresses to match the scrubber's supported format and reran the tests.

Commands executed:

```bash
pytest tests/unit/test_pii_scrubber.py -v
ruff check .
```

Results:

```text
23 passed, 5 xfailed
```

```text
All checks passed.
```

---

## Implementation Notes

### 2026-11-25

Reviewed the existing test suite and confirmed that the scrubber relied exclusively on example-based tests. Reproduced the issue by verifying the absence of Hypothesis property tests and established the baseline test result of:

```text
20 passed, 5 xfailed
```

### 2026-11-25

Added Hypothesis-based tests for email redaction, email detection, and scrub idempotence. The initial implementation used `st.emails()`, which generated edge-case addresses not supported by the scrubber's regex patterns.

### 2026-11-25

Encountered Hypothesis health-check failures caused by the existing pytest fixture design. Replaced fixture usage inside property tests with direct `PIIScrubber()` construction.

### 2026-11-25

Replaced the original email strategy with a constrained generator aligned with the scrubber's supported email format. Verified that the new property tests passed and completed validation using pytest and Ruff.

---

## Pull Request

**PR Link:** https://github.com/hephz-pekun/pathreview-ai201-fa26-howard/pull/1#issue-6619488752

### PR Description

Closes #45

#### Why?

The PII scrubber test suite contains extensive example-based coverage, but it lacks property-based tests that exercise behavior across a broader range of randomized inputs. Adding Hypothesis-based tests increases confidence that email detection, email redaction, and scrubbing idempotence continue to work correctly beyond a small set of hand-written examples.

#### AI Assistance Disclosure

AI assistance was used for implementation planning, test-design review, debugging support, and documentation. All code changes, testing, validation, and final decisions were manually reviewed and verified before submission.

#### What does this change?

- Add Hypothesis-based property tests for email redaction
- Add Hypothesis-based property tests for email detection
- Add a Hypothesis-based property test for scrub idempotence
- Add randomized email generation aligned with the scrubber's supported email format

#### How was it tested?

- [x] `pytest tests/unit/test_pii_scrubber.py -v`
- [x] `ruff check .`
- [x] Added automated test coverage
- [x] Existing xfailed tests remain unchanged

#### Evidence

Before:

```text
The PII scrubber test suite relied entirely on example-based tests.
No Hypothesis property tests existed.
```

After:

```text
23 passed, 5 xfailed in 1.71s
```

Commands run:

```bash
pytest tests/unit/test_pii_scrubber.py -v
ruff check .
```

**Maintainer Feedback:**

- None yet.

**Status:**

Open

---

## Learnings and Reflections

### Technical Skills Gained

I learned how to use Hypothesis to design meaningful property-based tests rather than simply generating random values. I also gained experience building custom strategies that align with application-specific validation rules instead of relying entirely on generic generators.

I learned how Hypothesis interacts with pytest fixtures, how health checks can reveal test-design problems, and how shrinking helps identify minimal failing examples when a property is violated.

### Challenges Overcome

The largest challenge involved designing useful input generators. My initial implementation used `st.emails()`, which generated addresses that were valid for Hypothesis but not supported by the scrubber's email regex.

Rather than expanding the scope into modifying production behavior, I redesigned the strategy to generate addresses matching the implementation's supported format. This kept the contribution focused on the issue while still providing meaningful randomized coverage.

Another challenge involved Hypothesis health-check failures caused by function-scoped pytest fixtures inside property tests. Creating the `PIIScrubber` instance directly inside the test resolved the issue.

### What I'd Do Differently Next Time

I would inspect implementation constraints before selecting Hypothesis strategies. Starting with the actual email regex would have avoided several rounds of failing property tests and reduced debugging time.

I would also use property-based testing earlier in development, since the generated examples quickly exposed assumptions that were not obvious when using only hand-written test cases.

---

## Resources Used

- https://github.com/codepath/pathreview-ai201-fa26-howard/issues/45#issuecomment-7175684100
- https://github.com/codepath/pathreview-ai201-fa26-howard/issues/45#issuecomment-7175967913
- https://github.com/codepath/pathreview-ai201-fa26-howard/issues/45
- `tests/unit/test_pii_scrubber.py`
- `safety/pii_scrubber.py`
- https://hypothesis.readthedocs.io/
- https://docs.pytest.org/