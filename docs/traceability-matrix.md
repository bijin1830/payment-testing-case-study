# Payment QA Traceability Matrix

This document shows how I connect a business requirement to test coverage, execution evidence and defects.

> All examples are synthetic and vendor-neutral.

| Requirement ID | Requirement | Test Case(s) | Evidence | Expected Result | Defect Link |
|---|---|---|---|---|---|
| REQ-PAY-001 | POS must process an approved purchase once | TC-PAY-001, TC-PAY-012 | POS log, ECR log, host response, transaction record | One approved financial transaction and one final response | If failed, create defect |
| REQ-PAY-002 | Issuer declines must not be shown as technical failures | TC-PAY-002 | Host response code, POS message, ECR result | Correct decline mapping | If mapping differs, create defect |
| REQ-PAY-003 | Unknown/timeout result must not cause duplicate debit | TC-PAY-003, TC-PAY-009, TC-PAY-013 | Request/response timestamps, reversal/inquiry evidence | Final state is safely resolved | Critical if duplicate risk exists |
| REQ-PAY-004 | Refund must reference an eligible transaction | TC-PAY-005, TC-PAY-006 | Original txn reference, refund request/response | Valid refund succeeds; invalid reference fails | Create defect if validation is bypassed |
| REQ-PAY-005 | Batch settlement totals must reconcile | TC-PAY-010, TC-PAY-011 | Local totals, host totals, batch response | Counts and amounts match agreed rules | Critical if financial mismatch exists |
| REQ-PAY-006 | ECR and POS must return the same final outcome | TC-PAY-012, TC-PAY-013 | ECR request/response, POS result, host outcome | Both layers show the same final financial state | High/Critical if inconsistent |

## Why traceability matters

A traceability matrix helps answer:

- What requirement is being tested?
- Which test case proves it?
- What evidence supports the result?
- Which defect blocks the requirement?
- Has the requirement been retested after the fix?

## Example execution record

| Field | Example |
|---|---|
| Test Case | TC-PAY-013 |
| Environment | UAT |
| Build | Synthetic v1.2.0 |
| Date/Time | 2026-09-26 14:30 |
| Amount | 25.00 |
| Interface | ECR → POS |
| Expected | Final approved result returned to ECR once |
| Actual | POS approved; ECR timed out |
| Result | Fail |
| Evidence | POS log + ECR log + host response reference |
| Defect | DEF-001 |
