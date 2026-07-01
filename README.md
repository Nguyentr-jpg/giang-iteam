# giang-iteam

Repo gốc của **Giang** — orchestrator điều phối task cho team IT.

## Nội dung

- **`ORCHESTRATOR.md`** — playbook của Giang: mỗi lần routine chạy, đọc task
  `To Do` trên Notion, giao cho đúng nhân viên (subagent), mở PR trên GitHub,
  cập nhật trạng thái và báo cáo Slack.
- **`SKILL_TEMPLATE.md`** — template file `SKILL.md` đặt ở gốc repo của mỗi
  nhân viên. Định danh nhân viên là ai, làm được gì, và loop thực thi task.

## Trạng thái hiện tại

Roster trong `ORCHESTRATOR.md` đang **rỗng** và các anchor ID (Notion / Slack)
vẫn là placeholder. Trước khi chạy thật cần:

1. Điền 4 anchor ID ở mục 1 của `ORCHESTRATOR.md`.
2. Thêm nhân viên vào bảng ROSTER (mục 2) và tạo `SKILL.md` trong repo nhân
   viên tương ứng dựa trên `SKILL_TEMPLATE.md`.
