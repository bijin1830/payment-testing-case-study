# ISO 8583 Manual Validation Guide

This document shows how I approach ISO 8583 message validation during payment testing. Field usage varies by implementation, so these are generic examples only.

## What I normally compare

When a transaction is reported as failed or inconsistent, I compare the request and response for:

- Message type / transaction class
- Processing code
- Transaction amount
- STAN / trace number
- Local transaction date/time
- Entry mode
- Acquirer / terminal / merchant identifiers
- Retrieval reference number
- Authorization code when approved
- Response code
- EMV data when present

## Common fields often useful during QA

| Data element | Typical purpose |
|---|---|
| DE 2 | PAN, usually masked in logs/tools |
| DE 3 | Processing code |
| DE 4 | Transaction amount |
| DE 7 | Transmission date/time |
| DE 11 | STAN / system trace audit number |
| DE 12 / 13 | Local time / date |
| DE 22 | POS entry mode |
| DE 25 | POS condition code |
| DE 35 | Track 2 data; should be protected/masked |
| DE 37 | Retrieval reference number |
| DE 38 | Authorization identification response |
| DE 39 | Response code |
| DE 41 | Terminal ID |
| DE 42 | Merchant ID |
| DE 49 | Transaction currency |
| DE 55 | EMV/ICC data |

> Exact field definitions and presence rules depend on the host specification in use.

## Example manual comparison

### Purchase request

```text
MTI              : 0200   (illustrative only)
Processing Code  : 000000
Amount           : 000000005000
STAN             : 123456
Entry Mode       : CHIP
Terminal ID      : DEMO0001
Merchant ID      : DEMOMERCHANT001
Currency         : 784
```

### Purchase response

```text
MTI              : 0210   (illustrative only)
STAN             : 123456
RRN              : 654321123456
Response Code    : 00
Auth Code        : A12345
```

### QA checks

- Request and response refer to the same transaction.
- Amount is unchanged.
- STAN/reference mapping is correct.
- Response code is interpreted correctly by the POS/ECR.
- Approval code is shown only for the correct successful flow.
- Terminal does not display Approved if host response indicates decline.

## Reversal investigation

For a timeout or uncertain result, I check:

1. Was the original financial request sent?
2. Did the host create a response?
3. Did the terminal/ECR receive that response?
4. Was a reversal generated?
5. Does the reversal contain the correct original transaction references?
6. What is the final host/database status?

This helps separate **host decline**, **communication failure**, **terminal handling issue** and **ECR response-loss issue**.

## Security note

Real PAN, track data, PIN data, cryptographic keys and confidential production messages must never be committed to a public repository. All examples here are synthetic.