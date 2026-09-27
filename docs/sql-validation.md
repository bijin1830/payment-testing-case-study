# Basic SQL Data Checks for Payment QA

This guide demonstrates simple SQL/data checks that can support payment testing and incident investigation. It is intended as basic QA-level database verification, not advanced SQL or database engineering.

> Queries are intentionally generic. They do not reference any real client schema, credentials or production data.

## Basic checks I can perform

Examples of basic checks include:

- Transaction status
- Amount and currency
- Terminal / merchant mapping
- STAN / RRN / internal reference
- Approval / decline code
- Reversal relationship
- Refund relationship
- Batch / settlement status
- Duplicate records
- Timestamps across application layers

## Synthetic transaction table

Assume a demo table:

```sql
payment_transaction
(
    id,
    transaction_type,
    amount,
    currency,
    terminal_id,
    merchant_id,
    stan,
    rrn,
    response_code,
    auth_code,
    status,
    original_transaction_id,
    batch_no,
    created_at
)
```

## Find a transaction by reference

```sql
SELECT
    id,
    transaction_type,
    amount,
    currency,
    terminal_id,
    stan,
    rrn,
    response_code,
    status,
    created_at
FROM payment_transaction
WHERE rrn = 'SYNTHETIC_RRN';
```

## Check potential duplicates

```sql
SELECT
    terminal_id,
    amount,
    stan,
    COUNT(*) AS record_count
FROM payment_transaction
WHERE created_at >= DATEADD(MINUTE, -10, CURRENT_TIMESTAMP)
GROUP BY terminal_id, amount, stan
HAVING COUNT(*) > 1;
```

## Example: check reversal linkage

```sql
SELECT
    original.id AS original_id,
    original.status AS original_status,
    reversal.id AS reversal_id,
    reversal.status AS reversal_status
FROM payment_transaction original
LEFT JOIN payment_transaction reversal
    ON reversal.original_transaction_id = original.id
   AND reversal.transaction_type = 'REVERSAL'
WHERE original.rrn = 'SYNTHETIC_RRN';
```

## Example: check settlement totals

```sql
SELECT
    batch_no,
    transaction_type,
    COUNT(*) AS txn_count,
    SUM(amount) AS total_amount
FROM payment_transaction
WHERE batch_no = 101
  AND status = 'APPROVED'
GROUP BY batch_no, transaction_type;
```

## How I use these checks in QA

These basic database checks should not be used in isolation. I compare DB state with:

1. POS result
2. ECR result
3. Host response
4. API response where applicable
5. Settlement/reporting output

The goal is to confirm the same transaction has a consistent state across every layer.
