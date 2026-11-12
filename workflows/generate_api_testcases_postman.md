---
description: Strict API testing workflow — analyze API DOC, confirm assumptions, generate Postman Collection v2.1 JSON.
rules:
  - api-testing.mdc
---

# Workflow: API Test Cases → Postman Collection

Act as a **Senior QA for API testing**.

Follow `.cursor/rules/api-testing.mdc` strictly.

The Rule defines WHAT/HOW to test.
This Workflow defines WHEN/WHAT to output.

## Flow

API DOC
↓
ANALYZE
↓
RULE CHECK
↓
UNCLEAR + ASSUMPTIONS
↓
🛑 USER CONFIRM
↓
TEST SCOPE
↓
GENERATE POSTMAN COLLECTION
↓
SCRIPT ↔ RULE CHECK
↓
🛑 USER REVIEW
↓
FINAL JSON
↓
CLEANUP TEMP FILES
↓
USER IMPORTS INTO POSTMAN

## 1. ANALYZE

Read only the relevant API DOC.

Apply all applicable rules from `api-testing.mdc`.

For each applicable rule area:

Covered / N/A / Unclear

Never silently skip a rule.
Do not generate the test file yet.

When authentication is required:

* Identify Login / Authentication API.
* Identify token response field.
* Identify authenticated APIs.
* Identify authentication dependency/order.

## 2. UNCLEAR + ASSUMPTIONS

Report only issues that affect testing.

| ID | Rule Area | Issue | Suggested Assumption |

Never silently assume missing behavior.

STOP → WAIT FOR USER CONFIRMATION.

## 3. TEST SCOPE

After confirmation, convert applicable rules into test coverage.

Every expected result must come from:

API DOC OR Confirmed Requirement OR Confirmed Assumption.

If no valid source exists:
DO NOT CREATE THE ASSERTION.

For authenticated API flows:

```text
Login
  ↓
Extract access_token
  ↓
Save to Collection Variable: access_token
  ↓
Authenticated APIs
```

## 4. GENERATE POSTMAN COLLECTION

Generate a valid Postman Collection v2.1 JSON.

Output MUST:

* Be valid JSON.
* Follow Postman Collection v2.1.
* Be directly importable into Postman.
* Contain API requests and their test scripts.
* Use environment variables where applicable.
* Use collection variables for reusable authentication values.
* Never hard-code passwords, tokens, API keys, secrets, or production credentials.
* Cover all applicable rules.
* Contain only meaningful test cases.

### Authentication

If authentication is required:

1. Put Login / Authentication API before dependent APIs.
2. Add a Login test script to extract the confirmed token field.
3. Save the token using:

```javascript
pm.collectionVariables.set(
    "access_token",
    response.access_token
);
```

4. Authenticated APIs must use:

```text
Bearer {{access_token}}
```

5. Do not hard-code the token.
6. Do not repeat login scripts in dependent APIs.

If the token field is different, use the field defined by the API DOC or confirmed requirement.

Always save final file under `/testcase/`.

Example:

```text
/testcase/api-test.postman_collection.json
```

JSON file is the official test-case output.

Do not treat Markdown/TXT/standalone JS as primary output.

## 5. SCRIPT ↔ RULE CHECK

Before presenting collection verify:

[ ] All applicable rules covered
[ ] Expected results have valid sources
[ ] Positive / Negative / Boundary covered where applicable
[ ] Validation covered
[ ] Authentication / Authorization covered where applicable
[ ] Login API exists when authentication is required
[ ] Login extracts the correct token field
[ ] Login saves `access_token` to Collection Variable
[ ] Authenticated APIs use `{{access_token}}`
[ ] Login runs before dependent APIs
[ ] Business Logic covered
[ ] Response / Error covered
[ ] Data / Dependency covered where applicable
[ ] Duplicate / Idempotency covered where applicable
[ ] No unsupported assumptions
[ ] No hard-coded secrets
[ ] Valid Postman Collection JSON
[ ] File exists under `/testcase/`

If any applicable item is missing:
DO NOT PRESENT AS COMPLETE.
FIX IT FIRST.

## 6. USER REVIEW

Output:

```text
Test Case File:
/testcase/<filename>.postman_collection.json

Format:
Postman Collection v2.1

Authentication:
Login → Save access_token → Reuse {{access_token}}

Coverage:
<brief summary>

Confirmed Assumptions:
<only confirmed assumptions>
```

STOP → WAIT FOR USER REVIEW.

If user requests changes:

1. Modify requested part.
2. Re-check against `api-testing.mdc`.
3. Re-check authentication flow.
4. Re-check Postman Collection validity.
5. Keep file under `/testcase/`.

## 7. FINALIZE

After user confirmation:

```text
FINAL TEST CASE:
/testcase/<filename>.postman_collection.json
```

File must be ready to:

```text
Postman → Import → Collection → Run
```

Expected execution order:

```text
Login
 ↓
Save access_token
 ↓
Authenticated API 1
 ↓
Authenticated API 2
 ↓
...
```

Cursor does not execute API.

Postman handles execution and actual PASS/FAIL.

## 8. CLEANUP

After final test case file is created and confirmed:

Delete only temporary files created during workflow:

`*.tmp`, `*.temp`, temporary analysis files, intermediate generated files, debug/output files.

Do NOT delete:

`/testcase/*.json`
user files
source files
API DOC
existing project files
existing test cases

Final `/testcase/` should contain only intended test-case output and existing user files.

## Strict Rules

NO silent assumptions
NO unsupported expected results
NO skipped applicable rules
NO unrelated file reading
NO hard-coded secrets
NO duplicated authentication logic
NO authenticated API before Login when token is required
NO execution claims
NO PASS/FAIL claims without evidence
NO non-JSON primary output
NO output outside `/testcase/`

## Token Efficiency

* Do not repeat the Rule.
* Do not repeat the API DOC.
* Read only relevant information.
* Avoid duplicate tests.
* Keep analysis concise.
* Reuse confirmed assumptions and variables.
* Do not create unnecessary temporary files.
* Clean temporary files after completion.
