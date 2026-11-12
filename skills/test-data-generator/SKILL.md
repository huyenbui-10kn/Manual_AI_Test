---
name: test-data-generator
description: Sinh test data có cấu trúc, unique, traceable theo 4 nhóm Positive / Negative / Boundary / Edge từ field spec, DOM, OpenAPI hoặc test case có sẵn; xuất JSON, CSV hoặc Markdown. Dùng khi cần data cho manual TC, checklist hoặc API payload (Postman).
---

# Test Data Generator

## When to Use

- Cần bộ data cho test case manual hoặc checklist
- Cần payload API hợp lệ / không hợp lệ theo schema
- Cần data boundary chính xác theo constraint (min/max, format)
- Workflow `/generate_test_data` gọi skill này

Không dùng khi: chỉ cần 1–2 giá trị minh họa trong TC (viết thẳng vào cột Test Data theo `test-case-generation.mdc`).

## 1. Xác định constraint — không đoán

Nguồn theo độ tin cậy:

| # | Nguồn | Ghi chú |
|---|---|---|
| 1 | User / requirement đã xác nhận | Cao nhất |
| 2 | API DOC / OpenAPI schema | `minLength`, `maxLength`, `pattern`, `enum`, `format`, `required` |
| 3 | DOM thực tế | `type`, `required`, `maxlength`, `pattern`, `min`, `max` — chỉ phản ánh FE, BE có thể khác |
| 4 | Test case có sẵn | Trích từ steps / expected |
| 5 | Convention domain | **Chỉ để đề xuất**, phải đánh dấu `Assumption` và chờ user xác nhận |

Ghi nguồn cho từng constraint. Constraint lấy từ DOM mà spec không nói → ghi `FE-only, cần xác nhận BE`.

Bảng field (chốt với user trước khi sinh data):

| # | Field | Type | Required | Constraints | Source | Dependency |
|---|---|---|---|---|---|---|
| 1 | email | string | ✅ | format email, unique, max 255 | REQ-02 | — |
| 2 | password_confirm | string | ✅ | = password | REQ-04 | password |

## 2. Kỹ thuật sinh data

| Nhóm | Kỹ thuật | Quy tắc |
|---|---|---|
| **Positive** | EP lớp hợp lệ | 1 đại diện mỗi lớp hợp lệ; một bộ "tất cả field hợp lệ" làm baseline |
| **Negative** | EP lớp không hợp lệ | Mỗi bộ chỉ **sai 1 field**, các field khác giữ giá trị baseline hợp lệ |
| **Boundary** | BVA | `min-1`, `min`, `max`, `max+1`; thêm `empty`, `0`, `-1` khi liên quan |
| **Edge** | Error guessing | Unicode/emoji, whitespace đầu/cuối, ký tự đặc biệt, rất dài, null, ngày đặc biệt |
| **Combination** | Pairwise / Decision Table | Khi nhiều field phụ thuộc nhau; ưu tiên tổ hợp có rủi ro |

Nguyên tắc "sai 1 field": nếu một bộ sai nhiều field, không xác định được field nào gây lỗi.

### Gợi ý giá trị theo loại

| Loại | Positive | Negative / Edge |
|---|---|---|
| Email | `qa_reg_01@example.test` | `qa_reg_01example.test`, `qa@`, `a@@b.test`, ` qa@example.test ` |
| Phone (VN) | `0901234567` | `090123456a`, `+84 90 123 4567` (nếu format không cho phép), độ dài min-1/max+1 |
| Text | `Nguyễn Văn Test` | chỉ khoảng trắng, `<script>alert(1)</script>`, `' OR 1=1--`, emoji `😀` |
| Number | giá trị giữa khoảng | `-1`, `0`, `1.5` (khi chỉ nhận số nguyên), `abc`, overflow |
| Date | `2026-11-12` | `2026-02-30`, `2024-02-29` (nhuận), `1970-01-01`, timezone biên ngày |
| File | file đúng loại, dưới max size | 0 KB, max+1 byte, sai extension, extension giả (`.jpg` chứa PDF) |

Payload bảo mật (XSS, SQLi) chỉ dùng trên môi trường được phép — xem `qa-safety.mdc`.

## 3. Unique & traceable

```
<prefix>_<testName|tcId>_<timestamp>_<random4>
auto_reg_TC004_1762930000_a8f2@example.test
```

- Data tạo bản ghi mới (email, username, code) **luôn** unique.
- Có thể truy ngược test nào sinh ra data qua prefix + TC ID.
- Khi cần chạy lại cùng bộ data ở vòng test sau: ghi rõ giá trị đã dùng vào file data/TC, không sinh lại ngẫu nhiên.

## 4. Mỗi bộ data phải có

| Thuộc tính | Bắt buộc |
|---|---|
| `id` | `POS_01`, `NEG_03`, `BND_02`, `EDGE_04` |
| `description` | Mục đích bộ data |
| `targetField` | Field đang test (Negative/Boundary/Edge) |
| `data` | Giá trị cụ thể |
| `expectedResult` | Kết quả mong đợi **có nguồn**; không có nguồn → `UNCLEAR — Qx` |
| `tcId` | TC liên quan nếu có |

## 5. Output

- **JSON** (mặc định): nhóm theo `positive / negative / boundary / edgeCases`.
- **CSV**: 1 dòng 1 bộ data; ghi UTF-8 có BOM nếu mở bằng Excel.
- **Markdown table**: để review hoặc dán vào cột Test Data của TC.

## 6. Cấm

- ❌ Placeholder (`email hợp lệ`, `dữ liệu đúng`)
- ❌ PII thật, data production
- ❌ Tự đặt constraint rồi sinh boundary theo đó mà không đánh dấu Assumption
- ❌ Bộ negative sai nhiều field cùng lúc
- ❌ Bộ data thiếu `expectedResult`
- ❌ Hard-code credential — dùng `<FROM_ENV>`

## 7. Checklist

- [ ] Bảng field + constraint có nguồn, đã được user xác nhận
- [ ] Đủ 4 nhóm (nhóm không áp dụng ghi lý do)
- [ ] Boundary đúng theo constraint đã xác nhận
- [ ] Negative chỉ sai 1 field mỗi bộ
- [ ] Data unique + traceable
- [ ] Mọi bộ có `expectedResult` hoặc đánh dấu UNCLEAR
- [ ] Không PII thật, không secret
