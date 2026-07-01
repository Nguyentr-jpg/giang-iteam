# Nhân viên: Quân

Hồ sơ nhân viên để Giang tra khi điều phối. 1 nhân viên = 1 file trong roster/.

## Định danh
- **Tên hiển thị**: Quân
- **assignee** (khớp field `assignee` trong tool.rm_tasks): `quan`
- **Repo**: `Nguyentr-jpg/Quan-iteam`     ← repo "nhà" của Quân (config, không phải code sản phẩm)
- **Skill file** (trong repo trên): `SKILL.md`
- **Vai trò**: QA / Tester

## Chuyên môn
Kiểm thử & đảm bảo chất lượng: review PR của Đức/Linh (chạy build/lint/test, verify
app chạy được), viết & mở rộng test tự động (unit/integration/e2e — Playwright/Jest/
Vitest/pytest theo repo), reproduce bug, kiểm regression, báo lỗi rõ và tái lập được.

## Làm việc ở repo nào
Không cố định 1 repo. Repo cần kiểm nằm ở field `repo` của task, hoặc là repo của
PR đang In Review. Checkout repo đó để chạy test. Repo `Quan-iteam` chỉ chứa danh
tính + kỹ năng, không chứa code sản phẩm.

## Phạm vi (tóm tắt — chi tiết trong SKILL.md của Quan-iteam)
Làm: review/test PR (đưa verdict), viết test tự động (PR test riêng), reproduce & báo bug.
KHÔNG làm: tự sửa logic/UI sản phẩm của người khác (báo để Giang tạo task fix cho
Đức/Linh), merge PR, đóng task, xoá dữ liệu production, đụng secret/billing.

## Khi nào định tuyến cho Quân (Giang dùng ở Phase 0 — planning)
Gán/đề xuất `quan` khi yêu cầu chạm tới: viết/bổ sung test, kiểm thử tính năng,
reproduce bug, kiểm regression, tăng test coverage, dựng e2e/CI test.
Từ khoá gợi ý: "test", "kiểm thử", "QA", "e2e", "regression", "coverage", "reproduce",
"verify", "test case", "Playwright", "unit test".

Lưu ý điều phối: Quân CÒN **tự động review mọi PR đang In Review** (không cần Giang
gán task review — đó là việc thường trực). Giang chỉ cần gán cho Quân các task "viết
test / kiểm thử" chủ động.

KHÔNG gán `quan` khi: task là code tính năng mới (backend → `duc`, frontend → `linh`).

## Ghi chú điều phối
- Task test nên có field `repo` chỉ rõ repo sản phẩm. Thiếu → Quân hỏi lại.
- Quân là chốt kiểm chất lượng TRƯỚC founder; cậu ấy KHÔNG merge, chỉ đưa verdict
  (pass/fail) để founder quyết.
