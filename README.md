# AI Testing Kit

Bộ rule / skill / workflow cho AI (Cursor) đóng vai Senior QA trong toàn bộ vòng đời kiểm thử: lập kế hoạch, phân tích requirement, thiết kế viewpoint, sinh test case / checklist / API test / test data, thực thi, báo bug và chọn regression. Kit tập trung vào **manual testing** (không bao gồm automation test).

## Cấu trúc

```
AI_Testing_Kit/
├── rules/                         # Luật AI PHẢI tuân theo (WHAT / HOW)
│   ├── tester-general.mdc             alwaysApply — pipeline bắt buộc, chuẩn chất lượng
│   ├── qa-safety.mdc                  alwaysApply — dữ liệu, credential, môi trường, trung thực kết quả
│   ├── requirement-analysis.mdc       Requirement → REQ/AC, QnA, assumption
│   ├── test-viewpoint.mdc             Test Viewpoint (12 nhóm catalog)
│   ├── test-case-generation.mdc       Sinh TC từ viewpoint, format cột, test data
│   ├── viewpoint-coverage.mdc         Ma trận coverage, gap analysis, Definition of Done
│   ├── checklist-generation.mdc       Checklist (WHAT to check) từ Basic Design
│   ├── bug-report.mdc                 Chuẩn bug report, severity vs priority
│   ├── api-testing.mdc                Thiết kế API test + Postman script, OWASP API checklist
│   └── projects/
│       └── squid-full-rbt.mdc         Riêng dự án SQUID (FULL RBT, template Excel)
│
├── skills/                        # Kiến thức / kỹ thuật chuyên sâu, AI đọc khi cần
│   ├── rbt-manual-testing/            Master skill manual TC — QUICK / FULL RBT
│   ├── requirements-analyzer/         Requirement từ web UI / DOM / ticket
│   ├── test-data-generator/           Data Positive / Negative / Boundary / Edge
│   └── regression-impact-analyzer/    Diff / PR / ticket → vùng ảnh hưởng → bộ regression
│
├── workflows/                     # Quy trình từng bước + checkpoint, gọi bằng /tên
│   ├── generate_test_plan.md
│   ├── analyze_requirement_document.md
│   ├── generate_requirements_from_website.md
│   ├── generate_testcases_from_requirements.md   (QUICK)
│   ├── generate_manual_testcases_rbt.md          (FULL RBT)
│   ├── qa_checklist_from_basic_design.md
│   ├── generate_integration_testcases.md
│   ├── generate_api_testcases_postman.md
│   ├── generate_test_data.md
│   ├── execute_testcases_browser.md
│   └── report_bug.md
│
├── templates/
│   └── test-knowledge/            # Khung trống cho dự án mới
│
└── test-knowledge/                # Ví dụ đã điền (dự án Welvie)
```

Phân vai: **Rule** định nghĩa chuẩn, **Skill** cung cấp kỹ thuật chi tiết, **Workflow** điều phối bước + điểm dừng chờ user. Khi mâu thuẫn: **Rule > Skill > Workflow**.

## Cài vào project

| Kit | Đích trong project |
| --- | --- |
| `rules/` | `.cursor/rules/` |
| `skills/` | `.cursor/skills/` |
| `workflows/` | `.cursor/commands/` (gọi bằng `/tên_workflow`) |
| `templates/test-knowledge/` | `test-knowledge/` ở gốc project, rồi điền theo dự án |

`rules/projects/*` chỉ copy khi đúng dự án đó. `test-knowledge/` trong kit chỉ là ví dụ tham khảo.

## Vòng đời kiểm thử & workflow tương ứng

```
Plan → Analyze → Design → Data → Execute → Report bug → Regression
```

| Giai đoạn | Tình huống | Workflow | Skill / Rule chính |
| --- | --- | --- | --- |
| Plan | Lập test plan cho release / dự án | `/generate_test_plan` | `regression-impact-analyzer` |
| Analyze | Có ticket/spec, cần hiểu và tìm điểm mơ hồ | `/analyze_requirement_document` | `requirements-analyzer`, `requirement-analysis.mdc` |
| Analyze | Chỉ có website, chưa có tài liệu | `/generate_requirements_from_website` | `requirements-analyzer` |
| Design | Requirement rõ, 1 module, cần TC nhanh | `/generate_testcases_from_requirements` | `rbt-manual-testing` (QUICK) |
| Design | Nhiều module / phức tạp / cần QnA | `/generate_manual_testcases_rbt` | `rbt-manual-testing` (FULL RBT) |
| Design | Có Basic Design, cần checklist + execute + report | `/qa_checklist_from_basic_design` | `checklist-generation.mdc` |
| Design | Luồng xuyên nhiều site / role / màn | `/generate_integration_testcases` | `test-viewpoint.mdc` |
| Design | Có API DOC, cần Postman collection | `/generate_api_testcases_postman` | `api-testing.mdc` |
| Data | Cần bộ test data | `/generate_test_data` | `test-data-generator` |
| Execute | Chạy TC/checklist trên browser, thu evidence | `/execute_testcases_browser` | `qa-safety.mdc` |
| Report | Có FAIL cần báo bug | `/report_bug` | `bug-report.mdc` |
| Regression | PR / release cần chọn test chạy lại | (gọi skill) | `regression-impact-analyzer` |

## Pipeline bắt buộc

```
Requirement → Test Viewpoint → Test Case → Viewpoint Coverage
```

Không sinh test case khi chưa có bảng viewpoint. Không tự đoán business logic — thiếu thông tin thì QnA, không được trả lời thì ghi Assumption rõ ràng. Không tuyên bố PASS/FAIL khi chưa có evidence.

## Quy ước đặt tên

| Loại | Quy ước | Ví dụ |
| --- | --- | --- |
| Rule | kebab-case `.mdc`, frontmatter `description` / `globs` / `alwaysApply` | `test-viewpoint.mdc` |
| Skill | folder kebab-case + `SKILL.md`, `name` trùng tên folder | `rbt-manual-testing/SKILL.md` |
| Workflow | snake_case `.md`, động từ đứng đầu, frontmatter `description` / `skills` / `rules` | `generate_test_data.md` |

Chỉ rule thật sự cần cho mọi chat mới đặt `alwaysApply: true` (hiện có `tester-general.mdc`, `qa-safety.mdc`) để tiết kiệm token.
