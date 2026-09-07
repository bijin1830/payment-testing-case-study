# Payment Testing Case Study

![Manual QA](https://img.shields.io/badge/Focus-Manual%20QA-0A66C2)
![Payments](https://img.shields.io/badge/Domain-Payments%20%26%20POS-6F42C1)
![ISO 8583](https://img.shields.io/badge/ISO-8583-2EA44F)
![EMV](https://img.shields.io/badge/EMV-Testing-F59E0B)

A practical **manual payment testing portfolio project** covering POS transaction flows, ECR integration, ISO 8583 validation, EMV checks, refunds, voids, reversals, fallback and settlement scenarios.

This repository is intentionally focused on **manual testing and payments-domain analysis**, which reflects my strongest hands-on experience.

> **Privacy note:** All examples are synthetic and generic. No real bank, merchant, terminal, cardholder, production log, credential, key or confidential client information is included.

## What this project demonstrates

- Manual functional and regression testing
- POS and ECR transaction validation
- Purchase, void, refund and reversal scenarios
- Contact, contactless and swipe/fallback testing
- PIN/CVM and cardholder-verification scenarios
- ISO 8583 request/response validation
- EMV transaction-flow understanding
- Settlement and reconciliation checks
- Negative testing and decline validation
- Defect reporting and root-cause-oriented investigation
- UAT readiness and production-validation thinking

## Example transaction flow

```text
Customer/Card
     |
     v
POS / Payment Application
     |
     v
ECR / Integration Layer (when applicable)
     |
     v
Acquirer / Payment Host
     |
     v
Card Scheme / Issuer
     |
     v
Response back to terminal
```

As a manual tester, validation is not limited to whether the receipt says **Approved** or **Declined**. I also verify the request, response, transaction identifiers, card interface, terminal behavior, host result, database record and final customer-facing outcome.

## Core scenarios

| Area | Example checks |
|---|---|
| Purchase | Approved sale, issuer decline, timeout, duplicate prevention |
| Contact | Application selection, PIN/CVM, online result, card removal |
| Contactless | Tap flow, CVM limit behavior, interface switching |
| Fallback | Chip failure followed by permitted swipe flow |
| Void | Same-batch cancellation, reference matching, status update |
| Refund | Valid refund, invalid reference, partial/full amount rules |
| Reversal | Communication loss after host processing, duplicate protection |
| Settlement | Batch totals, no-batch condition, host acknowledgement |
| ECR | Request/response mapping, timeout, retry/last-transaction handling |
| Negative | Invalid amount, cancel, network loss, host decline, terminal errors |

## Repository contents

```text
payment-testing-case-study/
├── docs/
│   ├── manual-test-cases.md
│   ├── iso8583-validation.md
│   ├── emv-flow.md
│   ├── defect-examples.md
│   └── uat-checklist.md
├── test-cases/
│   └── payment-test-cases.csv
└── README.md
```

## How I approach a payment issue

1. Reproduce the scenario with controlled test data.
2. Capture transaction time, amount, interface and reference details.
3. Check terminal/ECR behavior and visible error.
4. Compare request and response messages.
5. Validate important ISO 8583 fields when available.
6. Review EMV result/cryptogram information for chip or contactless cases.
7. Confirm whether the host approved, declined or never responded.
8. Check whether a reversal or retry was generated.
9. Validate database/TMS records and settlement impact.
10. Raise a defect with clear evidence, expected result and actual result.

## Important note on standards

Exact ISO 8583 MTIs, field usage, EMV parameters and transaction behavior vary by acquirer, host, scheme, kernel and implementation. The examples in this repository are **illustrative QA examples**, not a specification for any particular bank or production system.

## Skills represented

`Manual Testing` · `Functional Testing` · `Regression Testing` · `UAT` · `Payments QA` · `POS` · `ECR` · `ISO 8583` · `EMV` · `API Testing` · `SQL Validation` · `Defect Analysis` · `Production Support`

## Author

**Bijin Benni**  
Software Test Engineer | Payments & POS Systems | API Testing

- Portfolio: https://bijin1830.github.io
- LinkedIn: https://www.linkedin.com/in/bijinbenni
- GitHub: https://github.com/bijin1830
