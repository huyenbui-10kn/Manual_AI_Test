---
description: Senior QA workflow from Basic Design — requirement review, risk analysis, test checklist, execution, report and cleanup.
rules:
  - checklist-generation.mdc
  - test-viewpoint.mdc
---

> **BẮT BUỘC:** Đọc rule `.cursor/rules/checklist-generation.mdc` (format checklist) và `.cursor/rules/test-viewpoint.mdc` (catalog viewpoint) nếu chưa có trong context. Nếu project có `test-knowledge/`, tuân theo `checklist-conventions.md` và tra `known-issues-and-lessons.md` trước khi kết luận FAIL.

# Workflow: QA Checklist from Basic Design

When a Basic Design is provided, act as a **Senior QA** and follow:

```text
Basic Design
    ↓
Requirement Review
    ↓
Risk & Test Scope Analysis
    ↓
Find Unclear / Missing Points
    ↓
Suggest Assumptions & Questions
    ↓
WAIT FOR USER CONFIRMATION
    ↓
Generate Test Checklist
    ↓
QA Review & Risk Check
    ↓
WAIT FOR USER REVIEW & CONFIRMATION
    ↓
Execute Checklist
    ↓
Analyze Results / Defects
    ↓
Test Report
    ↓
Cleanup Temporary Files
```

## 1. Requirement Review

* Read and understand the Basic Design.
* Identify functions, UI, validations, business rules, errors and integrations.
* Find unclear, missing, inconsistent or risky requirements.
* Do not make assumptions silently.

Output:

| ID | Point | QA Concern | Assumption / Question |
| -- | ----- | ---------- | --------------------- |

**STOP and wait for user confirmation.**

## 2. Test Scope & Risk Analysis

* Define scope based on requirements and risk.
* Prioritize critical/high-risk, boundary, negative, integration, security and performance areas.
* Use the **Test Viewpoint** to identify applicable viewpoints.
* Avoid unnecessary checks.

## 3. Test Checklist

After confirmation:

* Use Basic Design + confirmed assumptions + Test Viewpoint + risk analysis.
* Generate relevant checklist items only.
* Checklist = **WHAT to check**, not detailed test steps.
* Initial status = `UNCHECK`.

| ID    | Screen | Target  | Viewpoint    | Checklist          | Status  |
| ----- | ------ | ------- | ------------ | ------------------ | ------- |
| CL001 | Login  | User ID | Input Format | Check input format | UNCHECK |

**STOP and wait for user review and confirmation.**

## 4. Execute

After confirmation:

* Execute `UNCHECK` items.
* Status: `PASS / FAIL / UNCHECK`.
* Require evidence for PASS/FAIL.
* For FAIL, record Expected, Actual, Evidence and Comment.
* Identify defect and regression impact.

## 5. Report

Export:

| Status  | Count |
| ------- | ----: |
| PASS    |    XX |
| FAIL    |    XX |
| UNCHECK |    XX |
| TOTAL   |    XX |

Result:

```text
FAIL    → any FAIL exists
UNCHECK → no FAIL but UNCHECK exists
PASS    → all PASS
```

Include major risks, defects and remaining concerns.

## 6. Cleanup

* Delete only temporary files created by the workflow.
* Never delete Basic Design, Test Viewpoint, Checklist, Report, source code or project files.
* If uncertain, do not delete.
