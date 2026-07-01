# giang-iteam — Repo điều hành team IT (Giang)

"Não" của hệ agent điều phối task. Routine "Giang IT" trong Claude Code trỏ
Instructions tới `ORCHESTRATOR.md` của repo này.

## Cấu trúc
```
ORCHESTRATOR.md     # vòng lặp điều phối (ngắn, ổn định)
roster/             # mỗi nhân viên 1 file (duc.md ...)
rules/              # quy tắc: intake, task-lifecycle, review
playbooks/          # quy trình việc lặp lại (đọc khi khớp)
logs/               # nhật ký mỗi lần chạy
```

## Luồng hoạt động
1. Founder tạo task ở tab "Team" của app ReMind → ghi vào `tool.rm_tasks` (Supabase).
2. Routine Giang chạy 2 lần/ngày (+ Run tay khi cần): đọc task To Do.
3. Tra `roster/` theo field `assignee` → giao nhân viên (subagent) → mở PR.
4. Cập nhật status, báo Slack, ghi `logs/`.
5. Founder review + merge PR → kéo task sang Done.

## Thêm nhân viên mới
1. Tạo `roster/<ten>.md` (copy mẫu từ `roster/duc.md`): đặt `assignee`, repo, skill file, chuyên môn.
2. Đặt `SKILL.md` chi tiết vào gốc repo của nhân viên đó.
3. Xong. KHÔNG sửa ORCHESTRATOR.md — nó tự đọc mọi file trong roster/.

## Checklist cấu hình Routine (Claude Code UI)
- Name: Giang IT
- Instructions: trỏ `@giang-iteam/ORCHESTRATOR.md`
- Trigger: Schedule, 2 lần/ngày (vd cron `0 2,11 * * *` = 9h & 18h giờ VN)
- Connectors: **Supabase + Slack + GitHub** (KHÔNG Notion)
- Repos gắn kèm: giang-iteam + các repo nhân viên (cs_agent ...)

## Anchors (trong ORCHESTRATOR.md)
Supabase project `wwevdsijedreuxqnbtyt`, schema `tool`, bảng `rm_tasks`/`rm_projects`.
Slack channel `C0BDXTY41E3`. GitHub owner `Nguyentr-jpg`.
