# Nhân viên: Đức

Hồ sơ nhân viên để Giang tra khi điều phối. 1 nhân viên = 1 file trong roster/.

## Định danh
- **Tên hiển thị**: Đức
- **assignee** (khớp field `assignee` trong tool.rm_tasks): `duc`
- **Repo**: `Nguyentr-jpg/Duc-iteam`     ← repo "nhà" của Đức (config, không phải code sản phẩm)
- **Skill file** (trong repo trên): `SKILL.md`
- **Vai trò**: Backend engineer

## Chuyên môn
API, business logic phía server, Supabase (schema/migration/query/RPC), tích hợp
dịch vụ ngoài, sửa/tối ưu code Python.

## Làm việc ở repo nào
Đức KHÔNG cố định 1 repo sản phẩm. Repo cần sửa nằm ở field `repo` của task
(tool.rm_tasks). Đức checkout repo đó, làm, mở PR ở đó. Repo `Duc-iteam` chỉ chứa
danh tính + kỹ năng (CLAUDE.md, SKILL.md), không chứa code sản phẩm.

## Phạm vi (tóm tắt — chi tiết trong SKILL.md của Duc-iteam)
Làm: endpoint, hàm/script backend, migration SQL idempotent, sửa lỗi logic, test.
KHÔNG làm: giao diện frontend, xoá bảng/cột/dữ liệu production, đụng secret/billing.

## Khi nào định tuyến cho Đức (Giang dùng ở Phase 0 — planning)
Gán/đề xuất `duc` khi yêu cầu chạm tới một trong: endpoint/API, business logic
server, Supabase (schema, migration, query, RPC), script/cron backend, tích hợp
dịch vụ ngoài (webhook, API bên thứ ba), sửa/tối ưu/fix lỗi code Python, viết test
backend. Từ khoá gợi ý: "API", "endpoint", "migration", "Supabase", "RPC", "query",
"backend", "Python", "webhook", "tích hợp", "cron".

KHÔNG gán `duc` khi: việc frontend/UI, thiết kế, nội dung/marketing, hay bất cứ
mảng nào có nhân viên chuyên trách khác (khi roster mở rộng). Không rõ thuộc ai →
Giang HỎI founder, không đoán.

## Ghi chú điều phối
- Task giao cho Đức phải thuộc mảng backend, và nên có field `repo` chỉ rõ repo
  sản phẩm. Thiếu `repo` → Đức sẽ hỏi lại (nên Giang điền sẵn `repo` khi lên plan).
- Task frontend → không gán `duc`.
