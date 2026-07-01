# Rule — Intake & Planning (Phase 0 của ORCHESTRATOR)

Đây là chi tiết cho **Phase 0** (mục 3 trong ORCHESTRATOR). Mục tiêu: founder chỉ
cần ném một *yêu cầu thô*, Giang tự phân tích → hỏi cho rõ hoặc lên plan kèm đề
xuất nhân viên → founder duyệt → Giang tạo task con đã gán đúng agent. Con người
giữ chốt "duyệt"; Giang không bao giờ tự đẻ task khi chưa được OK.

`rules/intake.md` là tiêu chí "task thế nào là đủ rõ để làm" — Phase 0 dùng lại y
nguyên tiêu chí đó khi quyết định *hỏi* hay *lên plan*.

---

## 1. YÊU CẦU THÔ LÀ GÌ

Một row `tool.rm_tasks` do founder tạo ở tab Team, `status='To Do'`, **chưa gán
nhân viên**: `assignee` là `unassigned` / rỗng / giá trị không khớp roster nào.
(Task founder đã tự gán sẵn agent thì bỏ qua Phase 0, chảy thẳng Phase 1.)

Trạng thái xử lý của yêu cầu thô lưu trong `extra.giang_stage`:

| giang_stage | Nghĩa                                        | Giang làm gì tiếp |
|-------------|----------------------------------------------|-------------------|
| (rỗng)      | Chưa đụng tới                                | Phân tích (mục 3) |
| `need_info` | Đã hỏi founder, chờ trả lời                  | Đọc trả lời → phân tích lại |
| `planned`   | Đã có plan, chờ founder duyệt                | Kiểm tín hiệu duyệt (mục 5) |
| `spawned`   | Đã duyệt & tạo task con xong                 | BỎ QUA vĩnh viễn |

---

## 2. ROUTING — CHỌN NHÂN VIÊN NÀO

Đọc `roster/*.md`, mỗi file có `assignee` + chuyên môn + mục "Khi nào định tuyến".
Khớp nội dung yêu cầu với chuyên môn:

- Backend / API / Supabase / migration / script / Python → `duc`.
- (Thêm nhân viên tương lai → thêm file roster, routing tự có thêm lựa chọn.)

Không có nhân viên nào hợp chuyên môn → KHÔNG bịa. Set `need_info`, hỏi founder
nên giao ai / có cần tuyển vai trò mới không. Giữ `To Do`.

---

## 3. PHÂN TÍCH: HỎI hay LÊN PLAN

Áp tiêu chí `rules/intake.md`. **Chặn lại để hỏi** (set `giang_stage='need_info'`)
nếu yêu cầu: mơ hồ (không rõ "xong là thế nào"), thiếu thông tin để quyết (A hay
B, thiếu link/repo), hoặc không rõ giao ai. **Đủ rõ** → dựng plan.

### 3a. Nếu HỎI
```sql
update tool.rm_tasks
set extra = coalesce(extra,'{}'::jsonb)
      || jsonb_build_object('giang_stage','need_info')
      || jsonb_build_object('giang_question','<1-3 cau hoi ngan, danh so>'),
    updated_at = now()
where id = '<req-id>';
```
Rồi **ping Slack đích danh founder** trong `SLACK_CHANNEL`: title yêu cầu + câu hỏi
+ cần founder làm gì. Giữ `To Do`. Founder trả lời bằng cách sửa `description`
trong app HOẶC reply trong Slack thread (mục 5 nói cách đọc). Lần chạy sau đọc lại
→ nếu đã đủ rõ thì chuyển sang lên plan.

### 3b. Nếu LÊN PLAN
Chia yêu cầu thành **1..N task con**, Giang tự quyết độ chia nhỏ theo nguyên tắc:
**mỗi task con = 1 việc rõ ràng, review được trong 1 PR nhỏ** (bám `rules/intake.md`
và `rules/review.md`). Đừng chẻ vụn quá (1 dòng/ task) cũng đừng gộp to (1 task ôm
cả hệ). Mỗi task con cần: `title`, `description` (spec đủ để nhân viên làm), `repo`
(repo sản phẩm — bắt buộc với task backend; thiếu thì đây là điểm phải HỎI),
`assignee` (mục 2), `priority`.

Lưu plan vào `extra` (KHÔNG tạo task con vội — chờ duyệt):
```sql
update tool.rm_tasks
set extra = coalesce(extra,'{}'::jsonb)
      || jsonb_build_object('giang_stage','planned')
      || jsonb_build_object('giang_plan', '<json plan>'::jsonb),
    updated_at = now()
where id = '<req-id>';
```
`giang_plan` dạng:
```json
{
  "summary": "Mô tả ngắn cách chia",
  "subtasks": [
    {"title":"...", "description":"...", "assignee":"duc",
     "repo":"cs_agent", "priority":"High"}
  ]
}
```
Rồi **ping Slack**: tóm tắt plan (số task con, mỗi con: title + assignee + repo) +
1 câu "Founder duyệt (reply 'ok' ở đây hoặc thao tác trong app) để mình tạo task."
Lưu `thread ts` của message này vào `extra.giang_slack_ts` để lần sau đọc lại đúng
thread. Giữ `To Do`.

---

## 4. TẠO TASK CON KHI ĐƯỢC DUYỆT (stage `planned` → `spawned`)

Với **mỗi** subtask trong `giang_plan.subtasks`, INSERT 1 row mới:

```sql
insert into tool.rm_tasks (id, user_id, title, description, assignee, repo,
                           priority, status, extra, "order", created_at, updated_at)
values (
  '<id-moi>',                       -- xem cách sinh bên dưới
  '<FOUNDER_USER_ID>',              -- anchor, bắt buộc (không có default)
  '<title>', '<description>', '<assignee>', '<repo>', '<priority>',
  'To Do',
  jsonb_build_object('parent','<req-id>','giang_created', true),
  (extract(epoch from now())*1000)::bigint,   -- order: mốc thời gian ms, giống app
  now(), now()
);
```

**Sinh `id`:** `rm_tasks.id` là text không có default (app tự sinh, ví dụ
`9y135buzrff0`). Giang sinh 1 chuỗi 12 ký tự `[0-9a-z]` ngẫu nhiên cho mỗi task
con, đảm bảo khác nhau. Nếu nghi trùng, đổi vài ký tự rồi thử lại.

Sau khi tạo hết, đóng vòng cho yêu cầu gốc:
```sql
update tool.rm_tasks
set status = 'In Progress',       -- đang được triển khai qua các task con
    extra = coalesce(extra,'{}'::jsonb)
      || jsonb_build_object('giang_stage','spawned')
      || jsonb_build_object('giang_children', '<json mảng id con>'::jsonb),
    updated_at = now()
where id = '<req-id>' and coalesce(extra->>'giang_stage','') = 'planned';
```
`and ... = 'planned'` là chốt chống đẻ trùng: nếu run khác đã spawn (stage đổi rồi)
thì update 0 dòng → KHÔNG insert lại. **Trước khi insert, luôn kiểm lại stage vẫn
là `planned`; nếu đã `spawned` thì bỏ qua.**

Task con (assignee ∈ roster, `To Do`) sẽ được **Phase 1** nhặt lên thực thi bình
thường. Founder đóng yêu cầu gốc (`In Progress` → `Done`) khi mọi task con xong —
Giang không tự đóng (xem `rules/task-lifecycle.md`).

> **Ngoại lệ 1-task:** nếu plan chỉ 1 subtask, được phép gán tại chỗ thay vì tạo
> con: `update ... set assignee='<agent>', repo='<repo>', description='<spec>',
> extra = ... || jsonb_build_object('giang_stage','spawned') where id='<req-id>'`.
> Khi đó chính yêu cầu gốc thành task thực thi, Phase 1 nhặt luôn.

---

## 5. NHẬN TÍN HIỆU DUYỆT — CHẤP NHẬN CẢ 2 KÊNH

Ở stage `planned`, coi là **ĐÃ DUYỆT** nếu gặp 1 trong các tín hiệu:

**Kênh app (ReMind):**
- Founder đổi `assignee` của yêu cầu gốc từ `unassigned` sang tên 1 agent trong
  roster, HOẶC
- Founder kéo thẻ yêu cầu gốc sang cột `In Progress` (status đổi `To Do`→`In Progress`).

**Kênh Slack:**
- Đọc thread `extra.giang_slack_ts` trong `SLACK_CHANNEL`. Founder reply chứa ý
  đồng ý ("ok", "duyệt", "đồng ý", "chốt", "approve", "yes", "làm đi") → duyệt.

Nếu founder reply/sửa mô tả nhưng KHÔNG phải đồng ý (bổ sung yêu cầu, đổi ý, sửa
scope) → coi là input mới: quay lại mục 3, cập nhật `giang_plan` rồi ping lại bản
plan mới. Nếu chưa có phản hồi nào → giữ nguyên, **không ping lại** (tránh spam),
sang yêu cầu kế.

Khi đã xác định duyệt → làm mục 4. Nếu founder đã tự đổi assignee sang agent (kênh
app) thì đó vừa là duyệt vừa là chỉ định agent — tôn trọng lựa chọn của founder,
ghi đè assignee đề xuất trong plan.

---

## 6. NGUYÊN TẮC
- Chưa duyệt → tuyệt đối không tạo task con.
- Một yêu cầu thô, mỗi lần chạy tiến đúng 1 bước (hỏi / plan / spawn), không nhảy cóc.
- `giang_stage` là nguồn sự thật chống trùng — luôn đọc & cập nhật đúng.
- Không xoá gì. Chỉ tạo/cập nhật. Mọi thay đổi kèm `updated_at=now()`.
- Không chắc giao ai / repo nào / muốn gì → HỎI, đừng đoán.
