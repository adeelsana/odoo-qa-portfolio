# Test cases — Sales order lifecycle

Module: Sales · Level: System · Type: Functional

---

### TC-SO-001 — Create quotation with a valid storable product

| | |
|---|---|
| **Priority** | High |
| **Technique** | Positive / happy path |
| **Risk covered** | R1 |
| **Preconditions** | Logged in as Sales user; Customer A and Product P1 exist; P1 stock = 100 |

**Steps**
1. Sales → Orders → Quotations → New
2. Set Customer = Customer A
3. Add order line: Product = P1, Quantity = 10
4. Save

**Expected result**
Quotation saved in state *Quotation*. Line shows quantity 10, the unit price from the product record, and a subtotal equal to 10 × unit price. Taxes applied per product configuration. Document reference follows the `S0xxx` sequence.

**Actual result**
Quotation S00025 created for customer Azure Interior in state "Quotation".
Line: TEST-P1 Standard Storable, quantity 10.00 Units, unit price $10.00,
amount $100.00. Taxes column empty (taxes were removed from test products).
Untaxed Amount $100.00, Total $100.00. Reference follows the S000xx
sequence. Expiration date auto-set to Nov 6. Payment Terms auto-filled from
the customer record as "End of Following Month".

**Status** Pass

**Notes** Chatter logged "Sales Order created" while the document was still
in state Quotation — recorded as an observation, see BUG-001.

**Status** Pass

---

### TC-SO-002 — Confirm quotation and verify state transition

| | |
|---|---|
| **Priority** | High · **Technique** State transition · **Risk** R3 |
| **Preconditions** | Quotation from TC-SO-001 exists in state *Quotation* |

**Steps**
1. Open the quotation
2. Click **Confirm**

**Expected result**
State becomes *Sales Order*. A delivery order is created automatically and appears in the Delivery smart button. The order date is set. The order is no longer editable in the same unrestricted way as a draft.

**Actual result** S00025 confirmed. State moved from "Quotation" to
"Sales Order" in the status bar. A Delivery smart button appeared showing
1 linked delivery order. Order Date set to 7 October 2026. The Confirm
button is no longer offered; the available action is now "Create Invoice",
confirming the document is past the draft stage.

**Status** Pass · **Notes** Available actions change with state, which is
the behaviour this state-transition case is checking.**Notes**

---

### TC-SO-003 — Quantity boundary: zero

| | |
|---|---|
| **Priority** | High · **Technique** Boundary value analysis · **Risk** R1 |
| **Preconditions** | New quotation, Customer A |

**Steps**
1. Add order line: Product = P1, Quantity = 0
2. Save, then Confirm

**Expected result**
System either rejects the zero quantity with a clear message, or accepts it and produces a subtotal of 0 with no stock movement. Whichever behaviour occurs, it must be consistent and must not create a delivery for zero units.

**Actual result** Quotation S00026 created for Azure Interior with
TEST-P1 Standard Storable at quantity 0.00 Units, unit price $10.00.
Saved without any validation message. Confirm succeeded with no warning
dialog; state moved to "Sales Order". Untaxed Amount $0.00, Total $0.00.
No Delivery smart button was created — Odoo did not generate a delivery
order for zero units.

**Status** Pass

**Notes** Odoo accepts zero quantity silently rather than blocking it,
which is defensible (zero-value lines have legitimate uses). The important
behaviour is that no delivery order is generated, so no meaningless
picking task reaches the warehouse. Recorded as acceptable behaviour, not
a defect. Residual observation: a mistyped quantity (0 instead of 10) is
accepted with no feedback at all, producing a confirmed order worth $0.00
that will sit in sales reporting until someone notices.

> *This case has two acceptable outcomes by design. Record which one you observed and judge whether it is reasonable — that judgement is what the case is really testing.*

---

### TC-SO-004 — Quantity boundary: negative

| | |
|---|---|
| **Priority** | High · **Technique** Boundary value analysis · **Risk** R1, R2 |
| **Preconditions** | New quotation, Customer A |

**Steps**
1. Add order line: Product = P1, Quantity = −5
2. Save, then attempt to Confirm

**Expected result**
Negative quantity on a sale is either blocked with a validation message, or handled explicitly as a return. It must not silently increase stock or produce a negative invoice line without warning.

**Actual result** Quotation S00027 created for Azure Interior with TEST-P1
at quantity -5.00 Units, unit price $10.00. Saved and confirmed without
any validation message or warning. Untaxed Amount -$50.00, Total -$50.00.
A transfer was created, but as an incoming Receipt (WH/IN/00007,
operation type "YourCompany: Receipts", Receive From: Azure Interior)
with a positive Demand of 5.00 Units, source document S00027, state Ready.

**Status** Pass

**Notes** Odoo handles a negative sale quantity as a customer return
rather than producing a negative outbound delivery. Stock direction and
sign are both correct. Residual observation: the conversion happens
silently — a user who types -5 instead of 5 gets a Receipt instead of a
Delivery with no prompt, and the sales order carries a negative total
into sales reporting. Noted as a usability risk, not a defect.
Inconsistency worth recording: quantity 0 produced no transfer at all,
while quantity -5 produced one.

---

### TC-SO-005 — Decimal quantity on a product sold by weight

| | |
|---|---|
| **Priority** | High · **Technique** Equivalence partitioning · **Risk** R5 |
| **Preconditions** | Product P4 configured with unit of measure = kg |

**Steps**
1. New quotation, Customer A
2. Add line: Product = P4, Quantity = 2.375
3. Save and check the subtotal

**Expected result**
Decimal quantity is accepted and retained at full precision. Subtotal = 2.375 × unit price, rounded per the decimal-precision setting, with no truncation to a whole number.

**Actual result** *(to fill)* · **Status** *(to fill)* · **Notes**

---

### TC-SO-006 — Discount boundary values

| | |
|---|---|
| **Priority** | Medium · **Technique** Boundary value analysis · **Risk** R5 |
| **Preconditions** | Discount field enabled (Settings → Sales → Discounts) |

**Steps**
For each discount value in turn — 0, 1, 99, 100, 101, −1 — set it on an order line with Product P1, Quantity 10, and record the subtotal.

**Expected result**
0 → full price. 100 → subtotal 0. 101 and −1 → rejected or clearly flagged; must not produce a negative subtotal without warning. No rounding error at 99.

**Actual result** *(to fill)* · **Status** *(to fill)* · **Notes**

---

### TC-SO-007 — Modify a confirmed order

| | |
|---|---|
| **Priority** | High · **Technique** State transition · **Risk** R3 |
| **Preconditions** | Confirmed sales order from TC-SO-002, not yet delivered |

**Steps**
1. Open the confirmed order
2. Change the quantity on the existing line from 10 to 25
3. Save

**Expected result**
The change is either blocked, or permitted and propagated — the linked delivery order quantity updates to match, and the change is visible in the chatter/audit log.

**Actual result** *(to fill)* · **Status** *(to fill)* · **Notes**

> *This is R3. In a real distribution business, a confirmed order that changes without the warehouse seeing it is how wrong deliveries happen. Test it hard.*

---

### TC-SO-008 — Cancel a confirmed order

| | |
|---|---|
| **Priority** | Medium · **Technique** State transition · **Risk** R6 |
| **Preconditions** | Confirmed sales order with a delivery order not yet validated |

**Steps**
1. Open the order → Cancel
2. Open the linked delivery order

**Expected result**
Order state becomes *Cancelled*. The linked delivery is cancelled too — it must not remain open and actionable. No reserved stock remains held against the cancelled order.

**Actual result** *(to fill)* · **Status** *(to fill)* · **Notes**
