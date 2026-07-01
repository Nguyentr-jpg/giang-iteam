# Nhân viên: Linh

Hồ sơ nhân viên để Giang tra khi điều phối. 1 nhân viên = 1 file trong roster/.

## Định danh
- **Tên hiển thị**: Linh
- **assignee** (khớp field `assignee` trong tool.rm_tasks): `linh`
- **Repo**: `Nguyentr-jpg/Linh-iteam`     ← repo "nhà" của Linh (config, không phải code sản phẩm)
- **Skill file** (trong repo trên): `SKILL.md`
- **Vai trò**: Frontend engineer

## Chuyên môn
Giao diện người dùng: component/page, state phía client, form & validation, styling/
responsive/accessibility, gọi & hiển thị dữ liệu từ API backend, tối ưu UX và hiệu
năng frontend.

## Làm việc ở repo nào
Linh KHÔNG cố định 1 repo sản phẩm. Repo cần sửa nằm ở field `repo` của task
(tool.rm_tasks). Linh checkout repo đó, làm, mở PR ở đó. Repo `Linh-iteam` chỉ chứa
danh tính + kỹ năng (CLAUDE.md, SKILL.md), không chứa code sản phẩm.

## Phạm vi (tóm tắt — chi tiết trong SKILL.md của Linh-iteam)
Làm: component/page UI, state phía client, form/validation, style/responsive, gọi & render API.
KHÔNG làm: business logic/DB/migration phía server (việc của Đức), xoá dữ liệu production, đụng secret/billing.

## Khi nào định tuyến cho Linh (Giang dùng ở Phase 0 — planning)
Gán/đề xuất `linh` khi yêu cầu chạm tới: giao diện/UI, component, page/màn hình,
layout, style/CSS/Tailwind, responsive, form phía client, state management, sửa lỗi
hiển thị, tối ưu trải nghiệm/hiệu năng frontend, gọi & render dữ liệu từ API.
Từ khoá gợi ý: "UI", "giao diện", "component", "màn hình", "page", "layout", "CSS",
"style", "responsive", "form", "frontend", "hiển thị", "nút", "modal", "screen".

KHÔNG gán `linh` khi: việc backend/API/DB/migration (gán `duc`), hạ tầng, secret.
Task đụng CẢ frontend lẫn backend → Giang chẻ: phần UI cho `linh`, phần server cho
`duc` (mỗi phần 1 task con, ghi rõ phần phụ thuộc). Không rõ thuộc ai → hỏi founder.

## Ghi chú điều phối
- Task giao cho Linh phải thuộc mảng frontend, nên có field `repo` chỉ rõ repo sản
  phẩm. Thiếu `repo` → Linh sẽ hỏi lại (nên Giang điền sẵn `repo` khi lên plan).
- Task backend/DB → không gán `linh`.
