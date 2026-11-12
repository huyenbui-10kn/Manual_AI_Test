# Test Viewpoint Catalog — <Tên dự án>

Danh mục góc nhìn test (VP) dùng chung. Mỗi TC/checklist item gắn đúng 1 VP chính. Catalog chuẩn 12 nhóm nằm ở rule `test-viewpoint.mdc`; file này ghi **cách áp dụng riêng cho dự án** và ví dụ thực tế.

Quy ước mã VP: `VP_<MODULE|VIẾT TẮT MÀN>_<NNN>` — ví dụ `VP_<MODULE>_001`.

## Danh mục

| Loại VP | Câu hỏi cốt lõi | Áp dụng khi | Ví dụ trong dự án |
| --- | --- | --- | --- |
| **Functional** | Chức năng chạy đúng mục đích spec? | Mọi action | |
| **Input / Field** | Required / type / format được kiểm đúng? | Field có ràng buộc | |
| **Boundary** | Biên và vượt biên xử lý thế nào? | max length / range / ngày | |
| **Combination** | Tổ hợp điều kiện cho kết quả đúng? | Spec có "nếu... thì..." | |
| **State** | Chuyển trạng thái hợp lệ / bị cấm đúng? | Có lifecycle / trạng thái | |
| **Authorization** | Role nào làm được gì; chưa login thì sao? | Có ma trận quyền | |
| **Error Handling** | Lỗi 4xx/5xx, timeout, message đúng? | Có gọi server | |
| **Data Integrity** | Dữ liệu lưu/hiển thị khớp thao tác? | Sau create/update/delete | |
| **Security** | Input độc hại bị vô hiệu hoá? | Field text tự do | |
| **Resilience** | Double click, reload, back, hết session? | Action ghi dữ liệu | |
| **UI / A11y** | Hiển thị, focus, Tab order, ngôn ngữ đúng? | Màn hình có UI | |
| **Integration** | Dữ liệu truyền đúng sang màn/portal/service khác? | Luồng cross-module | |

## Nguyên tắc riêng của dự án

- Nhóm rủi ro cao nhất của dự án: `<ví dụ: Authorization cross-tenant, Integration cross-portal>`
- Nhóm spec không định nghĩa rule (chỉ ghi nhận rủi ro, không FAIL): `<ví dụ: Security, Performance>`
- `<quy tắc khác>`
