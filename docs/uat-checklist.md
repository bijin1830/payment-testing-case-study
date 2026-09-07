# Payment UAT Checklist

A compact checklist for validating a POS/payment release before UAT sign-off or controlled production rollout.

## Environment and configuration

- [ ] Correct application version installed
- [ ] Correct terminal ID / merchant configuration
- [ ] Required AIDs / card parameters available
- [ ] Host connectivity verified
- [ ] ECR/integration mode configured correctly
- [ ] Date/time and network settings validated
- [ ] Receipt configuration checked

## Core financial transactions

- [ ] Contact purchase
- [ ] Contactless purchase
- [ ] Swipe/fallback scenario where supported
- [ ] Approved transaction
- [ ] Declined transaction
- [ ] Void
- [ ] Full refund
- [ ] Partial refund where supported
- [ ] Reversal / communication-recovery scenario
- [ ] Last-transaction/retry behavior where supported

## CVM and card behavior

- [ ] Online PIN
- [ ] Incorrect PIN
- [ ] PIN cancel
- [ ] No-CVM/contactless low-value scenario where applicable
- [ ] Card removal / cancel handling
- [ ] Unsupported card/application behavior

## ECR / integration

- [ ] ECR request reaches POS once
- [ ] Amount/reference mapping is correct
- [ ] Approved response returns to ECR
- [ ] Decline response returns to ECR
- [ ] Timeout behavior is safe
- [ ] Duplicate transaction is prevented
- [ ] POS/ECR references can be correlated in logs

## Settlement and reporting

- [ ] Transaction appears in terminal/TMS report
- [ ] Approved and declined statuses are correct
- [ ] Void/refund reflected correctly
- [ ] Batch totals match transaction history
- [ ] Settlement succeeds
- [ ] No-batch condition handled correctly
- [ ] New batch starts as expected after settlement

## Negative / resilience checks

- [ ] Network interruption
- [ ] Invalid amount / input
- [ ] Customer cancel
- [ ] Host timeout
- [ ] Device disconnect where applicable
- [ ] Printer unavailable where receipt is required
- [ ] Application restart/recovery

## Evidence for sign-off

For each important case, capture:

- Test-case ID
- Date/time
- Application version
- Terminal/test environment
- Amount
- Transaction reference
- Expected result
- Actual result
- Pass/Fail
- Screenshot/log/reference where appropriate

## Sign-off principle

UAT sign-off should be based on the agreed scope, resolved critical/high defects, known limitations and evidence that the main business flows work consistently. A successful single transaction is not enough to establish release readiness.