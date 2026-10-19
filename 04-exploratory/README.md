# Session-based exploratory testing

Scripted test cases find the defects you thought of in advance. Exploratory sessions find the rest. Both belong in a portfolio — showing only scripted cases suggests you can follow a plan; showing both suggests you can test.

## Method

Time-boxed sessions of 60 minutes against a written charter. Notes taken during the session, not after. Each session produces: what was covered, what was found, and what is still unexplored.

## Charters

| ID | Charter | Duration | Status |
|---|---|---|---|
| ES-01 | Explore **decimal quantities and units of measure** across order, delivery and invoice, to discover rounding and conversion inconsistencies | 60 min | ☐ |
| ES-02 | Explore **the confirmed-order modification path** with a focus on what the warehouse sees, to discover desynchronisation between order and delivery | 60 min | ☐ |
| ES-03 | Explore **cancellation and reversal at every stage** (quotation, confirmed, partly delivered, invoiced) to discover orphaned records and stuck states | 60 min | ☐ |
| ES-04 | Explore **multi-line orders with mixed product types** (storable, service, weight-based) to discover incorrect handling of the mix | 60 min | ☐ |
| ES-05 | Explore **the customer-facing documents** (quotation PDF, delivery note, invoice PDF) to discover inconsistencies between what is on screen and what is printed | 60 min | ☐ |

*ES-05 is worth doing. Printed-document defects are common, rarely tested, and immediately understandable to a non-technical interviewer.*

Use `SESSION-NOTES-TEMPLATE.md` for each.
