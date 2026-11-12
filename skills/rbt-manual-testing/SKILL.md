---
name: rbt-manual-testing
description: Skill sinh manual test case theo pipeline Requirement → Test Viewpoint → Test Case → Viewpoint Coverage, với 2 mode — QUICK (một lượt, vẫn in viewpoint trước) và FULL RBT (6 bước tuần tự, có checkpoint với user). Master skill cho mọi tác vụ manual test case.
---

# RBT Manual Testing

## Description

Master skill cho mọi tác vụ sinh manual test case. Skill này thực thi pipeline bắt buộc của `tester-general.mdc`:

```
Requirement → Test Viewpoint → Test Case → Viewpoint Coverage
```

| Bước pipeline | Rule nguồn | Output |
|---|---|---|
| Phân tích requirement | `.cursor/rules/requirement-analysis.mdc` | REQ/AC có ID, field spec, luồng, QnA, assumption |
| Sinh viewpoint | `.cursor/rules/test-viewpoint.mdc` | Bảng viewpoint `VP_[MODULE]_[NNN]` |
| Sinh test case | `.cursor/rules/test-case-generation.mdc` | Bảng TC, mỗi TC gắn đúng 1 `VP ID` chính |
| Kiểm tra coverage | `.cursor/rules/viewpoint-coverage.mdc` | Ma trận coverage, gap analysis, Definition of Done |

Khi skill và rule khác nhau, **rule thắng**. Đọc các rule trên trước khi chạy nếu chưa có trong context.

Hai mode khác nhau ở **số lần dừng chờ user**, không khác ở pipeline. Cả hai mode đều phải có bảng viewpoint và ma trận coverage.

| Mode | Khi nào dùng | Checkpoint với user |
|---|---|---|
| **QUICK** | 1 module / 1 tính năng, requirement rõ | Không dừng. Vẫn in bảng viewpoint trước bảng TC |
| **FULL RBT** | Nhiều module, nghiệp vụ phức tạp, requirement mơ hồ | Dừng ở QnA (Bước 2) và ở bảng viewpoint (Bước 4) |

Nguyên tắc:

- **Human Strategy:** user chốt scope, risk và câu trả lời QnA.
- **AI Execution:** AI phân tích, sinh viewpoint, sinh TC, tự rà coverage.
- **Human Verification:** user duyệt viewpoint (FULL RBT) và bộ TC trước khi chốt.

---

## When to Use

Dùng skill này khi:

- Sinh manual test case từ requirement, user story, Basic Design, spec
- Phân tích requirement để tìm ambiguity trước khi test
- Lập danh sách test viewpoint cho một module
- Kiểm tra coverage của bộ test case có sẵn theo viewpoint
- Áp dụng Risk-Based Testing (gắn Risk cho viewpoint và TC)

**Không** dùng skill này khi:

- Chỉ sinh test data → skill `test-data-generator` (workflow `/generate_test_data`)
- Chạy TC trên browser → workflow `/execute_testcases_browser`
- Chọn bộ regression từ thay đổi → skill `regression-impact-analyzer`
- Sinh checklist từ Basic Design → workflow `/qa_checklist_from_basic_design` + rule `checklist-generation.mdc`
- Sinh test case tích hợp cross-site/cross-screen → workflow `/generate_integration_testcases`
- Sinh API test / Postman collection → workflow `/generate_api_testcases_postman`

---

## Mode Routing

### → QUICK

- User dùng workflow `/generate_testcases_from_requirements`
- User nói: "sinh test case nhanh", "tạo TC từ requirement này", "viết TC cho form..."
- Requirement rõ, scope 1 module / 1 tính năng
- User không yêu cầu QnA hay duyệt từng bước

### → FULL RBT

- User dùng workflow `/generate_manual_testcases_rbt`
- User nói: "quy trình 6 bước", "phân tích RBT", "sinh bộ TC bài bản"
- Scope lớn (> 3 module), logic nhiều điều kiện chồng chéo
- Requirement mơ hồ, cần QnA

### → Chuyển từ QUICK sang FULL RBT

Đang chạy QUICK mà gặp một trong các điều kiện sau thì dừng, báo user và đề xuất chuyển sang FULL RBT:

- Có ambiguity ảnh hưởng tới expected result (không thể ghi assumption an toàn)
- Scope thực tế > 3 module
- User yêu cầu QnA, risk assessment hoặc duyệt viewpoint

### → Không rõ mode

Hỏi user bằng `AskQuestion`, hai lựa chọn: QUICK (sinh một lượt, không dừng giữa chừng) hoặc FULL RBT (6 bước, dừng ở QnA và viewpoint).

---

# Mode 1: QUICK

## Mục đích

Sinh bộ TC đủ chất lượng trong một lượt cho requirement rõ ràng. **Không được bỏ bước viewpoint** — QUICK chỉ bỏ các lần dừng chờ user.

## Quy trình (một lượt, theo đúng thứ tự)

1. **Phân tích requirement** (`requirement-analysis.mdc`, bản rút gọn):
   - Tách rule thành `REQ-01`, `REQ-02`... hoặc dùng ID có sẵn trong tài liệu
   - Liệt kê field spec nếu có form/API: tên, type, required, min/max, format, unique
   - Phân luồng Happy / Alternate / Exception
   - Ambiguity nhỏ → ghi **Assumption** gắn với REQ cụ thể. Ambiguity lớn → chuyển FULL RBT
2. **In bảng viewpoint** (`test-viewpoint.mdc`):
   - Xét đủ 12 nhóm catalog; nhóm không liên quan ghi lý do loại
   - Mỗi input field: ≥ 1 viewpoint positive + ≥ 2 viewpoint negative/boundary (tham chiếu mục "Field-Level Validation" bên dưới)
   - Gắn `Technique` và `Risk` cho từng viewpoint
3. **Sinh TC từ viewpoint** (`test-case-generation.mdc`):
   - Mỗi TC gắn đúng 1 `VP ID` chính
   - Viewpoint High: ≥ 2 TC (happy + negative/boundary)
   - Thiếu viewpoint khi đang viết TC → quay lại bước 2 thêm VP, không viết TC orphan
4. **Ma trận coverage + gap analysis** (`viewpoint-coverage.mdc`)
5. **Self-Quality Gate**, rồi mới giao

## Output QUICK

Theo thứ tự: Assumption (nếu có) → Bảng viewpoint → Bảng TC → Ma trận coverage → Kết quả Self-Quality Gate.

---

# Mode 2: FULL RBT — 6 bước tuần tự

## Mục đích

Quy trình đầy đủ cho module phức tạp. Mỗi bước hoàn thành rồi mới sang bước sau. **Không gộp bước.**

| Bước | Tên | Pipeline | Dừng chờ user? |
|---|---|---|---|
| 1 | Context & Scope | — | Có, xác nhận scope |
| 2 | Requirement Analysis & QnA | Requirement | **Có, bắt buộc** |
| 3 | Decomposition & Risk | Requirement | Không |
| 4 | Test Viewpoint | Viewpoint | **Có, bắt buộc** |
| 5 | Test Case Generation | Test Case | Hỏi nếu phải chia part |
| 6 | Coverage & Delivery | Coverage | Giao kết quả |
### Bước 1: Context & Scope

1. Yêu cầu user cung cấp nếu chưa có: tên dự án / tính năng, mô tả hệ thống, mục tiêu kiểm thử, tài liệu (requirement, Basic Design, Figma, PDF...)
2. Đọc tài liệu, tóm tắt scope: module, actor, in-scope, out-of-scope
3. **Chờ user xác nhận scope**

### Bước 2: Requirement Analysis & QnA

Làm theo `requirement-analysis.mdc`. Output tối thiểu:

```markdown
## Overview
## Actors & Preconditions
## Functional Breakdown (REQ/AC)
## Field Specifications
## Flows (Happy / Alternate / Exception)
## Business Rules & Dependencies
## Ambiguities & QnA
## Assumptions
```

- QnA đánh số `Q1`, `Q2`... Mỗi câu ghi REQ liên quan, ngữ cảnh, và assumption sẽ dùng nếu không được trả lời
- Không bịa validation, timeout, phân quyền, message lỗi khi tài liệu không nói

> **DỪNG — chờ user trả lời QnA.** Câu nào không được trả lời thì dùng assumption đã ghi và đánh dấu rõ trong output các bước sau.

### Bước 3: Decomposition & Risk

1. Chia tính năng thành module / sub-module, theo UI (header, bảng, form popup...) hoặc theo luồng (tạo, sửa, xóa...)
2. Ghi dependency giữa các module
3. Map mỗi module với REQ/AC ở Bước 2. REQ nào chưa thuộc module nào → báo gap
4. Gắn Risk sơ bộ cho từng module:
   - **High:** tiền, bảo mật, mất/sai dữ liệu, luồng chính của khách hàng
   - **Medium:** sai chức năng nhưng có đường vòng
   - **Low:** hiển thị, tiện ích phụ

Output: bảng `Module | REQ/AC | Dependency | Risk`.

### Bước 4: Test Viewpoint

Làm theo `test-viewpoint.mdc`. Đây là bước thay cho "High-Level Test Scenarios" của quy trình cũ.

1. Với mỗi module, xét đủ 12 nhóm catalog: Functional, Input / Field, Boundary, Combination, State, Authorization, Error Handling, Data Integrity, Security, Resilience, UI / A11y, Integration. Nhóm bỏ qua phải ghi lý do
2. Mỗi REQ/AC có ≥ 1 viewpoint
3. Mỗi input field: ≥ 1 positive + ≥ 2 negative/boundary viewpoint, mỗi field VP riêng
4. Một viewpoint = một concern, quan sát được, không chứa steps hay data
5. Gắn `Technique` (EP / BVA / Decision Table / State Transition / Error Guessing) và `Risk` (High / Medium / Low)

Output bắt buộc:

| VP ID | Category | Viewpoint | Source | Technique | Risk |
|---|---|---|---|---|---|
| VP_LOGIN_001 | Functional | Đăng nhập thành công với credential hợp lệ | REQ-01 | EP | High |
| VP_LOGIN_002 | Input / Field | Email sai định dạng bị từ chối | REQ-02 | EP | High |

Kèm danh sách nhóm catalog bị loại và lý do.

> **DỪNG — chờ user duyệt bảng viewpoint.** User có thể thêm, bỏ, đổi Risk. Chưa duyệt thì chưa sinh TC.

### Bước 5: Test Case Generation

Làm theo `test-case-generation.mdc`.

1. Sinh TC **từ viewpoint đã duyệt**, không từ requirement trực tiếp
2. Mapping:
   - 1 viewpoint → 1..n TC. Viewpoint High: ≥ 2 TC (happy + negative/boundary)
   - 1 TC → đúng 1 viewpoint chính (VP phụ ghi thêm nếu cần)
   - Không gộp validation nhiều field vào 1 TC
3. Chọn kỹ thuật theo `Technique` của viewpoint:
   - **EP:** 1 đại diện cho mỗi lớp hợp lệ / không hợp lệ
   - **BVA:** min, min-1, max, max+1, kèm empty/zero nếu liên quan
   - **Decision Table:** mỗi rule (cột) → 1 TC
   - **State Transition:** mỗi chuyển hợp lệ + mỗi chuyển bị cấm
4. Test data cụ thể (xem mục "Quy tắc test data")
5. Đang viết TC mà phát hiện concern chưa có viewpoint → **không tự thêm VP trong lúc viết TC**. Ghi lại, quay về Bước 4 bổ sung và báo user
6. Nhiều module → sinh từng module, hỏi user trước khi sang module tiếp

### Bước 6: Coverage & Delivery

1. Lập ma trận coverage theo `viewpoint-coverage.mdc` (mẫu ở mục "Output Format")
2. Gap analysis:
   - Liệt kê VP `❌ GAP` và `⚠️ Weak`, đề xuất TC bổ sung
   - Liệt kê TC orphan, sửa `VP ID` hoặc xóa
   - Cross-check: REQ/AC nào chưa có viewpoint → quay lại Bước 4
3. Chạy Self-Quality Gate
4. Không đạt Definition of Done → không giao, sửa trước. Gap Low risk được giữ nếu có lý do
5. Xuất file nếu user yêu cầu (`test_cases_<module>.md` hoặc CSV)

---

## Field-Level Validation (nguồn cho viewpoint Input / Field)

Dùng bảng này khi lập viewpoint nhóm **Input / Field** và **Boundary** ở QUICK bước 2 hoặc FULL RBT Bước 4. Mỗi field có checklist riêng theo loại; không dùng chung một bộ cho mọi field. Chỉ lấy dòng áp dụng được cho requirement; giới hạn (max length, format...) phải lấy từ tài liệu hoặc assumption đã ghi, không tự đặt.

| Loại field | Validation cần xét |
|---|---|
| **Text (Name, Address...)** | Required/Optional · Min/Max length · Chỉ khoảng trắng · Ký tự đặc biệt (`<>&"'`) · XSS (`<script>alert(1)</script>`) · SQL injection (`' OR 1=1--`) · Unicode/Emoji · Khoảng trắng đầu/cuối |
| **Email** | Format đúng (`user@domain.com`) · Thiếu `@` · Thiếu domain · Domain sai · Nhiều `@` · Ký tự đặc biệt trước `@` · Max length · Hoa/thường · Đã tồn tại (nếu unique) |
| **Phone** | Chỉ số · Prefix (`+84`, `0`) · Min/Max length · Lẫn chữ · Dấu `-`, `.`, khoảng trắng · Mã vùng sai |
| **Date / DateTime** | Format · Ngày không tồn tại (`31/02`) · Năm nhuận (`29/02/2024`) · Quá khứ / tương lai · Min/max date · Timezone |
| **Number / Currency** | Min/Max · Số âm · 0 · Thập phân · Ký tự không phải số · Overflow · Số 0 đầu · Định dạng tiền |
| **Dropdown / Select** | Giá trị mặc định · Mọi option hợp lệ · Option disabled · Đổi lựa chọn · Required |
| **Checkbox / Radio** | Trạng thái mặc định · Check/Uncheck · Required · Nhóm radio chỉ chọn 1 |
| **File Upload** | Type hợp lệ / không hợp lệ · Max size · File 0 KB · Tên file ký tự đặc biệt · Nhiều file · Kéo thả vs nút chọn |
| **Password** | Min/Max length · Ký tự đặc biệt · Hoa/thường · Số · Chặn copy-paste? · Hiện/ẩn · Confirm khớp / không khớp |
| **Textarea** | Max length · Xuống dòng · HTML tag · Bộ đếm ký tự |
| **OTP / MFA** | Auto-focus ô tiếp · Paste chuỗi OTP · Hết hạn · Vượt số lần sai (lockout) · Giới hạn gửi lại |
| **Date Range / Time Picker** | Ngày kết thúc < ngày bắt đầu · Trùng khung giờ · Giới hạn độ dài khoảng · Chặn quá khứ / tương lai |
| **Rich Text Editor** | Lọc tag nguy hiểm (`<script>`, `<iframe>`) · Paste kèm format/ảnh · Bộ đếm theo text thô vs HTML |
| **Multi-Select / Tag Input** | Giới hạn số tag · Tag trùng · Xóa bằng Backspace / nút X · Ký tự đặc biệt |
| **Range Slider / Stepper** | Min/max · Sai bước nhảy · Nhập tay vs kéo |

## Kịch bản nâng cao theo nhóm catalog

Khi xét catalog, các nhóm sau hay bị bỏ sót. Chỉ tạo viewpoint khi tính năng có hành vi tương ứng; không áp dụng thì ghi lý do loại nhóm.

| Nhóm catalog | Góc nhìn gợi ý |
|---|---|
| **Resilience** | Double click Save/Submit không tạo 2 bản ghi · Session/token hết hạn khi đang điền form · Mất mạng khi submit, timeout, retry |
| **Data Integrity** | Hai tab/user cùng sửa một bản ghi · Xóa bản ghi đang được tham chiếu |
| **Input / Field** | Tiếng Việt có dấu · Emoji (`😀🎉🚀`) · Tiếng Nhật, Trung, RTL |
| **UI / A11y** | Thứ tự `Tab` hợp lý · Kích hoạt bằng `Enter`/`Space` · Viền focus rõ · Trạng thái disabled |
| **Error Handling** (API) | `200/201`, `400`, `401`, `403`, `404`, `409`, `422`, `429`, `500/503` — chỉ assert mã mà tài liệu định nghĩa |
| **Authorization** | Chưa login · Sai role · IDOR (đổi ID trên URL/API) · Thao tác vượt quyền |

---

## Quy tắc test data

Áp dụng cho cả hai mode.

```
❌ SAI: "Nhập email hợp lệ"
✅ ĐÚNG: "Nhập email: test_khachhang_01@domain.com"

❌ SAI: "Nhập mã số hợp lệ"
✅ ĐÚNG: "Nhập mã: KH-2026-0012"

❌ SAI: "Nhập quá maxlength"
✅ ĐÚNG: "Nhập 256 ký tự 'A' vào Name (max: 255)"
```

Steps phải ghi rõ nhập gì, vào đâu, bấm nút nào. Không viết "nhập dữ liệu", "thao tác bình thường".

---

## Output Format

### 1. Bảng viewpoint

| VP ID | Category | Viewpoint | Source | Technique | Risk |
|---|---|---|---|---|---|

- VP ID: `VP_[MODULE]_[NNN]`
- Kèm danh sách nhóm catalog bị loại và lý do

### 2. Bảng test case

| TC ID | Module | VP ID | Risk Level | Test Scenario | Pre-Condition | Test Steps | Expected Result | Test Data | Priority | Automatable | Auto Type | Tags |
|---|---|---|---|---|---|---|---|---|---|---|---|---|

- TC ID: `[DỰ_ÁN]_[MODULE]_TC_[NNN]`, ví dụ `CRM_LOGIN_TC_001`. Dùng quy ước của dự án nếu user đã có
- `Risk Level` lấy theo viewpoint
- Test Steps và Expected Result đánh số khớp 1-1; trong cell Markdown xuống dòng bằng `<br>`
- `Automatable`: `Yes` / `No` / `Partial`. `Auto Type`: `UI` / `API` / `Unit` / `N/A`
- `Tags`: `@Smoke` `@Regression` `@CriticalPath` `@Security` `@Boundary`
- Không rút gọn hay bỏ sót TC khi đưa vào bảng. Quá dài → chia Part 1, Part 2... và hỏi user

### 3. Ma trận coverage

| VP ID | Category | Viewpoint | Risk | TC IDs | # TCs | Status |
|---|---|---|---|---|---|---|

| Status | Điều kiện |
|---|---|
| ✅ Covered | ≥ 1 TC gắn đúng VP; High nên ≥ 2 TC (happy + negative/boundary) |
| ⚠️ Weak | Có TC nhưng chỉ happy path, data mơ hồ, hoặc thiếu BVA/EP |
| ❌ GAP | 0 TC |
| ⚠ Orphan | TC không gắn VP, hoặc VP ID không tồn tại |

```
Coverage % = số VP Covered / tổng số VP
```

Báo coverage theo viewpoint, không theo số TC:

```
❌ "Đã có 40 test cases, coverage đủ"
✅ "12/12 VP High-Medium covered; 1 VP Low (A11y Tab order) accepted gap — PO xác nhận"
```

### 4. Export

Khi user yêu cầu:

- CSV cho Jira Xray / TestRail, cùng cột với bảng TC
- Google Sheets nếu project có script tích hợp (ví dụ `scripts/integrations/google_sheet/sheet_writer.js`)

Mọi output bằng **Tiếng Việt**, format Markdown.

---

## Self-Quality Gate + Definition of Done

Chạy trước khi giao, ở cả hai mode. Một ô chưa tick → sửa, không giao.

**Viewpoint**

- [ ] Mọi REQ/AC có ≥ 1 viewpoint
- [ ] Đã xét đủ 12 nhóm catalog; nhóm bị loại có lý do
- [ ] Mỗi input field có ≥ 1 VP positive và ≥ 2 VP negative/boundary
- [ ] Mỗi VP một concern, không chứa steps/data, có `Technique` và `Risk`

**Test case**

- [ ] Mọi TC có `VP ID` tồn tại trong bảng viewpoint
- [ ] TC ID unique, đúng format
- [ ] Steps ↔ Expected khớp 1-1
- [ ] 100% test data cụ thể
- [ ] Không gộp nhiều field / validation vào 1 TC
- [ ] Đủ `Automatable`, `Auto Type`, `Tags`

**Coverage**

- [ ] Mọi VP High/Medium = ✅ Covered
- [ ] Không còn TC orphan
- [ ] Gap Low risk (nếu có) có lý do
- [ ] Báo coverage theo viewpoint, không đếm số TC

---

## Anti-Patterns (cấm ở cả hai mode)

**Pipeline**

- ❌ Sinh TC thẳng từ requirement khi chưa có bảng viewpoint (kể cả QUICK)
- ❌ Gộp nhiều bước FULL RBT vào một lượt; bỏ qua checkpoint QnA hoặc duyệt viewpoint
- ❌ Tự thêm viewpoint trong lúc viết TC thay vì quay lại bước viewpoint
- ❌ Giao bộ TC khi chưa có ma trận coverage

**Viewpoint**

- ❌ Viết test case giả danh viewpoint (có steps/data trong VP)
- ❌ Copy requirement thành viewpoint ("Hệ thống cho phép đăng nhập")
- ❌ Chỉ có viewpoint happy path, bỏ catalog
- ❌ Một viewpoint cho cả form nhiều field
- ❌ Viewpoint không truy vết được về REQ/AC

**Test case**

- ❌ Test data placeholder ("hợp lệ", "dữ liệu đúng")
- ❌ Steps mơ hồ ("nhập dữ liệu")
- ❌ Gộp validation nhiều field vào 1 TC
- ❌ Dùng chung một bộ validation cho mọi field (Email ≠ Phone ≠ Date ≠ Text)
- ❌ Bỏ security validation (XSS, SQL injection) cho text/textarea khi field nhận input tự do
- ❌ Rút gọn hoặc bỏ sót TC khi đưa vào bảng
- ❌ Sinh toàn bộ TC một lần cho hệ thống lớn (phải chia module)

**Nghiệp vụ**

- ❌ Tự đoán business logic, validation, message lỗi khi tài liệu không nói. Thiếu thông tin → QnA; không được trả lời → Assumption ghi rõ
