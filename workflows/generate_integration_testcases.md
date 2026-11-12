---
description: Generate Integration/E2E test cases for cross-site/cross-screen business flows from Basic Design, excluding scenarios already covered by Unit Tests.
rules:
  - test-viewpoint.mdc
  - test-case-generation.mdc
---

# Workflow: Integration Test Case Generation

Role: Senior QA Engineer.

## 1. Objective

Generate Integration Test Cases for business flows that span multiple sites/portals, roles or screens.

Example (Fairtech) — three sites:

- Sys Admin
- Company
- User

Focus on end-to-end business flows, cross-site data consistency, role permissions, state transitions and integration points.

**Do not duplicate existing Unit Test coverage.**

## 2. Required Input Documents

Before generating test cases, inspect the existing project and locate:

- Basic Design
- Existing Unit Test Checklists and Unit Test Cases
- Test Viewpoint document
- Existing QA rules and workflows
- API specifications and DB design, if available
- Existing Integration/E2E Test Cases, if available

Reuse the actual file paths found in the repository. Do not assume files exist at a specific location.

If a required document is missing or unreadable, report the missing document and its impact. Do not invent its contents.

## 3. Workflow

### Step 1 — Analyze Basic Design

Read the relevant Basic Design and identify:

- Business flows involving two or more sites
- Data created or modified in one site and consumed by another
- Relationships between company, office, service type, video, announcement and user
- Role-based permissions and organizational boundaries
- State transitions and dependent data
- API, DB or other integration points
- Error handling at integration boundaries

Focus on business outcomes, not individual screen functionality.

### Step 2 — Identify Existing Unit Test Coverage

Compare the candidate integration scenarios against existing Unit Test Checklists and Test Cases.

Classify each scenario as:

- UNIT-COVERED: already verified independently by Unit Tests
- INTEGRATION-REQUIRED: requires verification across sites, roles, components or persisted state
- PARTIALLY-COVERED: unit tests cover individual operations, but not their interaction
- UNCLEAR: existing coverage or expected behavior cannot be determined

Rules:

- Do not reproduce individual field validation, button behavior, CRUD functionality or screen-level checks already covered by Unit Tests.
- Do not copy or summarize existing Unit Test Cases into the Integration Test Checklist.
- Retain a scenario when it verifies an interaction or business outcome that cannot be established by the existing Unit Tests.
- Reuse existing test evidence to identify gaps; do not assume that a unit test proves the entire business flow.
- Record the reason for retaining or excluding each candidate scenario.

### Step 3 — Identify Gaps and Unclear Requirements

Analyze missing coverage involving:

- Data propagation between Sys Admin, Company and User
- Data consistency and relationship integrity
- Role permissions and cross-company data isolation
- Business state transitions
- Dependency constraints
- Integration errors and partial failures
- Regression risks caused by changes in one site affecting another

Create a clarification table:

| ID | Business Flow | Unclear Point / Gap | QA Concern | Suggested Assumption |
|---|---|---|---|---|

Do not silently assume missing requirements.

**STOP:** Present the analysis, Unit Test exclusions, integration gaps and clarification questions. Wait for user confirmation before generating the Integration Test Checklist.

### Step 4 — Generate Integration Test Checklist

After confirmation, generate a checklist containing only relevant integration scenarios.

Required columns:

| ID | Business Flow | Sites Involved | Integration Scenario | Expected Business Outcome | Priority | Existing Unit Test Coverage | Reason for Integration Test |
|---|---|---|---|---|---|---|---|

Prioritize scenarios using business impact and risk:

- P1: Critical cross-site business flow, authorization or data integrity
- P2: Important business interactions and dependency rules
- P3: Lower-risk integration and less frequent scenarios

Ensure the checklist covers the applicable requirements and Test Viewpoints without reproducing existing Unit Test coverage.

**STOP:** Save and present the checklist. Wait for user review and confirmation before generating detailed test cases.

### Step 5 — Generate Detailed Integration Test Cases

After checklist approval, generate detailed test cases for approved scenarios only.

Required columns:

| Field | Description |
|---|---|
| Test Case ID | Unique identifier |
| Checklist ID | Related integration scenario |
| Test Objective | Business outcome being verified |
| Preconditions | Required system state and permissions |
| Test Data | Unique test data and entity relationships |
| Steps | Ordered actions across sites and roles |
| Expected Result | Observable business outcome for each critical step |
| Priority | P1 / P2 / P3 |
| Test Type | Positive / Negative / Boundary / Recovery |
| Unit Test Overlap | None / Partial / Required Integration Verification |

Each test case must:

- Specify the site and role for every relevant action.
- Verify data created or changed in one site from the perspective of another site.
- Verify final state and data consistency where required.
- Verify unauthorized access or operations where relevant.
- Avoid repeating screen-level assertions unless needed to establish the integration outcome.
- Use only requirements supported by Basic Design or user-approved assumptions.
- Identify unavailable test prerequisites or unverifiable expectations.

### Step 6 — QA Review

Review the generated test cases against:

- Basic Design
- Test Viewpoint
- Approved clarification decisions
- Existing Unit Test coverage
- Integration gaps identified in Step 3

Check for:

- Duplicate test cases
- Unit Test scenarios incorrectly included as integration tests
- Missing cross-site business flows
- Missing role and data-isolation checks
- Incomplete preconditions or expected results
- Unclear or unsupported assumptions
- Missing traceability to requirements

Fix identified issues before finalizing.

### Step 7 — Finalize and Report

Save approved artifacts in the existing test-management structure. If no structure exists, propose:

- `testcase/integration/integration-test-checklist.md`
- `testcase/integration/integration-test-cases.md`
- `testcase/integration/unit-test-coverage-mapping.md`

Generate a summary containing:

- Number of candidate scenarios
- Number excluded due to existing Unit Test coverage
- Number retained for integration verification
- Number of partially covered scenarios
- Number of unresolved requirements
- Coverage by site combination and business flow
- P1/P2/P3 distribution
- Known limitations and outstanding decisions

Use test statuses:

- Pass
- Fail
- Unchecked

Do not mark test cases as executed or passed unless actual execution evidence exists.

## 4. Scope Boundaries

IN SCOPE:
- Cross-site business flows
- Data consistency and propagation
- Integration between components and APIs
- Role and organizational permissions
- State transitions and dependency rules
- Integration error handling and recovery

OUT OF SCOPE:
- Repeating existing screen-level Unit Tests
- Repeating isolated CRUD or field validation tests without an integration objective
- Inventing requirements or expected results
- Executing tests before the test cases are reviewed and approved

## 5. Cursor Execution Rules

- Inspect relevant files before modifying the repository.
- Reuse existing QA rules and naming conventions.
- Minimize unnecessary source-code scanning; inspect relevant code only when needed to establish integration behavior.
- Do not overwrite existing test artifacts.
- Do not create detailed test cases before the checklist is approved.
- Stop and ask for clarification when a missing requirement materially affects expected behavior.
- Keep generated test cases traceable to Basic Design and the approved checklist.
- Report actual files inspected, artifacts created and unresolved issues.

## 6. Execution Sequence

Basic Design + Existing Unit Tests + Test Viewpoint
→ Business Flow Analysis
→ Unit Test Coverage Comparison
→ Integration Gap Analysis
→ STOP for User Confirmation
→ Integration Test Checklist
→ STOP for Checklist Approval
→ Detailed Integration Test Cases
→ QA Review
→ Final Report

**Core Principle:** Test the interaction between components, sites and roles—not the same individual functionality that has already been verified by Unit Tests.