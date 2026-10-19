# Test Plan — Odoo Order-to-Cash

| Field | Value |
|---|---|
| Document | Test Plan |
| Version | 1.0 |
| Author | Muhammad Adeel |
| Date | *(fill in)* |
| Status | Draft |

---

## 1. Introduction

This plan covers functional testing of the order-to-cash process in Odoo Community, an open-source ERP used widely by small and medium businesses. The process under test runs from a customer quotation through to a posted customer invoice, including the inventory movements it triggers.

## 2. Test objectives

1. Verify that a sales order can be created, confirmed, delivered and invoiced, and that data stays consistent at every step.
2. Verify that stock levels update correctly and cannot be driven into invalid states.
3. Verify that invoice amounts correctly reflect ordered and delivered quantities under each invoicing policy.
4. Identify defects, usability problems and data-integrity risks a business user would hit in daily operation.

## 3. Scope

### In scope
- Sales: quotations, sales orders, order confirmation, order modification, cancellation
- Inventory: delivery orders, stock on hand, partial deliveries, backorders
- Invoicing: customer invoice creation from a sales order, invoicing policies, credit notes
- Data integrity between the three modules
- Input validation on quantity, price, discount and date fields

### Out of scope
- Purchase, Manufacturing, Accounting reconciliation, Payroll, Website/eCommerce
- Performance, load and security testing
- Localisation and tax-regime testing beyond the default configuration
- Mobile applications
- Odoo Enterprise-only features

## 4. Test approach

Manual functional testing at **system test level**, black-box, executed against the UI, with database queries used to verify what the UI reports.

Test design techniques:

| Technique | Applied to |
|---|---|
| Equivalence partitioning | Quantity, unit price, discount percentage inputs |
| Boundary value analysis | Quantity at 0, 1, −1, maximum; stock at exactly 0 |
| Decision table testing | Invoicing policy × delivery status combinations |
| State transition testing | Sales order lifecycle: Draft → Sent → Sale → Done / Cancelled |
| Exploratory (session-based) | Domain-driven charters — see `04-exploratory/` |

Test execution is risk-based: high-risk areas (money and stock quantities) are tested first and in most depth.

## 5. Risk analysis

| # | Risk | Impact | Likelihood | Priority | Mitigation |
|---|---|---|---|---|---|
| R1 | Invoice total does not match delivered goods | High | Medium | High | Decision-table tests on invoicing policy; SQL verification of totals |
| R2 | Stock quantity becomes negative or inconsistent | High | Medium | High | Boundary tests at zero stock; SQL check of `stock_quant` |
| R3 | Confirmed order can be modified without audit trail | Medium | Medium | Medium | State transition tests on post-confirmation edits |
| R4 | Partial delivery creates an incorrect backorder | High | Medium | High | Dedicated partial-delivery test cases |
| R5 | Rounding errors on discounts and unit prices | Medium | Medium | Medium | Boundary values on discount %; SQL comparison of computed totals |
| R6 | Cancelled order leaves orphaned stock moves | Medium | Low | Low | Cancellation tests plus SQL check for orphaned records |

## 6. Entry criteria

- Odoo instance installed and reachable
- Sales, Inventory and Invoicing modules installed
- Demonstration data loaded
- Test data prepared (see section 8)
- Environment details recorded in section 9

## 7. Exit criteria

- 100% of planned test cases executed
- No open defects of severity Critical or High, or each one documented with justification for acceptance
- All High-priority risks in section 5 covered by at least one executed test case
- Test summary report completed in `07-test-report/`

## 8. Test data

| Item | Value |
|---|---|
| Customer A | Azure Interior — demo customer. Carries default payment terms "End of Following Month" |
| Customer B | Openwood — payment terms set to "30 Days" for this project |
| Product P1 | TEST-P1 Standard Storable — Goods, Track Inventory, $10.00, Units, on hand 100 |
| Product P2 | TEST-P2 Zero Stock — Goods, Track Inventory, $10.00, Units, on hand 0 |
| Product P3 | TEST-P3 Service — Service, $50.00, no stock tracking |
| Product P4 | TEST-P4 By Weight — Goods, Track Inventory, $12.50 per kg, on hand 50 kg |

All four test products have sales and purchase taxes removed, so every expected
total in this plan is tax-exclusive. Product P4 exists because wholesale food
distribution sells by weight, and decimal quantity handling is where defects
tend to hide.

## 9. Test environment


| Component | Value |
|---|---|
| Odoo | Community, version 19.0-20260908 (user menu → About) |
| Database | PostgreSQL 16 |
| Deployment | Docker Compose (postgres:16 + odoo:latest), local |
| Company | My Company (San Francisco) — Odoo demo company |
| Currency | USD ($) |
| Taxes | Removed from all test products; expected totals are tax-exclusive |
| Demo data | Loaded |
| Browser | Google Chrome 155.0.8059.80 (Official Build) (64-bit) (cohort: Stable)  (chrome://version) |
| Operating system | Windows Windows 11 Version 25H2 (Build 26200.9711)|
## 10. Deliverables

- This test plan
- Test cases and traceability matrix — `02-test-cases/`
- Defect reports — `03-bug-reports/`
- Exploratory charters and session notes — `04-exploratory/`
- SQL verification queries and results — `06-sql-checks/`
- Test summary report — `07-test-report/`

## 11. Schedule

| Day | Activity |
|---|---|
| 1 | Environment setup, test data creation, plan finalised |
| 2 | Test case design — Sales and Invoicing |
| 3 | Test case design — Inventory; traceability matrix |
| 4 | Execution, first pass; defect logging |
| 5 | Exploratory sessions; SQL verification |
| 6 | API testing (Restful-Booker); retest of fixed or reproducible defects |
| 7 | Test summary report; repository tidy-up |
