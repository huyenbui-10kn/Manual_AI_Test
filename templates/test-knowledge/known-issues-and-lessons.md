# Known Issues & Lessons — <Tên dự án>

Ghi lại phát hiện **đã xác minh bằng evidence**. Đọc file này **trước khi kết luận FAIL** để tránh báo trùng hoặc báo sai.

## Bài học khi quan sát UI

### <Tiêu đề bài học>

`<Hiện tượng — vì sao dễ kết luận sai — cách kiểm tra đúng — kết luận đã xác minh (màn, ngày)>`

## Bài học khi đọc tài liệu spec

- `<ví dụ: cách trích BD từ xlsx, cell tô xám = out of scope>`

## Defect / nghi vấn theo màn

| Màn / Module | Bug ID / File defect | Trạng thái | Ghi chú |
| --- | --- | --- | --- |
| | | | |

## Gap spec đã phát hiện (không phải bug code)

- `<ví dụ: ma trận quyền thiếu dòng cho màn X → không có expected phân quyền>`

## Cách ghi FAIL

Mỗi FAIL bắt buộc có: Actual Result, Expected Result (trích spec + nguồn), evidence, và **các bước tái hiện**. Thiếu bước tái hiện thì chưa được để trạng thái FAIL. Chi tiết: rule `bug-report.mdc`.
