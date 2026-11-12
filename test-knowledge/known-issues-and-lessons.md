# Known Issues & Lessons

Ghi lại phát hiện đã xác minh bằng evidence. Đọc file này **trước khi kết luận FAIL** để tránh báo trùng hoặc báo sai.

## Bài học khi quan sát UI

### Message hiển thị bằng toast ở đáy màn hình, không inline

Ứng dụng dùng `react-hot-toast`. Message validation/login **không** nằm dưới field mà là toast ở đáy màn hình và **tự biến mất**. Đã từng nghi "message sai so với BD" chỉ vì tìm message ở chỗ inline.

Cách bắt chắc chắn khi execute: cài `MutationObserver` trên `document.body` trước khi submit để log mọi node text mới xuất hiện, thay vì chụp snapshot sau khi toast đã tắt.

Kết luận đã xác minh cho C001 (case sai password và sai email): toast hiện đúng `メールまたはパスワードが間違っています。ご確認ください` → **khớp BD**, giữ PASS.

### Field email có `type=email` → 2 nguồn message khác nhau

Trình duyệt chặn trước bằng validation HTML5 (message của Chrome, tiếng hệ thống), chưa gửi request. Message của BD chỉ xuất hiện khi request thật được gửi. Phân biệt rõ trước khi so với expected trong BD.

### Xác minh phía backend bằng cách chặn XHR/fetch

Khi cần biết app thực sự gọi API nào và server trả gì (phân biệt lỗi FE hay BE), intercept `XMLHttpRequest` / `fetch` qua CDP `Runtime.evaluate` trước khi bấm submit, hoặc bật CDP Network. Đây là cách đã dùng để chứng minh message toast khớp response API ở C001.

### Session: localStorage vs sessionStorage

Checkbox `ログイン情報保存` quyết định nơi lưu token: tick → `localStorage` (session sống qua phiên mới, auto-login khi vào lại `/admin/login`); không tick → `sessionStorage`.

## Bài học khi trích BD (xlsx)

- Trích bằng PowerShell + `System.Xml.XmlDocument`: đọc `xl/sharedStrings.xml` để giải mã cell `t="s"`, nối toàn bộ node `<t>` trong một `<si>` (nếu không sẽ mất phần rich text).
- **Luôn đọc kèm style**: map `cellXfs → fillId/fontId` để phát hiện dòng tô xám (out of scope) và in đậm. Bỏ qua bước này là nguy cơ test phần đã bị loại khỏi scope.
- Ô 備考 cạnh 画面ID chứa trạng thái đối ứng (`対応不要`, `一旦保留`, `既存システムと同じにする`) — đọc trước khi sinh checklist cho cả màn.
- BD có typo cần biết trước: URL `dashboad`, `noties`, `infomations`; tên field `NoticeTittle`. Không báo FAIL cho app vì typo tài liệu.
- Số ở cột 発効日 / No. đôi khi là serial date hoặc float (`46232`, `75.0`) — không dùng trực tiếp làm nhãn.

## Defect / nghi vấn theo màn

Danh sách defect chi tiết nằm trong `test case/welvie/*_defects.csv` (hiện có cho A005 required training, A005 allstaff, A008 webinar). Bảng dưới chỉ là chỉ mục.

| Màn | File defect | Ghi chú |
| --- | --- | --- |
| A005 研修進捗 — 必須研修進捗 | `A005_required_training_defects.csv` | |
| A005 研修進捗 — 全スタッフ実績 | `A005_allstaff_defects.csv` | |
| A008 コンテンツ / webinars | `A008_webinar_defects.csv` | |
| C001 Company Login | — | Case sai email/sai password đã retest: **không phải defect** |
| C004 スタッフ管理 | `C004_staff_management_checklist.md` (Test Report) | FAIL CL019 (có email bắt buộc mật khẩu; BD No.19 / r12 cho phép bỏ trống) và CL006 (không hiện 契約上限 như mockup) |

### C004 スタッフ管理 — bài học khi execute (2026/09/28)

- Modal staff dùng Radix Select: set value bằng JS / event giả lập **không** chọn được 事業所 → dùng click thật (browser_click theo ref). Tenant bắt buộc chọn 事業所 khi tạo.
- Tìm kiếm / lọc chỉ chạy khi bấm `検索` (QA058). Chọn dropdown hoặc gõ tên không tự gọi API.
- `ログイン情報送信` gửi ngay, không có dialog xác nhận; sau khi gửi thì mật khẩu cũ của staff không còn login được → đừng dùng staff vừa gửi thông tin đăng nhập cho case login portal User.
- Server validate body **trước** khi kiểm quyền bản ghi: gửi body không hợp lệ tới id ngoài phạm vi vẫn nhận 422 → không kết luận được phân quyền. Body hợp lệ tới id ngoài phạm vi → 404 `データが見つかりません。`.
- Trần staff tính theo số staff 有効 hiển thị trong danh sách (không tính account manager — QA046). Sau C004 dm đang ở **10/10** → test tạo staff khác trong dm sẽ bị chặn.
- 電話番号 ở C004 phải đúng 11 số; A004 cho lưu số 10 chữ số → staff tạo ở A004 có thể không sửa được ở C004 nếu không đổi số.
- Portal User không có nút ログアウト; portal Company có ログアウト trong menu `設定`.

## Gap spec đã phát hiện (không phải bug code)

- Authority matrix thiếu dòng cho `スタッフへのお知らせ` (C007) và `コンテンツ` (C006) → không có expected phân quyền.
- `Validation rule` có max length = 10000 cho `NoticeDescription` nhưng **không có message** khi vượt.
- `パフォーマンス定義` bỏ trống `同時接続数` và `推奨ブラウザ` → không test được Performance/Compatibility.
- Role `viewer` (System admin) được BD ghi là chưa chốt phạm vi quyền.
- 7 switch chức năng ở A003 phần lớn ở trạng thái `保留`; `コンテンツ` và `スタッフへのお知らせ` ghi "Huy đang xem xét spec".

## Cách ghi FAIL

Mỗi FAIL bắt buộc có: Actual Result, Expected Result (trích BD), evidence (screenshot / response API / log toast), và **các bước tái hiện**. Thiếu bước tái hiện thì chưa được để trạng thái FAIL.
