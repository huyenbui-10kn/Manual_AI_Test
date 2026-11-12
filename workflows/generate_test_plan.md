---
description: Sinh Test Plan / Test Strategy cho một release, dự án hoặc tính năng — scope, approach, môi trường, entry/exit criteria, rủi ro, lịch, deliverables.
skills:
  - regression-impact-analyzer
rules:
  - requirement-analysis.mdc
---

> Đọc rule `.cursor/rules/requirement-analysis.mdc` để xác định scope. Khi plan cho một release có danh sách thay đổi, đọc thêm skill `.cursor/skills/regression-impact-analyzer/SKILL.md` để xác định phạm vi regression.

# Workflow: Generate Test Plan

```
Thu thập context → Scope & Risk → [STOP] → Draft Test Plan → [STOP] → Final
```

## Input

| Input | Bắt buộc | Ghi chú |
|---|---|---|
| Tên dự án / release / tính năng | ✅ | |
| Tài liệu requirement / BD / danh sách ticket | ✅ | |
| Mốc thời gian (code freeze, UAT, release) | ⭕ | Thiếu → để `TBD`, không tự đặt ngày |
| Nhân sự QA, môi trường, tool | ⭕ | Thiếu → để `TBD` |
| Template test plan của công ty | ⭕ | Có → dùng đúng template, không tự đổi mục |

## Bước 1: Thu thập & phân tích

1. Đọc tài liệu, liệt kê module / tính năng / ticket trong phạm vi.
2. Xác định loại test cần: functional, integration, API, regression, UI/UX, security, performance, compatibility, i18n, UAT support — mỗi loại ghi `Applicable` / `N/A` / `TBD` kèm lý do.
3. Đánh giá rủi ro sản phẩm & dự án (bảng mục 6 của template).
4. Liệt kê thông tin còn thiếu.

**DỪNG — trình bày:** scope in/out, loại test, top rủi ro, thông tin thiếu. Chờ user xác nhận.

## Bước 2: Draft Test Plan

Sinh theo template dưới (hoặc template của công ty). Mục không có thông tin → `TBD` + câu hỏi, không bịa.

**DỪNG — chờ user review.** Chỉnh sửa rồi xuất file `test-plan_<project>_<release>.md` khi user đồng ý.

## Template

```markdown
# Test Plan — <Project> <Release>

| Version | Ngày | Người soạn | Trạng thái |
|---|---|---|---|
| 0.1 | YYYY-MM-DD | | Draft |

## 1. Giới thiệu
Mục tiêu, bối cảnh, tài liệu tham chiếu (requirement, BD, API DOC — có version/link).

## 2. Phạm vi
### 2.1 In scope
| Module / Tính năng | Ticket | Loại thay đổi (mới/sửa) | Risk |
### 2.2 Out of scope
| Hạng mục | Lý do |

## 3. Chiến lược kiểm thử
| Loại test | Applicable | Cách tiếp cận | Tool | Người phụ trách |
|---|---|---|---|---|
| Functional (manual) | Yes | RBT: Requirement → Viewpoint → TC → Coverage | Excel/TestRail | |
| Integration / E2E | | | | |
| API | | Postman collection | Postman | |
| Regression | | Smoke + impacted regression | | |
| Security | | Phạm vi được phép | | |
| Performance | | Chỉ khi có SLA/tiêu chí | | |
| Compatibility | | Browser/device theo spec | | |

Kỹ thuật thiết kế: EP, BVA, Decision Table, State Transition, Pairwise, Error Guessing.

## 4. Môi trường & dữ liệu
| Môi trường | URL | Mục đích | Data | Ghi chú |
Account theo role (không ghi password). Nguồn test data, quy tắc dọn data.

## 5. Tiêu chí
### 5.1 Entry criteria
- Requirement/BD đã chốt (QnA trọng yếu đã trả lời)
- Build deploy lên môi trường test, smoke PASS
- Test case đã được review
- Account + test data sẵn sàng
### 5.2 Exit criteria
- 100% TC High/Medium đã chạy
- Không còn bug Critical/Major mở (hoặc được PO chấp nhận bằng văn bản)
- Viewpoint High/Medium Covered
- Regression + smoke PASS trên build release
### 5.3 Suspension / Resumption
- Tạm dừng khi: smoke fail, môi trường down > X giờ, bug blocker chặn > Y% TC
- Tiếp tục khi: nguyên nhân được khắc phục và smoke PASS lại

## 6. Rủi ro
| ID | Rủi ro | Loại (Product/Project) | Impact | Likelihood | Mitigation | Owner |

## 7. Lịch & ước lượng
| Hoạt động | Bắt đầu | Kết thúc | Effort | Phụ trách |
| Phân tích requirement & QnA | | | | |
| Thiết kế viewpoint & TC | | | | |
| Review TC | | | | |
| Execute vòng 1 | | | | |
| Retest & regression | | | | |
| Report | | | | |

## 8. Deliverables
Test plan, viewpoint, test case, coverage matrix, bug report, execution report, test summary report.

## 9. Quản lý defect
Tool, workflow trạng thái, quy tắc Severity/Priority (theo `bug-report.mdc`), SLA phản hồi.

## 10. Vai trò & liên lạc
| Vai trò | Người | Trách nhiệm |

## 11. Câu hỏi mở / TBD
| ID | Câu hỏi | Ảnh hưởng | Người trả lời |
```

## Quy tắc

- Không tự đặt ngày, nhân sự, SLA, ngưỡng performance khi chưa có nguồn — ghi `TBD`.
- Entry/Exit criteria là đề xuất; user/PM chốt.
- Rủi ro phải cụ thể cho dự án, không liệt kê rủi ro chung chung cho đủ bảng.
