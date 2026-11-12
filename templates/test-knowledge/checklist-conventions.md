# Checklist / Test Case Conventions — <Tên dự án>

Quy ước áp dụng cho mọi checklist / test case trong `<thư mục test case>`.

## Đặt tên file

```
<SCREEN_ID|MODULE>_<slug>_checklist.csv     # bản import / nguồn dữ liệu chính
<SCREEN_ID|MODULE>_<slug>_checklist.md      # bản đọc / review
<SCREEN_ID|MODULE>_<slug>_viewpoints.csv    # (tuỳ chọn) danh sách VP
<SCREEN_ID|MODULE>_<slug>_qna.csv           # (tuỳ chọn) QnA riêng
<SCREEN_ID|MODULE>_<slug>_defects.csv       # (tuỳ chọn) defect list
```

## Cột

| Cột | Nội dung |
| --- | --- |
| ID | `<SCREEN_ID>_CL<nnn>` hoặc `[DỰ_ÁN]_[MODULE]_TC_[NNN]` |
| Screen / Module | |
| Target | Item spec kèm số tham chiếu (`<field> (No.x)`) |
| VP ID | Mã + loại VP |
| Checklist / Test Steps | Checklist: kiểm **CÁI GÌ**; TC: các bước cụ thể |
| Expected Result | Cụ thể, message trích nguyên văn spec |
| Actual Result | Điền khi execute |
| Status | `PASS` / `FAIL` / `UNCHECK` |
| Evidence | Đường dẫn screenshot / response |
| Note | QnA ID, Bug ID, lý do UNCHECK |

`<Thêm / bớt cột theo template thực tế của dự án — không tự đổi thứ tự cột khi đã có template.>`

## Encoding

CSV ghi **UTF-8 có BOM** để Excel hiển thị đúng tiếng Việt / tiếng Nhật:

```powershell
$t = [IO.File]::ReadAllText($p)
[IO.File]::WriteAllText($p, $t, [Text.UTF8Encoding]::new($true))
```

## Trạng thái

| Trạng thái | Khi nào |
| --- | --- |
| `UNCHECK` | Chưa chạy, hoặc cố ý không chạy vòng này (ghi `UNCHECK cố ý — <lý do>`) |
| `PASS` | Đã chạy, có evidence, khớp expected |
| `FAIL` | Đã chạy, có evidence, lệch expected có nguồn spec, kèm bước tái hiện |

Kết quả tổng thể: có FAIL → `FAIL`; không FAIL mà còn UNCHECK → `UNCHECK`; tất cả PASS → `PASS`.

## Nguyên tắc nội dung riêng của dự án

- `<ví dụ: dòng tô xám trong BD = out of scope>`
- `<ví dụ: message expected trích nguyên văn tiếng Nhật, đủ dấu 。>`
- `<ví dụ: typo đã biết trong tài liệu — không FAIL app>`
