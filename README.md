# Odoo ERP — Manual QA Portfolio

A hands-on software testing project on **Odoo Community**, an open-source ERP.
Scope: the order-to-cash process — Sales quotation → Sales order → Delivery → Customer invoice — plus the Inventory operations behind it.

**Author:** Muhammad Adeel — ISTQB Certified Tester (Foundation Level), MSc Software Engineering (Blekinge Institute of Technology, 2026), based in the Nantes area.

---

## Why this application

I spent four years as Office & Operations Manager at a wholesale food distributor in Île-de-France, running the exact process this project tests: order intake, stock control, supplier ordering, invoicing and client payment records. Before that I tested a water supply billing system for a government client.

That matters for testing. I know what a wrong stock figure costs when a delivery goes out short, and I know the edge cases a real business hits — partial deliveries, credit notes, price changes after confirmation, unit-of-measure mismatches. This project applies that domain knowledge systematically, using ISTQB test design techniques.

---

## What's in this repository

| Folder | Contents |
|---|---|
| `01-test-plan/` | Test plan — scope, approach, risk analysis, entry/exit criteria |
| `02-test-cases/` | Test cases with design technique, steps and expected results; traceability matrix |
| `03-bug-reports/` | Defect reports for issues found during execution |
| `04-exploratory/` | Session-based exploratory testing — charters and session notes |
| `05-api-testing/` | API testing against Restful-Booker with Postman |
| `06-sql-checks/` | SQL queries verifying data integrity behind the UI |
| `07-test-report/` | Test summary report — results, metrics, conclusions |
| `SETUP.md` | How to stand up the environment under test |

---

## Test design techniques applied

- **Equivalence partitioning** — quantity, price and date input classes
- **Boundary value analysis** — zero, negative and maximum quantities; stock at exactly zero
- **Decision tables** — invoicing policy combinations (ordered vs delivered quantities)
- **State transition testing** — the sales order lifecycle (Draft → Sent → Sale → Done → Cancelled)
- **Experience-based / exploratory** — domain-driven charters from wholesale distribution practice

---

## Status

| Area | Status |
|---|---|
| Environment setup | ☐ |
| Test plan | ☐ |
| Test case design | ☐ |
| Test execution | ☐ |
| Defect reporting | ☐ |
| API testing | ☐ |
| SQL verification | ☐ |
| Test summary report | ☐ |

*Update this table as you go. Recruiters read it first.*

---

## Note

This is a personal portfolio project carried out on a local instance of Odoo Community to demonstrate testing practice. It is not affiliated with or endorsed by Odoo S.A. Defects recorded here are my own observations on the version and configuration stated in the test plan, and are not claims about Odoo's general quality.
