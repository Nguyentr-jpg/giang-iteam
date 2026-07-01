# Logs

Nhật ký mỗi lần Giang chạy (B8 trong ORCHESTRATOR). Để founder soi lịch sử điều
phối khi cần debug.

## Cách ghi
Mỗi run append 1 file hoặc 1 dòng theo ngày, ví dụ `logs/2026-07.md`:

```
## 2026-07-01 18:00 (run)
- Đọc: 3 task To Do
- In Review: 1 (task abc123 → PR #12)
- Lỗi: 0
- Bỏ qua (thiếu người): 1 (task "Test X", assignee=unassigned)
- Chặn chờ founder: 1 (task "Làm lại auth" — quá lớn, đã ping Slack)
```

Giữ ngắn gọn. Không ghi dữ liệu nhạy cảm (secret, token).
