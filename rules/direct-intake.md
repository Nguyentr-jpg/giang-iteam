# Rule — Yêu cầu đến THẲNG qua chat/Slack (kèm ảnh)

Áp dụng khi founder không tạo task trên ReMind mà **nhắn yêu cầu trực tiếp vào
phiên chat của Giang** — thường vì có ảnh chụp màn hình, log, hoặc tiện tay. Đây
là điểm mà Giang hay bị cám dỗ "làm luôn cho nhanh". KHÔNG. Vẫn chạy đúng quy
trình như `rules/roles.md` §4.

## 0. Nguyên tắc số 1
Yêu cầu qua chat = **một yêu cầu thô** như bất kỳ task nào khác, chỉ khác đường
vào. Giang **không tự thực thi**. Giang ghi nhận → phân tích/định tuyến → giao
nhân viên → Quân QC → báo cáo.

## 1. Ghi nhận thành task (để có dấu vết + lên board)
Ảnh không lưu được trong `rm_tasks`, nhưng phần chữ thì có. Tạo 1 row ghi nhận,
tóm tắt yêu cầu vào `description`, và **trích dẫn nguồn** (ảnh/chi tiết ở đâu):

```sql
insert into tool.rm_tasks (id, user_id, title, description, assignee, repo,
                           priority, status, extra, "order", created_at, updated_at)
values (
  '<id-12-ky-tu>', '<FOUNDER_USER_ID>',
  '<title ngắn gọn từ yêu cầu>',
  '<spec: mô tả vấn đề + ví dụ cụ thể founder đưa (vd tên order vs tên folder). '
    || 'Ảnh/chi tiết gốc: chat Giang IT ngày <ngày> (và/hoặc Slack thread).>',
  'unassigned',                     -- để Phase 0 định tuyến, hoặc điền thẳng agent nếu đã rõ
  '<repo sản phẩm nếu biết>', '<priority>', 'To Do',
  jsonb_build_object('source','direct-chat','giang_created', true),
  (extract(epoch from now())*1000)::bigint, now(), now()
);
```
Ghi lại **ví dụ cụ thể** founder đưa vào description — chúng là test case cho nhân
viên và Quân (vd: order `20260630-2035 Milan, San Antonio, TX 78258` vs folder
`… 78258 photos` phải khớp). Đừng để ví dụ chỉ nằm trong ảnh.

## 2. Phân tích & định tuyến (tầng điều phối, KHÔNG sửa code)
- Đọc yêu cầu + đọc **đủ** codebase để biết: thuộc repo nào, chuyên môn ai
  (backend→Đức, frontend→Linh), có cần chẻ nhỏ không, spec cần gì. Đọc để hiểu
  phạm vi thì được; **commit thay đổi thì không** (`rules/roles.md` §2).
- Mơ hồ / thiếu thông tin → hỏi lại founder ngay trong chat/Slack, giữ task `To Do`
  (theo `rules/intake.md`). Đừng đoán.
- Đủ rõ → lên plan/route (theo `rules/planning.md`). Vì founder đang ở đây, có thể
  xin duyệt ngay trong chat ("chốt vậy nhé?") thay vì chờ vòng sau.

## 3. Giao nhân viên thực thi (subagent) — KHÔNG tự làm
Được duyệt → chạy Phase 1 như thường (ORCHESTRATOR §4): claim task, spawn
**subagent nhân viên** đúng chuyên môn, đưa cho họ spec + ví dụ test, để **họ**
đọc sâu codebase và mở PR. Giang không tự viết code.

## 4. QC bởi Quân (bắt buộc)
PR xong → Giang set task `In Review` + `pr_link`. Quân QC (build/lint/test, verify
đúng các ví dụ founder đưa), đưa verdict pass/fail. Fail → tạo task fix cho nhân
viên. Không bỏ qua QC vì "việc nhỏ".

## 5. Báo cáo
Trả về chat/Slack: đã tạo task gì, giao ai, link PR, verdict QC. Founder review +
merge, kéo task sang Done.

---

### Vì sao không "làm luôn cho nhanh"
Ví dụ có thật: founder nhắn thẳng việc sửa matcher tên order/folder (kèm ảnh).
Nếu Giang tự đọc `orders.py` rồi tự sửa luôn — task xong nhưng: Đức (người hiểu
matcher nhất) không đụng tới, Quân không QC các ca lệch tên, và không có task/PR
nào để founder soi chuẩn. Đúng quy trình: Giang ghi task + ví dụ → Đức sửa & mở PR
→ Quân test các ca lệch → founder merge. Chậm hơn vài phút, nhưng đúng vai và có
kiểm chứng.
