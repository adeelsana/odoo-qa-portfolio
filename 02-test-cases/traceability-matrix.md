# Traceability matrix

Every risk in the test plan must be covered by at least one test case, and every test case must trace to a risk. Gaps in either direction are the point of this document.

| Risk | Description | Test cases | Coverage |
|---|---|---|---|
| R1 | Invoice total does not match delivered goods | TC-SO-001, TC-SO-003, TC-SO-004, TC-INV-004, TC-BILL-001…006, TC-BILL-007 | ☐ |
| R2 | Stock quantity becomes negative or inconsistent | TC-SO-004, TC-INV-001, TC-INV-004, TC-INV-005 | ☐ |
| R3 | Confirmed order modified without audit trail | TC-SO-002, TC-SO-007 | ☐ |
| R4 | Partial delivery creates an incorrect backorder | TC-INV-002, TC-INV-003 | ☐ |
| R5 | Rounding errors on discounts and unit prices | TC-SO-005, TC-SO-006, TC-INV-006, TC-BILL-008 | ☐ |
| R6 | Cancelled order leaves orphaned stock moves | TC-SO-008 | ☐ |

Tick Coverage once every listed case has been executed.

## Execution summary

| | Count |
|---|---|
| Total cases | 22 |
| Executed | |
| Passed | |
| Failed | |
| Blocked | |
| Not run | |
