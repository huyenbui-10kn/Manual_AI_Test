---
description: Sinh manual test case theo quy trình FULL RBT 6 bước, đi đúng pipeline Requirement → Test Viewpoint → Test Case → Viewpoint Coverage, có checkpoint với user.
skills:
  - rbt-manual-testing
  - requirements-analyzer
---

> **BẮT BUỘC:** Đọc skill `rbt-manual-testing` tại `.cursor/skills/rbt-manual-testing/SKILL.md` và chạy **Mode FULL RBT**. Đọc thêm các rule nếu chưa có trong context:
> - `.cursor/rules/requirement-analysis.mdc`
> - `.cursor/rules/test-viewpoint.mdc`
> - `.cursor/rules/test-case-generation.mdc`
> - `.cursor/rules/viewpoint-coverage.mdc`
>
> Khi workflow, skill và rule khác nhau thì rule thắng. Chỉ đọc skill `requirements-analyzer` khi phải phân tích từ giao diện web.

# Workflow: Sinh Manual Test Case theo FULL RBT

```
Requirement → Test Viewpoint → Test Case → Viewpoint Coverage
```

## Nguyên tắc thực thi

- Chạy **tuần tự** 6 bước. Không gộp bước.
- **Dừng chờ user** ở Bước 1 (scope), Bước 2 (QnA) và Bước 4 (duyệt viewpoint).
- Cấm sinh test case khi chưa có bảng viewpoint đã duyệt.
- Chưa có tài liệu requirement thì hỏi user trước khi bắt đầu.
- Không đoán business logic. Thiếu thông tin → QnA; không được trả lời → Assumption ghi rõ.
- Mọi output bằng **Tiếng Việt**.

## Các bước

| Bước | Tên | Pipeline | Rule | Dừng? |
|---|---|---|---|---|
| 1 | Context & Scope | — | — | Có |
| 2 | Requirement Analysis & QnA | Requirement | `requirement-analysis.mdc` | **Có** |
| 3 | Decomposition & Risk | Requirement | `requirement-analysis.mdc` | Không |
| 4 | Test Viewpoint | Viewpoint | `test-viewpoint.mdc` | **Có** |
| 5 | Test Case Generation | Test Case | `test-case-generation.mdc` | Hỏi khi chia module/part |
| 6 | Coverage & Delivery | Coverage | `viewpoint-coverage.mdc` | Giao kết quả |

### Bước 1: Context & Scope

1. Hỏi user nếu còn thiếu: tên dự án / tính năng, mô tả hệ thống, mục tiêu kiểm thử, tài liệu (requirement, Basic Design, Figma, PDF...).
2. Đọc tài liệu, tóm tắt module, actor, in-scope, out-of-scope.
3. **Chờ user xác nhận scope.**

### Bước 2: Requirement Analysis & QnA

1. Tách rule thành `REQ-01`, `AC-01`... (hoặc dùng ID có sẵn trong tài liệu).
2. Liệt kê field spec nếu có form/API: tên, type, required, min/max, format, unique.
3. Phân luồng Happy / Alternate / Exception. Trích business rule, phân quyền, dependency.
4. Ambiguity → QnA đánh số `Q1`, `Q2`... Mỗi câu ghi REQ liên quan và assumption sẽ dùng nếu không được trả lời.
5. **DỪNG — chờ user trả lời QnA.**

Output theo khung của `requirement-analysis.mdc`: Overview, Actors & Preconditions, Functional Breakdown (REQ/AC), Field Specifications, Flows, Business Rules & Dependencies, Ambiguities & QnA, Assumptions.

### Bước 3: Decomposition & Risk

1. Chia module / sub-module theo UI hoặc theo luồng, ghi dependency.
2. Map module với REQ/AC. REQ chưa thuộc module nào → báo gap.
3. Gắn Risk sơ bộ cho module: High (tiền, bảo mật, mất/sai dữ liệu, luồng chính) / Medium / Low.

Output: bảng `Module | REQ/AC | Dependency | Risk`.

### Bước 4: Test Viewpoint

1. Với mỗi module, xét đủ 12 nhóm catalog: Functional, Input / Field, Boundary, Combination, State, Authorization, Error Handling, Data Integrity, Security, Resilience, UI / A11y, Integration. Nhóm bị loại phải ghi lý do.
2. Mỗi REQ/AC có ≥ 1 viewpoint.
3. Mỗi input field: ≥ 1 VP positive + ≥ 2 VP negative/boundary, field nào VP riêng field đó. Dùng bảng **Field-Level Validation** trong skill để chọn góc nhìn theo loại field.
4. Một VP = một concern quan sát được, không chứa steps hay data. Gắn `Technique` và `Risk`.
5. **DỪNG — chờ user duyệt bảng viewpoint** (thêm, bỏ, đổi Risk).

Output:

| VP ID | Category | Viewpoint | Source | Technique | Risk |
|---|---|---|---|---|---|

VP ID: `VP_[MODULE]_[NNN]`. Kèm danh sách nhóm catalog bị loại và lý do.

### Bước 5: Test Case Generation

1. Sinh TC **từ viewpoint đã duyệt**.
2. 1 TC → đúng 1 VP chính. 1 VP → 1..n TC; VP High ≥ 2 TC (happy + negative/boundary).
3. Kỹ thuật theo `Technique` của VP: EP, BVA (min, min-1, max, max+1, empty/zero), Decision Table (mỗi rule 1 TC), State Transition (chuyển hợp lệ + bị cấm).
4. Test data cụ thể. Steps ghi rõ nhập gì, vào đâu, bấm gì. Không gộp validation nhiều field vào 1 TC.
5. Gặp concern chưa có VP → không tự thêm khi đang viết TC. Quay lại Bước 4 bổ sung và báo user.
6. Nhiều module → sinh từng module, hỏi user trước khi sang module tiếp.

Output:

| TC ID | Module | VP ID | Risk Level | Test Scenario | Pre-Condition | Test Steps | Expected Result | Test Data | Priority | Automatable | Auto Type | Tags |
|---|---|---|---|---|---|---|---|---|---|---|---|---|

- TC ID `[DỰ_ÁN]_[MODULE]_TC_[NNN]`, hoặc theo quy ước dự án.
- Steps và Expected đánh số khớp 1-1, xuống dòng trong cell bằng `<br>`.
- `Automatable` `Yes`/`No`/`Partial`; `Auto Type` `UI`/`API`/`Unit`/`N/A`; `Tags` `@Smoke` `@Regression` `@CriticalPath` `@Security` `@Boundary`.

### Bước 6: Coverage & Delivery

1. Lập ma trận coverage:

| VP ID | Category | Viewpoint | Risk | TC IDs | # TCs | Status |
|---|---|---|---|---|---|---|

   Status: `✅ Covered` / `⚠️ Weak` / `❌ GAP` / `⚠ Orphan` theo `viewpoint-coverage.mdc`. `Coverage % = VP Covered / tổng VP`.
2. Gap analysis: liệt kê VP GAP và Weak kèm TC đề xuất bổ sung; sửa hoặc xóa TC orphan; REQ/AC chưa có VP → quay lại Bước 4.
3. Chạy **Self-Quality Gate + Definition of Done** trong skill (3 phần Viewpoint, Test case, Coverage). Còn ô chưa đạt thì sửa, không giao.
4. Giao kết quả. Xuất file khi user yêu cầu: `test_cases_<module>.md`, CSV cho Jira Xray / TestRail (cùng cột bảng TC), hoặc Google Sheets nếu project có script tích hợp (ví dụ `scripts/integrations/google_sheet/sheet_writer.js`).

## Output cuối

1. Phân tích requirement, QnA đã giải quyết và Assumption còn lại
2. Bảng module + Risk
3. Bảng viewpoint đã duyệt
4. Bảng test case
5. Ma trận coverage + gap analysis, báo theo viewpoint (ví dụ: "12/12 VP High-Medium covered; 1 VP Low accepted gap — lý do ...")
6. Kết quả Self-Quality Gate
