# Rule — Intake incident ops (task do Ngọc báo)

Ngọc (agent giám sát của cs_agent) tự kiểm tra sức khoẻ các tool/subagent theo
chu kỳ. Phát hiện lỗi → Ngọc tạo task `ops_incident` lên board (`tool.rm_tasks`),
gán sẵn `assignee='giang'`. Ngọc KHÔNG fix — phát hiện + báo là hết vai; nhận
và xử incident là việc của Giang, theo rule này. Mỗi lần routine chạy, quét
incident **TRƯỚC** intake thường (Phase 0) và task thường (Phase 1) — hệ thống
đang hỏng thì việc mới xếp sau.

## 1. Lấy incident đang chờ

```sql
select id, title, priority, repo, extra
from tool.rm_tasks
where status = 'To Do' and deleted_at is null
  and extra->>'source' = 'ngoc'
  and extra->>'kind'   = 'ops_incident'
order by case priority when 'High' then 0 when 'Medium' then 1 else 2 end,
         created_at;
```

Không có → sang Phase 0/1 như thường. Có → xử từng cái theo mục 2.

## 2. Với mỗi incident

1. **Đọc evidence.** `extra.component` = monitor bị lỗi (vd `orders.scan`,
   `giang-runner`, `blog-pipeline`); `extra.evidence` = `{error, since(epoch ms),
   fail_count, monitor_kind}`; `extra.thread[]` có report của Ngọc (và các lần
   lỗi lặp — Ngọc append cùng task thay vì tạo trùng, khoá `extra.fingerprint`).
2. **Xác định repo đích.** Field `repo` của task là gợi ý của Ngọc (mặc định
   `cs_agent`). Đối chiếu với `component`/`error` để chắc chắn — vd lỗi phase
   của cs_agent → repo `cs_agent`; workflow blog fail → repo của blog.
3. **Đủ evidence để hành động?**
   - **Đủ** → claim (ORCHESTRATOR B4) rồi xử như một yêu cầu PHỨC TẠP hay DỄ
     theo `rules/roles.md`: chẻ việc theo `rules/planning.md` nếu cần, spawn
     đúng nhân viên (Đức backend / Linh frontend) fix, Quân QC theo
     `rules/loop.md`. Một fix một PR như mọi task.
   - **Mơ hồ / thiếu evidence** (không tái lập được, không rõ component, nghi
     báo nhầm) → áp `rules/intake.md`: KHÔNG claim, ghi `giang_question`,
     ping Slack hỏi founder, giữ `To Do`.
4. **Fix xong:** append thread
   `{by:'giang', kind:'done', text:'<đã fix gì, PR #>'}` (SQL append-only trong
   `rules/loop.md`) + set status theo lifecycle thường (`In Review` khi có PR —
   founder review/merge/đóng). Tick sau của Ngọc thấy monitor hồi phục sẽ tự
   append note `đã tự hồi phục` — Giang không cần báo lại cho Ngọc.

## 3. Ranh giới

- KHÔNG đóng task ops (founder đóng), KHÔNG xoá; Ngọc cũng không tự đóng.
- KHÔNG giao task cho `ngoc` — Ngọc không phải nhân viên, không nhận việc
  (xem `rules/roles.md`).
- Incident lặp: Ngọc dedup bằng `extra.fingerprint` (append thread vào task mở
  cùng khoá thay vì tạo mới) — nếu vẫn thấy 2 task mở trùng component, xử 1 cái,
  ghi note trỏ sang cái kia, hỏi founder trước khi đụng task thừa.
- Ưu tiên: incident `High` xử trước mọi task thường trong run đó.
