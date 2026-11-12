---
description: Thực thi manual test case / checklist trên trình duyệt bằng browser tool — chuẩn bị, chạy từng TC, thu evidence, ghi PASS/FAIL/UNCHECK, tổng hợp report.
rules:
  - qa-safety.mdc
  - bug-report.mdc
---

> **BẮT BUỘC:** Tuân theo `.cursor/rules/qa-safety.mdc`. FAIL ghi theo `.cursor/rules/bug-report.mdc`. Nếu project có `test-knowledge/`: chuẩn bị theo `environment-and-accounts.md`, ghi kết quả theo `checklist-conventions.md`, tra `known-issues-and-lessons.md` trước khi kết luận FAIL.

# Workflow: Execute Test Cases trên Browser

```
Chuẩn bị → [STOP] → Chạy từng TC → Evidence → Ghi kết quả → Report → Cập nhật knowledge
```

## Input

| Input | Bắt buộc |
|---|---|
| File test case / checklist (có ID, Steps/Checklist, Expected, Status) | ✅ |
| URL môi trường test | ✅ |
| Account theo role (password do user cung cấp trong phiên hoặc qua env) | ✅ |
| Phạm vi chạy (toàn bộ / theo module / theo tag / chỉ UNCHECK) | ⭕ |

## Bước 1: Chuẩn bị

1. Đọc file TC, lọc theo phạm vi. Mặc định chỉ chạy item `UNCHECK`.
2. Liệt kê điều kiện cần: account theo role, test data, config/switch, thứ tự phụ thuộc.
3. Đánh dấu trước các item **không thể chạy** (thiếu account portal khác, thiếu spec, thiếu data) → `UNCHECK cố ý — <lý do>`.
4. Xác định item có thao tác không hoàn tác được (xóa, gửi mail/thông báo thật) → cần user cho phép.

**DỪNG — trình bày:** số item sẽ chạy, item UNCHECK cố ý + lý do, item cần xin phép, account/data còn thiếu. Chờ user xác nhận.

## Bước 2: Chạy từng TC

Với mỗi TC:

1. `browser_navigate` tới màn hình; `browser_snapshot` để lấy ref phần tử.
2. Thực hiện đúng Steps bằng `browser_click` / `browser_fill` / `browser_type` / `browser_select_option`. Ưu tiên thao tác thật thay vì set value bằng JS (component custom như Radix/MUI Select thường không nhận event giả).
3. So sánh với Expected:
   - Message tự biến mất (toast) → cài `MutationObserver` qua `browser_cdp` `Runtime.evaluate` **trước** khi submit để bắt text.
   - Cần biết server trả gì → intercept XHR/fetch hoặc bật CDP Network trước thao tác.
   - Field `type=email`/`required` → phân biệt message validation của trình duyệt với message của app.
4. Thu evidence: `browser_take_screenshot` tại điểm kiểm tra; lưu response khi liên quan.
5. Ghi kết quả ngay sau mỗi TC (không dồn cuối).

Không dùng một lần thất bại do môi trường (timeout, 502, mất session) để kết luận FAIL — thử lại 1 lần, vẫn lỗi → `UNCHECK — môi trường` và báo user.

## Bước 3: Ghi kết quả

| Status | Điều kiện |
|---|---|
| `PASS` | Đã chạy, có evidence, khớp Expected |
| `FAIL` | Đã chạy, có evidence, lệch Expected có nguồn spec, **có bước tái hiện** |
| `UNCHECK` | Chưa chạy / không chạy được — ghi lý do |

- Lệch Expected nhưng spec không định nghĩa → **không FAIL**; ghi hành vi thực tế + tạo QnA.
- FAIL → draft bug theo `bug-report.mdc` (hoặc chạy `/report_bug`).
- Cập nhật cột `Actual Result`, `Status`, `Evidence`, `Note` trong file TC; giữ nguyên các cột khác. CSV ghi lại bằng UTF-8 có BOM.

## Bước 4: Report

```markdown
## Test Execution Report — <Module> — <YYYY-MM-DD>

Môi trường: STG, Chrome 131 | Tester: AI (Cursor) theo yêu cầu của <user>

| Status | Count |
|---|---:|
| PASS | xx |
| FAIL | xx |
| UNCHECK | xx |
| TOTAL | xx |

Overall: FAIL nếu có FAIL; UNCHECK nếu không FAIL nhưng còn UNCHECK; PASS nếu tất cả PASS.

### FAIL
| ID | Tóm tắt | Bug ID | Severity |

### UNCHECK
| ID | Lý do | Cần gì để chạy |

### QnA phát sinh
### Rủi ro còn lại
```

## Bước 5: Cập nhật knowledge

- Bài học kỹ thuật mới khi execute → `known-issues-and-lessons.md`.
- QnA mới → `open-questions.md`.
- Xóa file tạm do workflow tạo; giữ evidence, report, file TC.
