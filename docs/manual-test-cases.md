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

## Void

### TC-PAY-004 — Void approved transaction
**Preconditions:** An approved purchase exists and is eligible for void.

**Steps:** Select Void and enter/select the original transaction reference.

**Expected result:**
- Correct original transaction is located.
- Void amount matches allowed rules.
- Host approves the void.
- Original transaction and void are linked in reports/database.
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

## Contactless

### TC-PAY-007 — Contactless purchase
**Steps:** Tap a supported test card/device and complete any requested CVM.

**Expected result:**
- Contactless application is selected correctly.
- Terminal follows the configured CVM/interface rules.
- Approved/declined result matches the host/card decision.
- No unnecessary fallback is triggered.

## Fallback

### TC-PAY-008 — Chip failure followed by allowed fallback
**Steps:** Reproduce a supported chip-read failure condition and follow the terminal instruction.

**Expected result:**
- Fallback is offered only when permitted by configuration/rules.
- Interface transition is clear to the user.
- Final transaction records identify the actual entry method.
- Host response is handled normally.

## Reversal

### TC-PAY-009 — Automatic reversal after uncertain transaction state
**Scenario:** Host may have processed the transaction, but the terminal does not receive the final response.

**Expected result:**
- Terminal follows configured reversal/recovery logic.
- Reversal contains the correct original reference information.
- Final state can be reconciled without double debit.

## Settlement

### TC-PAY-010 — Successful batch settlement
**Expected result:**
- Terminal sends the correct batch totals.
- Host acknowledges settlement.
- Local batch closes only after the expected success flow.
- New transactions start under the next batch as designed.

### TC-PAY-011 — No batch available
**Expected result:**
- Terminal handles an empty batch gracefully.
- No misleading settlement success/failure state is generated.

## ECR Integration

### TC-PAY-012 — ECR purchase success
**Steps:** Send a purchase from the ECR to the POS using a synthetic amount/reference.

**Expected result:**
- Request fields are mapped correctly.
- POS processes the transaction once.
- Final response is returned to the ECR.
- ECR and POS show the same financial outcome and reference.

### TC-PAY-013 — POS succeeds but ECR misses final response
**Expected result:**
- Integration does not create a duplicate charge on retry.
- Recovery/last-transaction logic can determine the original outcome.
- Logs clearly show whether the host approved and where communication was lost.
