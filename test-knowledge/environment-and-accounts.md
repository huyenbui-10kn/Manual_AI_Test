# Environment & Accounts

## Môi trường

| Môi trường | Base URL | Ghi chú |
| --- | --- | --- |
| Staging (dev) | `https://app-dev.stg.welvielearning.com` | Môi trường đang test toàn bộ checklist |

| Portal | URL login | Landing sau login |
| --- | --- | --- |
| System admin | `/admin/login` | `/admin/dashboard` |
| Company admin | `/company/login` | `/company/dashboard` |

BD ghi URL dạng `~admin/...` / `~company/...` và có typo (`dashboad`, `noties`, `infomations`). Luôn lấy URL thật từ app.

## Quy ước credential

**Không ghi mật khẩu vào repo.** File này chỉ lưu email/role/mục đích; mật khẩu lấy từ Bui trong phiên làm việc hoặc từ biến môi trường / file `.env` (đã nằm trong `.gitignore`). Cột "Password" dưới đây cố tình để dạng ký hiệu.

## Account đã dùng

| Portal | Email | Role / loại | Password | Tình trạng |
| --- | --- | --- | --- | --- |
| System admin | `admin@fairtechnologies.co.jp` | Admin (nghi `super`) | `<hỏi Bui>` | Đã login thành công nhiều lần |
| System admin | `huyen.bui01@10kn.io` … `huyen.bui04@10kn.io` | 4 role phụ để test phân quyền A013 | `<hỏi Bui>` | Đã dùng cho A013; chưa map chắc từng email ↔ role nào |
| Company admin | `huyenbt@gmail.com` | Company admin | `<hỏi Bui>` | Đã login thành công (C001) — **chưa xác nhận là 本社 / エリア / 事務所** |
| ? | `huyen.bui09@10kn.io` | Chưa rõ | `<hỏi Bui>` | Login thất bại ở cả `/company/login` và `/admin/login` |
| ? | `huyenstaff02@gmail.com` | Nghi là staff (portal User) | `<hỏi Bui>` | Chưa xác định URL portal User |

## Thiếu để execute đầy đủ

1. **Role của account Company admin**: cần biết account nào là `tenant` (本社), `area` (エリア), `branch` (事務所) — bắt buộc cho mọi case Authorization của C0xx.
2. **Account portal User (staff)**: cần để verify các case thông báo phía staff (popup 署名 ON, banner số 未確認, `確認スタッフ数`, xóa お知らせ kéo theo phía staff). Chưa biết URL portal User.
3. **Account System admin theo từng role** (`super` / `content_admin` / `cs_lead` / `cs` / `viewer`) để phủ ma trận quyền ở `../business-knowledge/welvie-roles-permissions.md`.
4. **法人 theo từng 法人種別** (一般 / FC本部 / グループ親会社 / FC加盟店 / グループ子会社) để test FC dashboard access và luồng親子.

## Ghi chú kỹ thuật khi login

- Checkbox `ログイン情報保存`: tick → token lưu `localStorage` (giữ session qua phiên mới); không tick → `sessionStorage`.
- BD cho phép **multi-session**: cùng account login nhiều thiết bị không bị chặn (`同時ログイン制御`). Không kết luận FAIL khi thấy 2 session song song.
- Portal admin/company chỉ có tiếng Nhật, nên mọi message expected đều là tiếng Nhật.
