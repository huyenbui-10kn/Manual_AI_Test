# Environment & Accounts — <Tên dự án>

## Môi trường

| Môi trường | Base URL | Mục đích | Ghi chú |
| --- | --- | --- | --- |
| DEV | `<url>` | | |
| STG | `<url>` | | |
| PROD | `<url>` | Chỉ smoke khi được yêu cầu | |

| Portal / App | URL login | Landing sau login |
| --- | --- | --- |
| `<Admin>` | `<path>` | `<path>` |
| `<User>` | `<path>` | `<path>` |

## Quy ước credential

**Không ghi mật khẩu vào repo.** File này chỉ lưu email / role / mục đích. Mật khẩu lấy từ `<người cung cấp>` trong phiên làm việc hoặc từ biến môi trường / secret store (`.env` phải nằm trong `.gitignore`).

## Account

| Portal | Email / Username | Role | Password | Tình trạng / Ghi chú |
| --- | --- | --- | --- | --- |
| `<Admin>` | `<email>` | `<role>` | `<FROM_ENV>` | |
| `<User>` | `<email>` | `<role>` | `<FROM_ENV>` | |

## Còn thiếu để execute đầy đủ

1. `<account role X — cần cho case Authorization của màn Y>`
2. `<test data đặc thù>`

## Ghi chú kỹ thuật khi login / thao tác

- `<ví dụ: tick "Remember me" → token lưu localStorage>`
- `<ví dụ: cho phép multi-session — không FAIL khi 2 session song song>`
- `<ví dụ: ngôn ngữ UI / message expected>`
