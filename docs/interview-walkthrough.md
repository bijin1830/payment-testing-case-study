# Payment QA Interview Walkthrough

This page gives a compact way to explain the project during an interview.

## 1. What is this project?

A synthetic manual QA portfolio that demonstrates how I test and investigate POS/payment flows across:

- POS
- ECR
- ISO 8583
- EMV
- API
- Basic SQL/data verification
- Settlement
- Reversal/recovery

## 2. Example issue I can explain

**Scenario:** Host approves a payment, but ECR receives a timeout.

My investigation approach:

1. Capture timestamp, amount and transaction reference.
2. Check the ECR request.
3. Confirm the POS sent the financial request.
4. Check the host response.
5. Match STAN/RRN or equivalent references.
6. Verify whether POS stored the approval.
7. Check why the final ECR response was not delivered.
8. Confirm whether reversal or inquiry is required.
9. Perform basic database/status checks where access is available and validate settlement state.
10. Prevent retry from causing a duplicate charge.

## 3. What makes payment testing different?

A visible terminal result is only one layer.

A tester must often correlate:

```text
Card decision
    ↓
EMV/kernel result
    ↓
POS application
    ↓
ECR integration
    ↓
Host / issuer response
    ↓
Database / TMS
    ↓
Settlement / reporting
```

## 4. Example ISO 8583 fields I check

- MTI
- DE3 processing code
- DE4 amount
- DE11 STAN
- DE22 entry mode
- DE37 RRN
- DE38 authorization code
- DE39 response code
- DE41 terminal ID
- DE42 merchant ID
- DE49 currency
- DE55 EMV data

Exact field usage depends on the host specification.

## 5. Example EMV evidence

Useful investigation tags can include:

- 9F26 Application Cryptogram
- 9F27 Cryptogram Information Data
- 9F34 CVM Results
- 9F36 ATC
- 95 TVR

I use them as supporting evidence together with the host and terminal result.

## 6. How I define a good payment defect

A good defect should make clear:

- What transaction was attempted
- What the user/ECR saw
- What the host/card actually returned
- Which layer appears inconsistent
- What evidence proves the issue
- What financial risk exists
