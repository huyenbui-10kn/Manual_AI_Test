# Test Viewpoint Catalog

Danh mục góc nhìn test (VP) dùng chung. Mỗi mục checklist **phải** gắn đúng 1 VP; VP là cầu nối giữa requirement và test case, và là cơ sở để kiểm tra Viewpoint Coverage.

Quy ước đặt mã VP theo màn: `VP_<VIẾT TẮT MÀN>_<số 3 chữ số>` + tên loại. Ví dụ `VP_NOTI_007 State`, `VP_CLOG_GAP_003`.

## Danh mục

| Loại VP | Câu hỏi cốt lõi | Áp dụng khi | Ví dụ đã dùng |
| --- | --- | --- | --- |
| **Functional** | Chức năng có chạy đúng mục đích BD không? | Mọi button/action | Bấm `新規作成` mở modal (C007_CL004) |
| **UI** | Item có hiển thị đúng loại/đúng chỗ như BD mô tả không? | Item có タイプ trong BD | Switch 署名 hiển thị và đổi được (C007_CL023) |
| **Input** | Required / kiểu dữ liệu / format có được kiểm tra đúng không? | Field có 必須 hoặc フォーマット | Bỏ trống タイトル → message required (C007_CL017) |
| **Boundary** | Giá trị tại biên và vượt biên xử lý thế nào? | Field có max length / range / ngày tháng | 255 ký tự OK, 256 bị chặn (C007_CL018/019) |
| **Combination** | Tổ hợp điều kiện dẫn tới hiển thị khác nhau có đúng không? | BD có mệnh đề "nếu... thì không hiện..." | 署名 OFF → ẩn 署名必要 và 確認スタッフ数 (C007_CL006/007) |
| **State** | Trạng thái và chuyển trạng thái có đúng tập giá trị không? | BD liệt kê trạng thái | 配信中 / 配信済み (C007_CL010–012) |
| **Data Integrity** | Dữ liệu lưu/hiển thị có khớp thao tác vừa làm không? | Sau create/update | 作成者 = account vừa 配信 (C007_CL008) |
| **Integration** | Dữ liệu có truyền đúng sang portal/màn khác không? | Luồng cross-portal, cross-screen | Xóa お知らせ → phía staff cũng mất (C007_CL015) |
| **Authorization** | Role nào được làm gì; chưa login thì sao? | Màn có trong Authority matrix | Role tenant/area/branch (C007_CL032) |
| **Security** | Input độc hại có bị vô hiệu hoá không? | Field text tự do | XSS payload trong タイトル (C007_CL030) |
| **Resilience** | Thao tác bất thường (double click, reload giữa luồng, back) có gây sai dữ liệu không? | Action ghi dữ liệu | Double click `配信する` (C007_CL029) |
| **Usability** | Thông báo/label có rõ và đúng ngôn ngữ không? | Có message hiển thị | Message tiếng Nhật khớp nguyên văn BD |
| **Compatibility** | Chạy đúng trên browser/thiết bị yêu cầu không? | Khi BD chốt 推奨ブラウザ | Chưa dùng — BD chưa điền giá trị |
| **Performance** | Thời gian phản hồi / tải dữ liệu lớn có đạt không? | Khi BD chốt 同時接続数 | Chưa dùng — BD chưa điền giá trị |

## Nguyên tắc chọn VP

- Một mục checklist = **một** VP = **một** mục đích. Ghép nhiều góc nhìn vào một mục là anti-pattern.
- Ưu tiên theo rủi ro: Integration và Authorization của luồng cross-portal thường rủi ro cao nhất ở dự án này, vì BD định nghĩa hành vi ở portal khác với nơi thao tác.
- Security và Resilience: BD Welvie **không** định nghĩa rule → vẫn test được nhưng chỉ **ghi nhận rủi ro**, không kết luận FAIL. Đánh dấu rõ trong cột Ghi chú.
- VP nào không có dữ liệu spec để đối chiếu (ví dụ Compatibility khi BD bỏ trống 推奨ブラウザ) → không sinh mục checklist, đưa vào `open-questions.md`.

## Coverage check

Sau khi sinh checklist, đối chiếu 2 chiều:

1. Mỗi item BD (theo No.) có ít nhất 1 VP phủ.
2. Mỗi VP có ít nhất 1 mục checklist.

Item BD bị tô xám không cần phủ. Item thiếu phủ mà không phải xám → bổ sung hoặc giải thích lý do trong report.
