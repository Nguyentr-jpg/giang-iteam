# Rule — Review PR của nhân viên

## Không auto-merge repo nhân viên
Repo nhân viên (cs_agent, v.v.) KHÔNG bật auto-merge. PR do Giang/nhân viên mở
phải founder review và merge tay. (Khác repo `remind` — repo đó có auto-merge
riêng cho việc của chính founder, không áp cho việc agent.)

## Mỗi PR phải có mô tả gồm
- Task gốc: id + title.
- Tóm tắt thay đổi.
- Cách kiểm thử (đã test gì, kết quả).
- Rủi ro / phần cần review kỹ — đặc biệt nếu có migration DB.

## Một task một PR
Không gộp nhiều task vào 1 PR. PR nhỏ, dễ review, dễ revert nếu sai.

## Founder review xong
Merge PR → kéo task In Review → Done (xem task-lifecycle.md). Nếu PR cần sửa:
comment trên GitHub, hoặc tạo task mới mô tả phần sửa.
