# Open Questions

QnA còn mở với BA/dev. Được trả lời → cập nhật `../business-knowledge/` rồi **xóa mục khỏi file này**.

Trạng thái: `OPEN` (chờ trả lời) / `ANSWERED` (đã trả lời, đã cập nhật knowledge) / `SKIPPED` (Bui quyết định bỏ qua).

## Chung toàn hệ thống

| ID | Điểm | Câu hỏi | Ảnh hưởng | Trạng thái |
| --- | --- | --- | --- | --- |
| G01 | `パフォーマンス定義` No.1 同時接続数 bỏ trống | Số kết nối đồng thời mục tiêu là bao nhiêu? | Không test được Performance | OPEN |
| G02 | `パフォーマンス定義` No.2 推奨ブラウザ bỏ trống | Browser nào trong phạm vi hỗ trợ? | Không test được Compatibility | OPEN |
| G03 | Role `viewer` (System admin) | BD ghi role này chưa được thiết kế ban đầu — phạm vi quyền thật là gì? | Không có expected cho case viewer | OPEN |
| G04 | 7 switch chức năng ở A003 phần lớn `保留` | Switch nào đã implement trong vòng này? | Ảnh hưởng menu hiển thị của Company admin | OPEN |
| G05 | URL trong BD sai chính tả (`dashboad`, `noties`, `infomations`) | Có cập nhật BD hay giữ nguyên? | Chỉ là typo tài liệu, không FAIL app | OPEN |

## C001 各社管理ログイン (Company Login)

Nhóm câu hỏi phát sinh khi đối chiếu sheet `法人` (A003) với luồng login company — chi tiết ở `test case/welvie/C001_company_login_checklist.csv`.

| ID | Điểm | Câu hỏi | Trạng thái |
| --- | --- | --- | --- |
| L01 | 法人 ở trạng thái `解約` / `離脱` | Company admin còn login được không? Message gì? | OPEN |
| L02 | Lock công ty (account lockout) | Message khi login vào công ty bị lock là gì? | OPEN |
| L03 | Session đang mở khi công ty bị lock | Bị đăng xuất ngay hay vẫn dùng tới khi hết session? | OPEN |
| L04 | 法人 trạng thái `テスト` | Có khác biệt gì khi login? | OPEN |
| L05 | Một email thuộc nhiều công ty | Có được phép? Nếu có thì login vào công ty nào? | OPEN |
| L06 | Message khi quyền bị thu hồi | Nội dung message? | OPEN |
| L07 | Menu hiển thị theo switch + 法人種別 | Quy tắc đầy đủ khi kết hợp 2 yếu tố? | OPEN |
| L08 | Vai trò manager trong công ty | Có role trung gian ngoài tenant/area/branch? | OPEN |

## C003 研修管理 (Training Management)

Checklist: `test case/welvie/C003_training_management_checklist.csv` (57 mục, tất cả UNCHECK).

| ID | Điểm | Câu hỏi | Trạng thái |
| --- | --- | --- | --- |
| Q01 | Chọn năm ở tab 進捗確認 | Phạm vi năm cho phép chọn (từ năm nào tới năm nào)? | OPEN |
| Q02 | Xuất CSV | Tên file, encoding, thứ tự cột? | OPEN |
| Q03 | Giải thích ký hiệu trên bảng tiến độ | Danh sách ký hiệu đầy đủ và nghĩa? | OPEN |
| Q04 | CHANGE-01 staff chuyển事業所 | Dữ liệu tiến độ tính về事業所 cũ hay mới? | OPEN |
| Q05 | — | — | SKIPPED (Bui yêu cầu bỏ qua) |
| Q06 | Thống kê chất lượng báo cáo | Công thức tính? | OPEN |
| Q07 | Dropdown chất lượng / trạng thái | Tập giá trị đầy đủ? | OPEN |
| Q08 | Cơ chế feedback báo cáo | Ai gửi, staff thấy ở đâu? | OPEN |
| Q09 | Tab 研修実績DL | Từng loại báo cáo tải về gồm cột gì? | OPEN |
| Q10 | 必須研修設定 — tag 法定研修 | Tag gắn tự động hay thủ công? | OPEN |
| Q11 | 必須研修設定 — checkbox tháng | Bỏ tick tháng đã có dữ liệu thì dữ liệu cũ thế nào? | OPEN |
| Q12 | Quyền theo role ở C003 | area/branch có sửa được kế hoạch của công ty khác không (matrix ghi 要確認)? | OPEN |

## C004 スタッフ管理 (Staff Management)

Checklist: `test case/welvie/C004_staff_management_checklist.csv` (55 mục: 50 PASS / 2 FAIL / 3 UNCHECK).

| ID | Điểm | Câu hỏi | Trạng thái |
| --- | --- | --- | --- |
| S01 | No.19 パスワード (checkbox, không bắt buộc) vs app (bắt buộc khi có email; không checkbox) | Spec nào đúng? Có email mà không mật khẩu thì staff login thế nào? | OPEN |
| S02 | Mockup 「全9名（契約上限 10名）」 | Có cần hiển thị 契約上限 ở danh sách không? | OPEN |
| S03 | 電話番号 | Có đúng là bắt buộc đủ 11 số (min 11)? A004 đang cho lưu 10 số — cần thống nhất | OPEN |
| S04 | 事業所 khi tenant tạo staff | Có phải 必須 không (BD No.16 không ghi)? | OPEN |
| S05 | ログイン情報送信 | Có cấp lại mật khẩu mới trong mail không? Có cần dialog xác nhận? Staff chỉ có phone thì nút nên ẩn / disable hay báo lỗi? | OPEN |
| S06 | Modal 編集 | Ô 新しいパスワード có nên ẩn khi staff không có email (No.19)? | OPEN |
| S07 | CL052 cross-tenant | Bui cấp 1 staff QA thuộc 法人 khác để kiểm sửa / xóa chéo 法人 | OPEN |

## C007 スタッフへのお知らせ (Staff Notices)

Checklist: `test case/welvie/C007_staff_notices_checklist.csv` (33 mục, tất cả UNCHECK).

| ID | Điểm | Câu hỏi | Trạng thái |
| --- | --- | --- | --- |
| N01 | 画面URL `~company/noties` | URL thật là `/company/notices`? | OPEN |
| N02 | Authority matrix không có dòng `スタッフへのお知らせ` | Role area/branch được tạo/xóa お知らせ hay chỉ xem? | OPEN |
| N03 | Định nghĩa 配信中 / 配信済み | Mốc "staff đã nhận" là mở app, đọc, hay ký xác nhận? | OPEN |
| N04 | 削除 không nêu modal xác nhận | Có cần bước xác nhận trước khi xóa (vì xóa cả phía staff)? | OPEN |
| N05 | Default của switch 署名 | ON hay OFF khi mở modal tạo mới? | OPEN |
| N06 | 内容 max 10000 không có message | Message khi vượt 10000 ký tự là gì? | OPEN |
| N07 | 備考 `既存システムと同じにする` | Hệ thống cũ có ràng buộc nào cần giữ (sort, phân trang, giới hạn số bản ghi)? | OPEN |
| N08 | Không nêu sort / phân trang / trạng thái danh sách rỗng | Quy tắc là gì? | OPEN |
