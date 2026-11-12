# Test Knowledge — <Tên dự án>

Kiến thức về **cách test** hệ thống: test bằng account nào, theo quy ước nào, đã học được gì. Không chứa phát biểu nghiệp vụ (nghiệp vụ nằm ở tài liệu requirement / BD).

> Cách dùng template: copy folder này vào gốc project, đổi tên thành `test-knowledge/`, thay mọi `<...>` bằng thông tin dự án. Ví dụ đã điền đầy đủ: `test-knowledge/` (dự án Welvie) trong kit.

## Danh sách file

| File | Nội dung | Cập nhật khi |
| --- | --- | --- |
| `test-viewpoint-catalog.md` | Danh mục VP dùng chung cho dự án | Thêm loại VP mới / ví dụ mới |
| `checklist-conventions.md` | Quy ước tên file, cột, trạng thái, encoding | Đổi template / quy ước |
| `environment-and-accounts.md` | Môi trường, URL, account theo role (không password) | Có môi trường / account mới |
| `known-issues-and-lessons.md` | Defect đã xác minh + bài học kỹ thuật khi test | Sau mỗi lần execute / report |
| `open-questions.md` | QnA còn mở với BA/PM/dev | Phát sinh / được trả lời |

## Vị trí trong pipeline

```
Requirement → Test Viewpoint → Test Case → Viewpoint Coverage → Execute → Report
```

| Bước | Đọc | Ghi |
| --- | --- | --- |
| Requirement Review | `open-questions.md` | QnA mới → `open-questions.md` |
| Viewpoint | `test-viewpoint-catalog.md` | |
| Test Case / Checklist | `checklist-conventions.md` | |
| Execute | `environment-and-accounts.md`, `known-issues-and-lessons.md` | Bài học mới |
| Report / Bug | `known-issues-and-lessons.md` | Chỉ mục defect |

## Nơi lưu artifact

| Loại | Thư mục |
| --- | --- |
| Tài liệu requirement / BD gốc | `<đường dẫn>` |
| Test case / checklist / report | `<đường dẫn>` |
| Evidence | `<đường dẫn>` |

Thư mục này chỉ chứa kiến thức tái sử dụng được giữa các lần test, không chứa checklist hay report.
