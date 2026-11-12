# Test Knowledge

Kiến thức về **cách test** hệ thống. Trả lời câu hỏi "test thế nào, bằng account nào, theo quy ước nào, đã học được gì" — không chứa phát biểu nghiệp vụ (nghiệp vụ nằm ở `../business-knowledge/`).

## Danh sách file

| File | Nội dung |
| --- | --- |
| `test-viewpoint-catalog.md` | Danh mục góc nhìn test (VP) dùng chung cho mọi màn |
| `checklist-conventions.md` | Quy ước đặt tên, cột CSV, trạng thái, UNCHECK cố ý, cách ghi FAIL |
| `environment-and-accounts.md` | Môi trường, URL login, account theo role, quy ước bảo mật credential |
| `known-issues-and-lessons.md` | Defect đã phát hiện + bài học kỹ thuật khi test UI/API |
| `open-questions.md` | QnA còn mở theo từng màn |

## Liên kết với pipeline

Pipeline bắt buộc (theo `../rules/tester-general.mdc`):

```
Requirement → Test Viewpoint → Test Case → Viewpoint Coverage
```

Workflow QA cho Basic Design (theo `../workflows/qa_checklist_from_basic_design.md`):

```
BD → Requirement Review → Risk & Scope → [STOP chờ xác nhận]
   → Checklist → [STOP chờ review] → Execute → Report → Cleanup
```

Vị trí file này trong pipeline:

- Bước Requirement Review: đọc `../business-knowledge/` + ghi phát hiện mới vào `open-questions.md`.
- Bước Risk & Viewpoint: dùng `test-viewpoint-catalog.md`.
- Bước Checklist: tuân theo `checklist-conventions.md`.
- Bước Execute: chuẩn bị theo `environment-and-accounts.md`, tra `known-issues-and-lessons.md` trước khi kết luận FAIL.
- Bước Report: cập nhật lại `known-issues-and-lessons.md`.

## Nơi lưu artifact

Checklist và report thực tế **không** nằm ở đây mà ở `test case/welvie/` (đặt tên theo 画面ID). BD gốc nằm ở `basic design/`. Thư mục này chỉ chứa kiến thức tái sử dụng được giữa các lần test.
