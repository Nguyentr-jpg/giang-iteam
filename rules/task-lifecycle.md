# Rule — Vòng đời task & ai chuyển trạng thái

4 trạng thái trong `tool.rm_tasks.status`:

| Trạng thái   | Nghĩa                              | Ai chuyển vào |
|--------------|------------------------------------|---------------|
| To Do        | Đã chốt, chờ làm                   | Founder (tạo ở tab Team) |
| In Progress  | Đang được nhân viên làm            | Giang (claim, B4) |
| In Review    | Đã có PR, chờ founder duyệt        | Giang (B6, sau khi có PR) |
| Done         | PR đã merge, task đóng             | **Founder** |

## Quy tắc chuyển
- **To Do → In Progress**: Giang claim (B4). Chỉ ăn khi còn To Do (chống trùng).
- **In Progress → In Review**: Giang, khi subagent mở PR xong. Điền `pr_link`.
- **In Progress → To Do**: Giang, khi subagent lỗi (nhả khoá, ghi `giang_error`).
- **In Review → Done**: **FOUNDER làm tay.** Founder review + merge PR trên GitHub,
  rồi tự kéo task sang Done ở tab Team. Giang KHÔNG tự đóng task.

## Vì sao Giang không tự đóng
"In Review" = việc của máy đã xong, chờ mắt người. Chất lượng PR do founder duyệt.
Giang tự merge/đóng = mất chốt kiểm soát. Giữ In Review cho tới khi founder quyết.

## (Tương lai) tự động hoá
Sau này có thể thêm webhook GitHub: PR merged → tự set task Done. Chưa làm ở
giai đoạn này — giữ thủ công cho an toàn.
