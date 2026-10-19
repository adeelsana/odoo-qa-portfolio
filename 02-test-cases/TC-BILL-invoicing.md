# Test cases — Invoicing

Module: Invoicing · Level: System · Type: Functional

---

## Decision table — invoicing policy × delivery status

Odoo products have an invoicing policy of **Ordered quantities** or **Delivered quantities**. This is the highest-risk logic in the whole flow: it decides what the customer is billed.

| Rule | Invoicing policy | Ordered | Delivered | Expected invoice quantity |
|---|---|---|---|---|
| 1 | Ordered | 10 | 0 | 10 |
| 2 | Ordered | 10 | 4 | 10 |
| 3 | Ordered | 10 | 10 | 10 |
| 4 | Delivered | 10 | 0 | Nothing to invoice |
| 5 | Delivered | 10 | 4 | 4 |
| 6 | Delivered | 10 | 10 | 10 |

Each rule below is a test case.

---

### TC-BILL-001 to TC-BILL-006 — Decision table execution

| | |
|---|---|
| **Priority** | High · **Technique** Decision table · **Risk** R1 |
| **Preconditions** | Product P1 configured with the policy under test; order and delivery set to the row's quantities |

**Steps (per rule)**
1. Configure P1 invoicing policy per the row
2. Create and confirm an order for the Ordered quantity
3. Deliver the Delivered quantity (backorder as needed)
4. On the sales order, click **Create Invoice** → Regular invoice
5. Record the invoice quantity and total

**Expected** Invoice quantity matches the table. Invoice total = quantity × unit price, plus tax. For rule 4, Odoo must state there is nothing to invoice rather than producing a zero-value or full-quantity invoice.

| Rule | Actual invoice qty | Total | Status | Defect |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |
| 6 | | | | |

---

### TC-BILL-007 — Credit note reverses a posted invoice

| | |
|---|---|
| **Priority** | Medium · **Technique** State transition · **Risk** R1 |
| **Preconditions** | A posted customer invoice exists |

**Steps**
1. Open the posted invoice → **Credit Note** → Full refund
2. Post the credit note
3. Check the customer balance

**Expected** Credit note created for the exact invoice amount with a reference to the original. Customer balance returns to its pre-invoice value. Original invoice remains in the system (not deleted) with its status updated.

**Actual** *(to fill)* · **Status** *(to fill)*

---

### TC-BILL-008 — Invoice total rounding across many lines

| | |
|---|---|
| **Priority** | Medium · **Technique** Boundary value analysis · **Risk** R5 |
| **Preconditions** | New order, Customer A |

**Steps**
1. Add 10 lines, each Product P1, Quantity 3, unit price set to 0.07
2. Confirm, deliver, invoice
3. Compare the invoice total against a manual calculation

**Expected** Invoice total equals the sum of line subtotals with no accumulated rounding drift. Tax is calculated on the correct base.

**Actual** *(to fill)* · **Status** *(to fill)*
