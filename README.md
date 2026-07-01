# giang-iteam

Repo gốc của **Giang** — orchestrator điều phối task cho team IT.

## Nội dung

- **`ORCHESTRATOR.md`** — playbook của Giang: mỗi lần routine chạy, đọc task
  `To Do` trong Supabase (`tool.rm_tasks`), giao cho đúng nhân viên (subagent),
  mở PR trên GitHub, cập nhật trạng thái và báo cáo Slack.
- **`SKILL_TEMPLATE.md`** — template file `SKILL.md` đặt ở gốc repo của mỗi
  nhân viên. Định danh nhân viên là ai, làm được gì, và loop thực thi task.

## Trạng thái hiện tại

Anchor đã điền đủ (toạ độ Supabase + `SLACK_CHANNEL = C0BDXTY41E3`). Roster
trong `ORCHESTRATOR.md` vẫn **rỗng**. Trước khi thực thi task thật cần:

1. Thêm nhân viên vào bảng ROSTER (mục 2) và tạo `SKILL.md` trong repo nhân
   viên tương ứng dựa trên `SKILL_TEMPLATE.md`, rồi set `assignee` của task
   khớp tên đó.
2. Routine "Giang IT" (Claude Code UI): connectors bật **Supabase + Slack +
   GitHub** (bỏ Notion); Instructions vẫn trỏ `@giang-it/ORCHESTRATOR.md`;
   Trigger giữ Schedule 2x/ngày.
3. Phase 1 remind tạo bảng `tool.rm_tasks` thật để pipeline có dữ liệu đọc.
