# EMV Manual Testing Flow

This document summarizes the EMV transaction stages I consider while testing chip/contactless payment scenarios. It is a QA-oriented overview, not a kernel specification.

## High-level flow

```text
Card detected
   ↓
Application selection
   ↓
Initiate application processing / GPO
   ↓
Read application records
   ↓
Offline data authentication (when applicable)
   ↓
Processing restrictions
   ↓
Cardholder verification (CVM)
   ↓
Terminal risk management
   ↓
Terminal action analysis
   ↓
Card action analysis / Generate AC
   ↓
Online authorization when required
   ↓
Issuer response / second Generate AC
   ↓
Final result: TC / AAC / other implementation-specific outcome
```

## QA checks by stage

### 1. Application selection
- Supported application is selected.
- Unsupported application is not incorrectly chosen.
- Multi-application cards behave according to terminal configuration.

### 2. GPO / processing options
- Terminal provides the expected transaction context.
- Card returns processing options successfully.
- Terminal handles malformed or failed responses safely.

### 3. Read records
- Required application data can be read.
- Terminal does not continue blindly if mandatory records are missing.

### 4. CVM
Examples can include:
- Online PIN
- Offline PIN
- Signature
- No CVM
- CDCVM / device verification for supported contactless wallet scenarios

QA checks:
- Correct CVM is requested for the transaction conditions.
- Cancel/incorrect PIN is handled correctly.
- ECR receives the final result, not just an intermediate prompt.

### 5. Generate AC / final card decision
During investigation, EMV data can help determine whether the card produced an approval-related cryptogram or an application authentication cryptogram indicating decline.

Common tags that may be useful during QA analysis include:

| Tag | Typical meaning |
|---|---|
| 9F26 | Application Cryptogram |
| 9F27 | Cryptogram Information Data |
| 9F10 | Issuer Application Data |
| 9F36 | Application Transaction Counter |
| 95 | Terminal Verification Results |
| 9B | Transaction Status Information |
| 9F34 | CVM Results |
| 9F02 | Amount, Authorized |
| 9C | Transaction Type |
| 82 | Application Interchange Profile |

Exact values must be interpreted together with the EMV/kernel and host context.

## Manual test examples

### Successful chip purchase
Expected:
- Card is read successfully.
- Correct application/CVM is used.
- Online request is sent when required.
- Final terminal result matches the card/host result.

### Incorrect PIN
Expected:
- PIN failure is communicated clearly.
- Terminal does not convert an issuer/card decline into a generic communication error.
- Retry behavior follows configured rules.

### Card removed early
Expected:
- Terminal handles premature removal without crashing.
- Final transaction state can be determined.
- Any uncertain online state is handled safely.

### Contactless interface issue
Expected:
- Terminal does not incorrectly report approval when the card/kernel has declined.
- Interface-switch/fallback instruction is displayed only when allowed.

## Important distinction during defect analysis

A transaction can fail at different layers:

```text
Card / EMV kernel issue
        ≠
Terminal application issue
        ≠
Communication issue
        ≠
Host / issuer decline
        ≠
ECR integration issue
```

A useful defect report should identify the most likely layer based on the available evidence instead of reporting every failure simply as “transaction declined.”
