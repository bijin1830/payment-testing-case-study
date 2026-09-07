# Sample Payment Defect Reports

These are synthetic examples showing how I structure payment defects so development/support teams can reproduce and investigate them quickly.

## DEF-001 — ECR receives timeout although POS transaction is approved

**Area:** POS / ECR Integration  
**Severity:** High  
**Priority:** High

**Preconditions:**
- ECR is connected to POS.
- Purchase interface is available.

**Steps to reproduce:**
1. Send a purchase request from ECR.
2. Complete the card transaction on POS.
3. Observe the POS result and ECR result.

**Expected result:**
ECR should receive the final approved response with the transaction reference.

**Actual result:**
POS displays Approved, but ECR receives a timeout/failure and does not receive the final response.

**Business risk:**
Operator may retry the transaction and create a duplicate financial attempt.

**Evidence to collect:**
- Transaction timestamp
- Amount
- POS/ECR reference
- POS application logs
- ECR logs
- Host response code
- STAN/RRN when available

**Investigation direction:**
Confirm whether the host response reached POS and whether the failure occurred while returning the result to ECR.

---

## DEF-002 — Incorrect message shown for issuer decline

**Area:** POS UI / Host Response Mapping  
**Severity:** Medium

**Expected result:**
Terminal should display the mapped decline reason or a correct generic decline message.

**Actual result:**
Terminal displays a communication failure even though a valid decline response was received from the host.

**Why this matters:**
Incorrect error mapping makes support investigation harder and may cause unnecessary network troubleshooting.

---

## DEF-003 — Settlement total does not match approved batch transactions

**Area:** Settlement / Reconciliation  
**Severity:** Critical or High depending on impact

**Steps:**
1. Perform several approved purchases.
2. Perform one void/refund where permitted.
3. Generate local totals.
4. Perform settlement.
5. Compare settlement totals with transaction history.

**Expected result:**
Settlement totals should match the eligible batch transactions according to the configured business rules.

**Actual result:**
Local/host totals differ.

**Evidence:**
- Batch number
- Transaction count
- Debit/credit totals
- Settlement request/response
- Transaction report
- Database records when available

---

## DEF-004 — Contactless transaction incorrectly requests fallback

**Area:** EMV / Contactless  
**Severity:** High

**Expected result:**
For the configured test condition, terminal should continue contactless processing or display the correct decline/result.

**Actual result:**
Terminal displays a fallback/interface-switch instruction unexpectedly.

**Evidence:**
- Card/interface type
- Amount
- AID/application selected
- Relevant EMV tags/results
- Terminal parameters
- Application version

## Defect-reporting principles

A useful payment defect should answer five questions:

1. **What was attempted?**
2. **What did the user see?**
3. **What did the host/card actually return?**
4. **At which layer is the mismatch likely occurring?**
5. **What evidence allows another person to reproduce it?**
