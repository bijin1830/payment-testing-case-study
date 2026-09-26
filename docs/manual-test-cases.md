# Manual Payment Test Scenarios

These are generic, synthetic examples of the types of scenarios I consider while manually testing payment applications.

## Purchase

### TC-PAY-001 — Successful contact purchase
**Preconditions:** Terminal is logged on, card is supported, network is available.

**Steps:**
1. Insert the test card.
2. Enter a valid amount.
3. Complete the requested CVM/PIN flow.
4. Wait for the final response.

**Expected result:**
- Transaction completes successfully.
- Terminal shows Approved.
- Receipt contains the expected amount and masked card information.
- A unique transaction reference is generated.
- Host/database status is successful.

### TC-PAY-002 — Issuer decline
**Steps:** Perform a purchase using a test condition configured to return a decline.

**Expected result:**
- Terminal displays a decline, not a technical error.
- No false success is shown to the ECR/customer.
- Decline response/reference is logged correctly.
- Transaction is not included as an approved sale in settlement totals.

### TC-PAY-003 — Communication loss after request
**Steps:** Start a purchase and simulate communication loss after the request is sent.

**Expected result:**
- Terminal/ECR handles the timeout safely.
- User is not encouraged to blindly repeat the payment.
- Last-transaction/retry/reversal logic works according to the implementation.
- Duplicate financial posting is prevented.

### TC-PAY-016 — Duplicate purchase retry
**Scenario:** The same transaction request is submitted again after an uncertain result.

**Expected result:**
- The system follows its duplicate/idempotency rules.
- A second unintended financial posting is not created.
- The original transaction remains traceable.

### TC-PAY-017 — Invalid amount
**Examples:** zero amount, negative amount, malformed decimal input, amount above configured limit.

**Expected result:**
- Invalid amount is rejected at the correct layer.
- No financial request is sent when local validation should block it.
- Operator receives a clear error.

## Void

### TC-PAY-004 — Void approved transaction
**Preconditions:** An approved purchase exists and is eligible for void.

**Expected result:**
- Correct original transaction is located.
- Void amount matches allowed rules.
- Host approves the void.
- Original transaction and void are linked.
- Batch totals reflect the cancellation correctly.

## Refund

### TC-PAY-005 — Successful full refund
**Expected result:**
- Refund is processed using permitted business rules.
- Correct amount is sent to the host.
- Refund receipt/result is displayed correctly.
- Reporting and settlement reflect the refund separately from a sale.

### TC-PAY-006 — Invalid refund reference
**Expected result:**
- Refund is rejected safely.
- No financial request is sent when validation should fail locally.
- Clear error is shown to the operator.

### TC-PAY-018 — Refund exceeds original amount
**Expected result:**
- Request is rejected according to business rules.
- Original transaction remains unchanged.
- No incorrect credit is created.

## Contactless

### TC-PAY-007 — Contactless purchase
**Expected result:**
- Contactless application is selected correctly.
- Terminal follows configured CVM/interface rules.
- Approved/declined result matches the host/card decision.
- No unnecessary fallback is triggered.

## Fallback

### TC-PAY-008 — Chip failure followed by allowed fallback
**Expected result:**
- Fallback is offered only when permitted.
- Interface transition is clear.
- Final transaction records identify the actual entry method.
- Host response is handled normally.

## Reversal

### TC-PAY-009 — Automatic reversal after uncertain transaction state
**Expected result:**
- Terminal follows configured recovery logic.
- Reversal contains the correct original reference information.
- Final state can be reconciled without double debit.

### TC-PAY-019 — Duplicate reversal prevention
**Expected result:**
- Repeated recovery attempts do not create uncontrolled duplicate reversals.
- Final status is deterministic and auditable.

## Settlement

### TC-PAY-010 — Successful batch settlement
**Expected result:**
- Terminal sends correct batch totals.
- Host acknowledges settlement.
- Local batch closes only after the expected success flow.
- New transactions start under the next batch as designed.

### TC-PAY-011 — No batch available
**Expected result:**
- Empty batch is handled gracefully.
- No misleading financial state is created.

### TC-PAY-020 — Settlement host timeout
**Expected result:**
- Batch is not cleared prematurely.
- Operator can safely retry according to implementation rules.
- Final batch status can be reconciled.

## ECR Integration

### TC-PAY-012 — ECR purchase success
**Expected result:**
- Request fields are mapped correctly.
- POS processes the transaction once.
- Final response is returned to the ECR.
- ECR and POS show the same financial outcome.

### TC-PAY-013 — POS succeeds but ECR misses final response
**Expected result:**
- Integration does not create a duplicate charge on retry.
- Recovery logic determines the original outcome.
- Logs show whether the host approved and where communication was lost.

### TC-PAY-021 — ECR sends malformed request
**Expected result:**
- Invalid request is rejected safely.
- POS does not start an incomplete financial transaction.
- Error response is deterministic and traceable.

## CVM

### TC-PAY-014 — Incorrect PIN
**Expected result:**
- Correct CVM result is handled.
- No false approval is stored.
- Retry/cancel behaviour follows configured rules.

## Resilience

### TC-PAY-015 — Customer cancels transaction
**Expected result:**
- Transaction stops safely.
- Consistent cancel result returns to all layers.

### TC-PAY-022 — POS restart during uncertain transaction
**Expected result:**
- On restart, the application can determine or recover the final state.
- No hidden duplicate transaction is created.
- Pending/reversal state is traceable.
