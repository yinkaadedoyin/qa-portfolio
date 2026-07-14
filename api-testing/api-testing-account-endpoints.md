# API Testing: Account Balance & Transaction History Endpoints

## Overview
This documents API-level testing performed against a sample banking API's account and transaction endpoints, using Postman. It covers functional validation, error handling, and edge cases that UI-level testing alone would miss.

## Endpoints Tested

### `GET /accounts/{accountId}/balance`
Returns the current balance for a given account.

### `GET /accounts/{accountId}/transactions`
Returns a paginated list of transactions for a given account.

## Test Approach
- Validated response structure and data types against API documentation (Swagger)
- Verified correct HTTP status codes for success and failure scenarios
- Checked error handling for invalid, missing, or malformed inputs
- Validated pagination behavior on the transactions endpoint
- Confirmed authentication/authorization is enforced (no access without valid token)

## Test Cases

| ID | Endpoint | Scenario | Expected Result |
|----|----------|----------|------------------|
| API-01 | GET /balance | Valid account ID, valid auth token | 200 OK, correct balance returned, matches known seeded value |
| API-02 | GET /balance | Invalid/non-existent account ID | 404 Not Found, clear error message |
| API-03 | GET /balance | Missing auth token | 401 Unauthorized |
| API-04 | GET /balance | Expired auth token | 401 Unauthorized, token-specific error message |
| API-05 | GET /balance | Valid account ID, but belongs to a different user | 403 Forbidden |
| API-06 | GET /transactions | Valid account ID, default pagination | 200 OK, returns expected page size, correct transaction fields |
| API-07 | GET /transactions | Request page beyond available data | 200 OK, empty array returned (not an error) |
| API-08 | GET /transactions | Invalid page/limit parameter (e.g., negative number) | 400 Bad Request, validation error |
| API-09 | GET /transactions | Malformed account ID (e.g., SQL injection string) | 400 Bad Request, input rejected safely, no server error |
| API-10 | GET /transactions | Response time under load (10 sequential requests) | All responses under 1s, no degradation or timeout |

## Sample Assertions (Postman test scripts)

```javascript
// API-01: Validate successful balance response
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response has correct balance field", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property("balance");
    pm.expect(jsonData.balance).to.be.a("number");
});

// API-03: Validate missing auth token is rejected
pm.test("Status code is 401 without auth token", function () {
    pm.response.to.have.status(401);
});
```

## Findings
- Confirmed the API correctly returns 403 (not 404) when a valid account ID belongs to a different user — important for not leaking account existence to unauthorized users
- Verified malformed input is safely rejected with 400 rather than causing a server error, reducing risk of injection-based vulnerabilities
- Identified pagination edge case: requesting a page beyond available data correctly returns an empty array rather than an error, which matches expected API design

## Notes
This is a sample API testing exercise created to demonstrate approach, test case design, and Postman scripting for API-level validation. Test cases are based on common patterns in banking/fintech account APIs.
