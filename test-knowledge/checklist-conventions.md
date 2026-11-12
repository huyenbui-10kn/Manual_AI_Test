# Checklist Conventions

Quy ước áp dụng cho mọi checklist trong `test case/welvie/`.

## Đặt tên file

```
<画面ID>_<slug chức năng>_checklist.csv     # bản import được
<画面ID>_<slug chức năng>_checklist.md      # bản đọc / review
<画面ID>_<slug chức năng>_viewpoints.csv    # (tuỳ chọn) danh sách VP
<画面ID>_<slug chức năng>_qna.csv           # (tuỳ chọn) QnA riêng
<画面ID>_<slug chức năng>_defects.csv       # (tuỳ chọn) defect list
```

Màn nhiều tab thì tách theo tab rồi gộp: `C003_tab1_progress_checklist.csv` … + `C003_training_management_checklist.csv`.

Ví dụ đang có: `A001_admin_login_checklist`, `A003_houjin_checklist`, `A005_required_training_checklist`, `A008_webinar_checklist`, `A013_admin_users_permission_checklist`, `C001_company_login_checklist`, `C003_*`, `C007_staff_notices_checklist`.

## Cột CSV

| Cột | Nội dung |
| --- | --- |
| ID | `<画面ID>_CL<nnn>` — ví dụ `C007_CL017` |
| Vung man hinh | Khối trong màn (ví dụ: Danh sách / Modal tạo / Phân quyền) |
| Man hinh | `<画面ID> <画面名>` |
| Doi tuong kiem | Item BD kèm số: `タイトル (No.5)` |
| Goc nhin (VP) | Mã + loại VP: `VP_NOTI_011 Input` |
| Noi dung kiem | Kiểm **CÁI GÌ** — không phải thủ tục chi tiết |
| Trang thai | `PASS` / `FAIL` / `UNCHECK` |
| Ket qua mong doi | Expected cụ thể, message trích nguyên văn BD |
| Ket qua thuc te | Điền khi execute; FAIL phải có evidence |
| Ghi chu | Mã QnA liên quan, lý do UNCHECK, cảnh báo "không FAIL theo BD" |

FAIL cần thêm cột hoặc mục `Cach tai hien (khi FAIL)` — các bước tái hiện rõ ràng.

## Encoding

CSV luôn ghi **UTF-8 có BOM** để Excel không lỗi font tiếng Việt/tiếng Nhật:

```powershell
$t = [IO.File]::ReadAllText($p)
[IO.File]::WriteAllText($p, $t, [Text.UTF8Encoding]::new($true))
```

## Trạng thái

| Trạng thái | Khi nào dùng |
| --- | --- |
| `UNCHECK` | Chưa chạy, hoặc đã quyết định không chạy trong vòng này (UNCHECK cố ý) |
| `PASS` | Đã chạy, có evidence, khớp expected |
| `FAIL` | Đã chạy, có evidence, lệch expected, **kèm bước tái hiện** |

Kết quả tổng thể: có FAIL → `FAIL`; không FAIL mà còn UNCHECK → `UNCHECK`; tất cả PASS → `PASS`.

## UNCHECK cố ý

Dùng khi mục không thể verify trong phạm vi hiện tại. Bắt buộc ghi lý do ở cột Ghi chú theo mẫu `UNCHECK cố ý — <lý do>`. Các lý do đã gặp:

- **Cần account portal khác**: hành vi được định nghĩa ở portal User (popup, banner, số thông báo chưa xác nhận) hoặc cần System admin bật/tắt switch.
- **Thiếu định nghĩa spec**: không có expected (ví dụ Authority matrix không có dòng cho màn đó).
- **Thiếu test data**: cần dữ liệu đặc thù chưa có (staff chuyển事務所, dữ liệu nhiều năm).

Quy ước của Bui: các case "user đăng nhập và check thông báo" để `UNCHECK`, không cố suy luận PASS từ phía admin.

## Nguyên tắc nội dung

- Không bịa requirement, UI behavior hay validation rule. BD không nói → ghi nhận hành vi thực tế, **không FAIL**, và đưa vào `open-questions.md`.
- Test data phải cụ thể: "nhập 256 ký tự `A`", không viết "nhập dữ liệu quá dài".
- Message expected trích nguyên văn tiếng Nhật, đủ cả dấu `。`.
- Dòng BD tô xám / xám in đậm = out of scope → không sinh mục checklist.
- Item `対応不要` / `一旦保留` ở ô 画面ID = không sinh checklist cho cả màn.
- Bao phủ Happy / Negative / Boundary / Exception, không chỉ happy path.
- Typo trong BD (URL `dashboad`, `noties`, `infomations`; field `NoticeTittle`) không phải bug của app → ghi nhận vào QnA.

## Quy trình xuất

1. Sinh `.csv` (nguồn dữ liệu chính) → thêm BOM.
2. Sinh `.md` từ chính CSV đó: thêm bảng tổng hợp số lượng theo nhóm, bảng status, QnA còn mở, điều kiện để execute.
3. Xóa file tạm đã tạo trong lúc trích BD (`practices/_tmp_*`). Không xóa BD, checklist, report.
