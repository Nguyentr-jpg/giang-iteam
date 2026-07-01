# Rule — Intake & khi nào chặn task để hỏi founder

Áp dụng ở B3 của ORCHESTRATOR. Mục tiêu: không để nhân viên làm mù một task
không đủ rõ, và không đẻ ra PR khổng lồ khó review.

## Task hợp lệ để giao ngay
- Một việc rõ ràng, phạm vi nhỏ (một PR review được trong vài phút).
- Có đủ thông tin trong `title` + `description` để bắt tay làm.
- Thuộc đúng chuyên môn của nhân viên được gán.

## Khi nào Giang CHẶN task (không claim, giữ To Do)
Chặn nếu 1 trong các dấu hiệu:
- **Mơ hồ**: không rõ muốn gì, thiếu tiêu chí "xong là thế nào".
- **Quá lớn**: một task ôm nhiều việc ("làm lại toàn bộ auth"). Cần founder chẻ nhỏ.
- **Thiếu thông tin**: cần quyết định A hay B, cần link/tài nguyên chưa có.
- **Lệch chuyên môn**: assignee không hợp loại việc (vd task frontend gán `duc`).

## Cách chặn (làm đủ 2 việc)
1. Ghi câu hỏi/lý do vào task:
   ```sql
   update tool.rm_tasks
   set extra = coalesce(extra,'{}'::jsonb)
     || jsonb_build_object('giang_question','<cau hoi ngan gon>'),
       updated_at = now()
   where id = '<task-id>';
   ```
2. **Ping Slack đích danh founder** trong `SLACK_CHANNEL`, nêu rõ:
   task title + câu hỏi + cần founder làm gì (sửa description / chẻ nhỏ / đổi assignee).

Giữ task ở `To Do`. Founder sửa `description` trong tab Team, lần chạy sau Giang
đọc lại.

## Nguyên tắc
Thà chặn và hỏi 1 câu còn hơn làm sai cả task rồi phải mở lại. Không đoán ý founder.
