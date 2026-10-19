# API testing — Restful-Booker

Target: <https://restful-booker.herokuapp.com/> — a free CRUD Web API built for API testing practice, deliberately seeded with defects.

**Be aware:** this is a popular practice target, so a recruiter may have seen it before. That is fine — it is here to demonstrate Postman and API technique, while the Odoo work carries the originality. Do not make this the centrepiece of the repository.

## Endpoints under test

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/auth` | Obtain a token |
| GET | `/booking` | List booking IDs |
| GET | `/booking/{id}` | Retrieve a booking |
| POST | `/booking` | Create a booking |
| PUT | `/booking/{id}` | Full update (requires auth) |
| PATCH | `/booking/{id}` | Partial update (requires auth) |
| DELETE | `/booking/{id}` | Delete (requires auth) |

Read the docs at <https://restful-booker.herokuapp.com/apidoc/index.html> before designing anything — testing an API without reading its contract is guessing.

## What to test

**Functional**
- Each endpoint returns the documented status code and response body
- A created booking is retrievable and its fields match what was sent
- Updates persist; deletes actually delete

**Authentication**
- PUT / PATCH / DELETE without a token → 403
- With an invalid or expired token → 403
- Token generation with wrong credentials → no token issued

**Validation and negative testing**
- Missing mandatory fields → sensible error, not 500
- `checkout` date earlier than `checkin`
- Wrong data types (string where a number is expected, `totalprice` as text)
- Very long strings in name fields
- Empty request body
- Non-existent booking ID → 404

**Boundary**
- `totalprice` at 0, negative, and very large
- Dates at far past and far future

## Test case log

| ID | Endpoint | Scenario | Expected | Actual | Status |
|---|---|---|---|---|---|
| API-001 | POST /auth | Valid credentials | 200 + token | | |
| API-002 | POST /auth | Invalid password | No token issued | | |
| API-003 | POST /booking | Valid payload | 200 + booking returned matches input | | |
| API-004 | GET /booking/{id} | Existing ID | 200 + correct booking | | |
| API-005 | GET /booking/{id} | Non-existent ID | 404 | | |
| API-006 | PUT /booking/{id} | No auth token | 403 | | |
| API-007 | PUT /booking/{id} | Valid token, valid payload | 200 + updated values persist | | |
| API-008 | POST /booking | Missing `firstname` | 400 or clear error, not 500 | | |
| API-009 | POST /booking | `checkout` before `checkin` | Rejected or documented behaviour | | |
| API-010 | POST /booking | `totalprice` negative | Rejected or documented behaviour | | |
| API-011 | DELETE /booking/{id} | Valid token | 201, then GET returns 404 | | |
| API-012 | PATCH /booking/{id} | Single field update | Only that field changes | | |

Extend this. Twelve is a starting point.

## Deliverables for this folder

1. `restful-booker.postman_collection.json` — export from Postman (Collection → … → Export → Collection v2.1)
2. `restful-booker.postman_environment.json` — environment with `baseUrl` and `token` variables, **with any real values removed**
3. Screenshot of a test run
4. Defect reports for what you find — same format as `03-bug-reports/`

## Postman tips worth demonstrating

- Store the token from `/auth` into an environment variable in the Tests tab, so later requests use it automatically — this shows you understand request chaining
- Add assertions in the Tests tab (`pm.test(...)`) rather than checking responses by eye — this is the step from "clicking around" to "automated checks", and it is the honest bridge toward the automation work you plan next
- Use the Collection Runner and screenshot the summary
