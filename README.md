# giang-iteam — Repo điều hành team IT (Giang)

"Não" của hệ agent điều phối task. Routine "Giang IT" trong Claude Code trỏ
Instructions tới `ORCHESTRATOR.md` của repo này.

## Cấu trúc
```
ORCHESTRATOR.md     # vòng lặp điều phối (ngắn, ổn định)
roster/             # mỗi nhân viên 1 file (duc.md ...)
rules/              # quy tắc: planning, intake, task-lifecycle, review
playbooks/          # quy trình việc lặp lại (đọc khi khớp)
logs/               # nhật ký mỗi lần chạy
```

## Luồng hoạt động
Mỗi lần chạy Giang làm 2 phase:

**Phase 0 — Intake & Planning** (mới): founder chỉ cần ném *yêu cầu thô* (task chưa
gán nhân viên).
1. Giang phân tích → **hỏi lại nếu chưa rõ**, hoặc **lên plan kèm đề xuất nhân viên**.
2. Founder duyệt (reply "ok" trong Slack, hoặc thao tác trong app ReMind).
3. Giang **tạo task con và gán đúng agent** → chảy vào Phase 1. Chi tiết: `rules/planning.md`.

**Phase 1 — Thực thi**: với task đã gán nhân viên.
4. Tra `roster/` theo `assignee` → giao nhân viên (subagent) → mở PR.
5. Cập nhật status, báo Slack, ghi `logs/`.
6. Founder review + merge PR → kéo task sang Done.

(Founder vẫn có thể tự tạo task đã-gán-sẵn ở tab Team → bỏ qua Phase 0.)

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
Slack channel `C0BDXTY41E3`. GitHub owner `Nguyentr-jpg`. `FOUNDER_USER_ID` (dùng
khi Giang tạo task mới — `rm_tasks.user_id` không có default).
