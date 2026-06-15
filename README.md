# Contribution [#]: [Issue Title]

**Contribution Number:** [1]  
**Student:** [Krishna Sai Sevilimedu Veeravalli]  
**Issue:** [https://github.com/documentdb/functional-tests/issues/202]
**Status:** [Phase I Complete]

---

## Why I Chose This Issue

[1-2 paragraphs explaining why this issue interests you, how it matches your skills/learning goals, what you hope to learn]
I chose this issue beacaise it is a well-scoped, test‑driven bug. The $bitAnd operator is narrow enough to be approachable but meaningful enough to matter. bitwise expressions often appear in data transformation pipelines where silent incorrectness is hard to catch without explicit coverage. I can reproduce it deterministically, validate the fix with the same test, know I am improving DocumentDB validation tests and improve the test suite.

---

## Understanding the Issue

### Problem Description

[In your own words, what's broken or missing?]

### Expected Behavior

[What should happen?]

### Current Behavior

[What actually happens?]

### Affected Components

[Which parts of the codebase are involved?]

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]
Environment Setup:
git clone https://github.com/documentdb/functional-tests.git
cd functional-tests
python -m venv .venv
pip install -r requirements.txt

export CONNECTION_STRING="mongodb://localhost:27017"

pytest -m bitwise --connection-string $CONNECTION_STRING --engine-name documentdb

Steps to Reproduce:

pytest documentdb_tests/compatibility/tests/core/operator/expressions/bitwise/bitAnd/test_smoke_expression_bitAnd.py 

Test Error observed in Terminal:
======================================= test session starts ========================================
platform darwin -- Python 3.14.4, pytest-9.1.0, pluggy-1.6.0 -- /Users/kv/Desktop/Workspace/Codepath/functional-tests/.venv/bin/python3.14
cachedir: .pytest_cache
metadata: {'Python': '3.14.4', 'Platform': 'macOS-26.4.1-arm64-arm-64bit-Mach-O', 'Packages': {'pytest': '9.1.0', 'pluggy': '1.6.0'}, 'Plugins': {'xdist': '3.8.0', 'json-report': '1.5.0', 'timeout': '2.4.0', 'metadata': '3.1.1'}}
rootdir: /Users/kv/Desktop/Workspace/Codepath/functional-tests/documentdb_tests
configfile: pytest.ini
plugins: xdist-3.8.0, json-report-1.5.0, timeout-2.4.0, metadata-3.1.1
timeout: 300.0s
timeout method: signal
timeout func_only: False
collected 1 item                                                                                   

documentdb_tests/compatibility/tests/core/operator/expressions/bitwise/bitAnd/test_smoke_expression_bitAnd.py::test_smoke_expression_bitAnd FAILED [100%]

============================================= FAILURES =============================================
___________________________________ test_smoke_expression_bitAnd ___________________________________
documentdb_tests/compatibility/tests/core/operator/expressions/bitwise/bitAnd/test_smoke_expression_bitAnd.py:29: in test_smoke_expression_bitAnd
    assertSuccess(result, expected, msg="Should support $bitAnd expression")
documentdb_tests/framework/assertions.py:145: in assertSuccess
    raise AssertionError(_format_exception_error(result))
E   AssertionError: [UNEXPECTED_ERROR] Expected success but got exception:
E   {'code': 31325, 'msg': 'Invalid $project :: caused by :: Unknown expression $bitAnd'}
===================================== short test summary info ======================================
FAILED documentdb_tests/compatibility/tests/core/operator/expressions/bitwise/bitAnd/test_smoke_expression_bitAnd.py::test_smoke_expression_bitAnd - AssertionError: [UNEXPECTED_ERROR] Expected success but got exception:
{'code': 31325, 'msg': 'Invalid $project :: caused by :: Unknown expression $bitAnd'}
======================================== 1 failed in 0.30s =========================================
Branch Link:
https://github.com/krishnasai453/functional-tests/tree/fix-issue-202-add_bitAnd_compatibility_test
---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

The repository only has a minimal smoke test for $bitAnd. It checks the operator in a single $project pipeline, so we don’t know whether $bitAnd works correctly in other stages ($set, nested fields, multiple operands, error cases) or on all supported engines.

**Match:** [What similar patterns/solutions exist in the codebase?]

Look for other bitwise operator tests that are more complete. The $bitOr tests (test_smoke_expression_bitOr.py) show the same simple pattern, but we also have richer examples for other operators (e.g., $add, $set tests) that include multiple operands, nested fields, and parametrization across engines.
**Plan:** [Step-by-step implementation plan]

1. Create test_bitAnd_compatibility.py under .../bitwise/bitAnd/ with a set of parametrized tests:
  • $project with simple operands (already covered, keep as reference).
  • $set with nested field, multi‑operand, and error cases.
  • $project with nested field.
  • Tests that deliberately feed non‑integer values and expect a failure.
2. Update test_set_expressions.py to add the nested‑field and multi‑operand $bitAnd cases.
3. Add/extend a fixture in conftest.py (if needed) to parametrize these new tests over each engine (documentdb, mongodb).
4. Edit pytest.ini to add a bitwise (or bitAnd) marker so CI can run them selectively.
5. Run the test suite locally (pytest -m bitwise) and fix any failing assertions.
6. Commit with a concise, present‑tense message like Add comprehensive $bitAnd compatibility tests.




**Implement:** [Link to your branch/commits as you work]

https://github.com/krishnasai453/functional-tests/tree/fix-issue-202-add_bitAnd_compatibility_test

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

 Follow the CONTRIBUTING.md:
  - Run pre‑commit hooks (black, isort, flake8, mypy).
  - Ensure docstrings and clear test names.
  - Use required tags (@pytest.mark.smoke, @pytest.mark.bitwise).
• Commit message style: present tense, concise, reference the issue (Fix #202: Add $bitAnd compatibility tests).
• Pull‑request template expects a description of what, why, and how to test – fill those in.

**Evaluate:** [How will you verify it works?]

Automated verification will be the same as any other functional test:
  - pytest -m bitwise should pass on both DocumentDB and MongoDB engines.
  - The CI pipeline will run the same command; no failures mean the fix works.
  - Optionally, check coverage reports to see that $bitAnd lines are now exercised.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
