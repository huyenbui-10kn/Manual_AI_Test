---
description: Tạo bug report chuẩn từ một FAIL — tái hiện, đối chiếu spec, chống trùng, thu evidence, viết report, cập nhật known issues.
rules:
  - bug-report.mdc
  - qa-safety.mdc
---

> **BẮT BUỘC:** Đọc rule `.cursor/rules/bug-report.mdc` trước khi bắt đầu. Nếu project có `test-knowledge/`, đọc `known-issues-and-lessons.md` và `open-questions.md`.

# Workflow: Report Bug

```
FAIL / hiện tượng lạ → Tái hiện → Đối chiếu spec → Chống trùng → Evidence → Draft → [STOP] → Final → Cập nhật knowledge
```

## Input

| Input | Bắt buộc |
|---|---|
| Mô tả hiện tượng hoặc TC/Checklist ID bị FAIL | ✅ |
| Môi trường, URL, account/role | ✅ |
| Evidence có sẵn (screenshot, log, response) | ⭕ |
| Tài liệu spec liên quan | ⭕ (cần để có Expected) |

## Bước 1: Tái hiện

1. Thực hiện lại các bước (bằng browser tool nếu là UI, hoặc request nếu là API).
2. Ghi Frequency: `Always` / `Intermittent (x/y)` / `Once`.
3. Không tái hiện được → báo user, hỏi thêm điều kiện; chưa tạo bug.

## Bước 2: Đối chiếu spec

1. Tìm Expected trong REQ/AC, Basic Design, API DOC, QnA đã trả lời.
2. Không có nguồn → **không tạo bug**. Đề xuất QnA (ghi vào `open-questions.md`) và dừng.
3. Loại trừ nguyên nhân phía test: data, role, môi trường, cache, validation HTML5 của trình duyệt.

## Bước 3: Chống trùng

Tra known issues / defect list. Trùng → đề xuất cập nhật bug cũ (thêm evidence, môi trường) thay vì tạo mới.

## Bước 4: Thu evidence

- UI: `browser_take_screenshot` ngay tại thời điểm lỗi; toast/message tự biến mất → bắt bằng `MutationObserver` hoặc chụp ngay sau thao tác.
- API / phân biệt FE-BE: request + response (status, body) qua Network/CDP.
- Console error nếu có.
- Che token, password, PII trước khi lưu.

## Bước 5: Draft report

Theo đủ các trường trong `bug-report.mdc` mục 2. Đề xuất Severity; Priority ghi `Đề xuất: P2 — chờ PO`.

**DỪNG — trình bày draft** và chờ user xác nhận/chỉnh.

## Bước 6: Finalize

1. Xuất theo định dạng user yêu cầu: Markdown, dòng CSV (`<画面ID|MODULE>_defects.csv`, UTF-8 BOM), hoặc nội dung paste vào Jira/Backlog.
2. Cập nhật trạng thái TC/Checklist liên quan: `FAIL` + Bug ID + bước tái hiện.
3. Thêm dòng chỉ mục vào `known-issues-and-lessons.md`; nếu học được điều mới về cách test (ví dụ: message hiện bằng toast), thêm vào mục bài học.

## Output mẫu

```markdown
### BUG_STAFF_003 — [C004 スタッフ管理] Lưu staff có email nhưng bỏ trống mật khẩu → báo lỗi bắt buộc

| Trường | Nội dung |
|---|---|
| Environment | STG `https://app-dev.stg.example.com`, Chrome 131, Windows 11, role tenant |
| Pre-conditions | Đăng nhập Company admin (tenant), công ty còn slot staff |
| Steps | 1. Mở スタッフ管理 → 新規作成<br>2. Nhập 氏名 = `QA Staff 03`, メール = `qa_staff_03@example.test`, để trống パスワード<br>3. Bấm 保存 |
| Expected | Lưu thành công; BD No.19: パスワード không bắt buộc khi có email |
| Actual | Toast `パスワードは必須です`, không lưu |
| Evidence | `evidence/BUG_STAFF_003.png`, response 422 `{ "password": ["required"] }` |
| Severity | Major |
| Priority | Đề xuất P2 — chờ PO |
| Frequency | Always (3/3) |
| Related | C004_CL019, BD No.19 |
```
