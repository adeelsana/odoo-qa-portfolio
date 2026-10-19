# Test cases

Seed set below — **22 fully written cases** you execute as-is, plus the pattern to extend.

## How to use this

1. Execute each case exactly as written.
2. Fill in **Actual result** and **Status** (Pass / Fail / Blocked).
3. Every Fail gets a defect report in `03-bug-reports/`, and its ID goes in the Notes column.
4. Do **not** pre-fill results. An empty Actual column is honest; a fabricated one ends your interview.

## Files

- `TC-SO-sales-order.md` — sales order lifecycle and validation (TC-SO-001 … 008)
- `TC-INV-inventory.md` — delivery, partial delivery, stock boundaries (TC-INV-001 … 006)
- `TC-BILL-invoicing.md` — invoicing policy decision table, credit notes (TC-BILL-001 … 008)
- `traceability-matrix.md` — risk and requirement coverage

## Extending

Each seed case names its design technique. To extend, apply the same technique to the next field or transition:

- **Equivalence partitioning** — you have quantity; do unit price, discount %, delivery date, customer reference
- **Boundary value analysis** — you have quantity boundaries; do discount at 0/100/101%, date at today/past/far future, text fields at max length
- **State transition** — you have the order lifecycle; do the invoice lifecycle (Draft → Posted → Paid → Cancelled) and the delivery lifecycle
- **Decision table** — you have invoicing policy; do delivery policy (ship all at once vs as soon as possible) × stock availability

Aim for depth on money and stock rather than breadth on cosmetic fields. Eighty shallow cases impress nobody; forty sharp ones on the risky paths do.
