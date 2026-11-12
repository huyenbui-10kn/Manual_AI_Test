---
name: regression-impact-analyzer
description: Phân tích thay đổi (git diff, PR, danh sách ticket trong release) để xác định vùng chức năng bị ảnh hưởng, đánh giá rủi ro và chọn bộ regression/smoke test cần chạy. Dùng khi chuẩn bị release, review PR, lập phạm vi regression hoặc hỏi "thay đổi này cần test lại những gì".
---

# Regression Impact Analyzer

## When to Use

- Trước release / deploy: cần chọn bộ regression phù hợp thay vì chạy toàn bộ
- Review PR / hotfix: thay đổi này ảnh hưởng tới đâu
- Có danh sách ticket trong sprint, cần phạm vi test lại

## Input

| Input | Bắt buộc | Ghi chú |
|---|---|---|
| Thay đổi | ✅ | Một trong: PR URL (`gh pr diff`), branch/commit range (`git diff A..B`), danh sách ticket + mô tả |
| Bộ test case / checklist hiện có | ⭕ | Để map vùng ảnh hưởng → TC cụ thể |
| Tài liệu kiến trúc / module map | ⭕ | Giúp xác định dependency |
| Thời gian test cho phép | ⭕ | Để cắt theo priority |

## 1. Thu thập thay đổi

- PR: `gh pr view <url> --json title,body,files` và `gh pr diff <url>`.
- Git: `git diff --stat A..B`, sau đó đọc chi tiết file quan trọng.
- Ticket: trích scope, AC, module được nhắc tới.

Chỉ đọc file liên quan; không quét toàn bộ repo.

## 2. Phân loại từng thay đổi

| Loại file / thay đổi | Mức lan tỏa điển hình |
|---|---|
| Migration DB, schema, model dùng chung | **Rộng** — mọi màn/API đọc ghi bảng đó |
| Auth, permission, middleware, config | **Rộng** — mọi luồng cần đăng nhập/phân quyền |
| Service / util / component dùng chung | **Rộng–Vừa** — tìm mọi nơi gọi tới |
| API contract (request/response) | **Vừa** — FE consumer, integration, mobile |
| Translation / i18n key dùng chung | **Vừa** — mọi màn dùng key |
| Queue / batch / cache / cron | **Vừa** — dữ liệu downstream, report |
| Logic một màn hình | **Hẹp** — màn đó + màn liền kề |
| CSS / text / cosmetic | **Hẹp** |
| Test, docs, CI | Không ảnh hưởng runtime (vẫn kiểm tra CI) |

Với code dùng chung: tìm nơi sử dụng (`rg "<SymbolName>"`) để liệt kê consumer thực tế, không suy đoán.

## 3. Bảng ảnh hưởng

| # | Thay đổi (file/ticket) | Loại | Vùng bị ảnh hưởng trực tiếp | Vùng bị ảnh hưởng gián tiếp | Bằng chứng | Risk |
|---|---|---|---|---|---|---|
| 1 | `services/notice.ts` đổi điều kiện lọc | Service dùng chung | C007 danh sách thông báo | Portal staff: banner số chưa xác nhận | `rg` thấy 3 nơi gọi | High |

Risk = Impact × Likelihood:

- **Impact cao**: tiền, bảo mật/phân quyền, mất/sai dữ liệu, luồng chính, report quan trọng.
- **Likelihood cao**: thay đổi logic (không phải chỉ refactor đổi tên), code dùng chung, thiếu unit test, vùng từng có bug (`known-issues-and-lessons.md`).

Không đủ thông tin → ghi `Cần xác nhận với dev`, không tự hạ risk.

## 4. Chọn bộ test

| Tầng | Nội dung | Khi nào |
|---|---|---|
| **Smoke** | Login, điều hướng chính, 1 happy path mỗi module chính | Luôn chạy |
| **Targeted** | Test trực tiếp cho thay đổi (TC của ticket) | Luôn chạy |
| **Impacted regression** | TC của vùng gián tiếp, ưu tiên High → Medium | Theo bảng ảnh hưởng |
| **Full regression** | Toàn bộ | Thay đổi rộng (DB, auth, framework upgrade) hoặc release lớn |

Khi có bộ TC hiện có: map sang TC ID cụ thể (lọc theo Module / Tags / VP). Vùng ảnh hưởng chưa có TC → ghi **GAP** và đề xuất viewpoint/TC cần bổ sung (chuyển sang `rbt-manual-testing`).

Thời gian hạn chế → cắt từ dưới lên: Low risk trước, giữ nguyên Smoke + Targeted + High.

## 5. Output

1. Tóm tắt thay đổi (số file, module, loại thay đổi)
2. Bảng ảnh hưởng (mục 3)
3. Bộ test đề xuất: `Tầng | TC ID / Checklist ID | Module | Risk | Lý do chọn`
4. GAP: vùng ảnh hưởng chưa có test
5. Câu hỏi cần dev xác nhận
6. Vùng **không** cần test lại + lý do (để reviewer kiểm tra)

## 6. Cấm

- ❌ Kết luận "không ảnh hưởng" khi chưa tìm consumer của code dùng chung
- ❌ Chọn test chỉ theo tên file mà không đọc thay đổi
- ❌ Bỏ Smoke vì thiếu thời gian
- ❌ Hạ risk để vừa thời gian mà không báo user
