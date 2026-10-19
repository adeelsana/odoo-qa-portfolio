# Test cases — Inventory and delivery

Module: Inventory · Level: System · Type: Functional

---

### TC-INV-001 — Full delivery reduces stock correctly

| | |
|---|---|
| **Priority** | High · **Technique** Positive / happy path · **Risk** R2 |
| **Preconditions** | Confirmed order for 10 × P1; P1 on hand = 100 |

**Steps**
1. Open the linked delivery order
2. Set Done quantity = 10 → Validate
3. Check P1 on-hand quantity

**Expected** On hand becomes 90. Delivery state *Done*. Stock move recorded with the correct date, source and destination location.

**Actual** *(to fill)* · **Status** *(to fill)*

---

### TC-INV-002 — Partial delivery creates a correct backorder

| | |
|---|---|
| **Priority** | High · **Technique** Equivalence partitioning · **Risk** R4 |
| **Preconditions** | Confirmed order for 10 × P1; on hand = 100 |

**Steps**
1. Open the delivery, set Done quantity = 4 → Validate
2. When prompted, choose **Create Backorder**
3. Inspect the backorder and the on-hand quantity

**Expected** First delivery *Done* for 4. A backorder exists for exactly 6. On hand = 96. The sales order shows 4 delivered, 6 remaining.

**Actual** *(to fill)* · **Status** *(to fill)*

---

### TC-INV-003 — Partial delivery without backorder

| | |
|---|---|
| **Priority** | High · **Technique** Decision table · **Risk** R4 |
| **Preconditions** | Confirmed order for 10 × P1 |

**Steps**
1. Delivery, Done quantity = 4 → Validate
2. Choose **No Backorder**
3. Inspect the sales order

**Expected** Delivery closes at 4. No backorder created. The sales order reflects that only 4 will ever be delivered — the remaining 6 must not stay silently pending, and must not later become invoiceable as delivered.

**Actual** *(to fill)* · **Status** *(to fill)*

---

### TC-INV-004 — Deliver more than ordered

| | |
|---|---|
| **Priority** | High · **Technique** Boundary value analysis · **Risk** R1, R2 |
| **Preconditions** | Confirmed order for 10 × P1; on hand = 100 |

**Steps**
1. Open the delivery, set Done quantity = 15 → Validate

**Expected** System warns that the delivered quantity exceeds the ordered quantity, or accepts it with a clear consequence for invoicing. Stock must reduce by exactly what was recorded as delivered — no discrepancy between the move and the on-hand figure.

**Actual** *(to fill)* · **Status** *(to fill)*

---

### TC-INV-005 — Sell a product with zero stock

| | |
|---|---|
| **Priority** | High · **Technique** Boundary value analysis · **Risk** R2 |
| **Preconditions** | Product P2, on hand = 0 |

**Steps**
1. Create and confirm an order for 5 × P2
2. Open the delivery and attempt to validate for 5

**Expected** Odoo warns of insufficient stock. Whether it blocks or permits a negative on-hand figure depends on configuration — record which, and check that the resulting on-hand value is arithmetically correct either way (i.e. −5, not 0 or blank).

**Actual** *(to fill)* · **Status** *(to fill)*

---

### TC-INV-006 — Decimal quantity delivery on a weight-based product

| | |
|---|---|
| **Priority** | High · **Technique** Boundary / EP · **Risk** R5 |
| **Preconditions** | Order for 2.375 kg of P4 (from TC-SO-005) |

**Steps**
1. Validate the delivery for the full 2.375 kg
2. Compare on-hand before and after, to 3 decimal places

**Expected** On hand decreases by exactly 2.375. No rounding to 2 or 2.38 in the stock figure.

**Actual** *(to fill)* · **Status** *(to fill)*

> *In wholesale food this is the money case — stock kept in kg but rounded somewhere in the chain produces losses nobody can trace. Verify it in the database too (`06-sql-checks/`), not just the UI.*
