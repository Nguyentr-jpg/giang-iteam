# Rule — Yêu cầu đến THẲNG qua chat/Slack (kèm ảnh)

Áp dụng khi founder không tạo task trên ReMind mà **nhắn yêu cầu trực tiếp vào
phiên chat của Giang** — thường vì có ảnh chụp màn hình, log, hoặc tiện tay.

## 0. Nguyên tắc
Yêu cầu qua chat = **một yêu cầu như mọi yêu cầu khác**, chỉ khác đường vào. Vẫn
chạy bước **phân loại độ khó** ở `rules/roles.md` §1 rồi mới quyết tự làm hay giao:
- **DỄ** (nhỏ, khu trú, rủi ro thấp, có cách kiểm) → Giang tự check & sửa
  (`rules/roles.md` §2). Nhanh gọn, nhưng vẫn ghi task + mở PR + tự verify.
- **PHỨC TẠP** (dài, nhiều hệ, migration/schema/production/secret, hoặc FE+BE) →
  chẻ nhỏ, giao nhân viên, làm **tuần tự**, Quân QC (`rules/roles.md` §3).
- Không chắc → coi là PHỨC TẠP.

## 1. Luôn ghi nhận thành task (để có dấu vết + lên board)
Dù DỄ hay PHỨC TẠP, ảnh không lưu được trong `rm_tasks` nhưng phần chữ thì có.
Tạo 1 row ghi nhận, tóm tắt yêu cầu vào `description`, **trích nguồn ảnh**:

```sql
insert into tool.rm_tasks (id, user_id, title, description, assignee, repo,
                           priority, status, extra, "order", created_at, updated_at)
values (
  '<id-12-ky-tu>', '<FOUNDER_USER_ID>',
  '<title ngắn gọn từ yêu cầu>',
  '<spec: mô tả vấn đề + ví dụ cụ thể founder đưa. Ảnh/chi tiết gốc: chat Giang IT '
    || 'ngày <ngày> (và/hoặc Slack thread).>',
  '<giang nếu tự làm | unassigned nếu để định tuyến | tên agent nếu đã rõ>',
  '<repo sản phẩm nếu biết>', '<priority>', 'To Do',
  jsonb_build_object('source','direct-chat','giang_created', true),
  (extract(epoch from now())*1000)::bigint, now(), now()
);
```
**Luôn chép ví dụ cụ thể founder đưa vào `description`** — chúng là test case cho
Giang tự verify (việc dễ) hoặc cho nhân viên + Quân (việc phức tạp). Đừng để ví dụ
chỉ nằm trong ảnh. Ví dụ: order `20260630-2035 Milan, San Antonio, TX 78258` phải
khớp folder `… 78258 photos` (lệch đuôi/dấu).

## 2. DỄ → Giang tự làm
Đọc code liên quan, sửa tối thiểu, chạy test/lint nếu có, **verify đúng các ví dụ**
founder đưa, mở PR mô tả đủ, set task `In Review`. Founder review + merge. (Chi
tiết kỷ luật tối thiểu: `rules/roles.md` §2.) Vì founder đang ở chat, báo lại ngay
kết quả + link PR.

## 3. PHỨC TẠP → giao nhân viên, tuần tự
Mơ hồ/thiếu tin → hỏi lại ngay trong chat, giữ `To Do` (`rules/intake.md`). Đủ rõ
→ chẻ task con, giao đúng người, làm **tuần tự** theo phụ thuộc (Đức API/migration
trước → Linh gọi/render sau → Quân QC), ghi rõ thứ tự để không xung đột. Vì founder
đang ở đây, có thể xin duyệt plan ngay trong chat thay vì chờ vòng sau.

## 4. Báo cáo
Trả về chat/Slack: đã tạo task gì, tự làm hay giao ai, link PR, verdict QC (nếu
có). Founder review + merge, kéo task sang Done.
