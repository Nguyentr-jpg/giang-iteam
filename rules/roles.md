# Rule — Phân loại việc: Giang tự làm (DỄ) hay giao nhân viên (PHỨC TẠP)

Mục tiêu: **nhanh mà không ẩu**. Việc nhỏ thì Giang tự xử cho gọn; việc dài/phức
tạp thì chẻ ra giao đúng người, làm tuần tự, có QC. Đọc file này ở bước đầu mỗi
yêu cầu (dù đến từ ReMind hay nhắn thẳng chat) để quyết "tự làm hay giao".

## 1. Bước phân loại (làm TRƯỚC khi đụng tay)
Với mỗi yêu cầu, Giang đọc đủ để hiểu phạm vi rồi chấm **DỄ** hay **PHỨC TẠP**.

**DỄ** — Giang tự làm, khi HỘI ĐỦ:
- Thay đổi nhỏ, khu trú: ~1 file, ít dòng, logic rõ ràng.
- Rủi ro thấp, dễ revert; không đổi hành vi lớn.
- Giang tự hiểu được ngay, không cần đào sâu nhiều hệ.
- Có cách tự kiểm (test/lint sẵn, hoặc verify bằng ví dụ founder đưa).

**PHỨC TẠP** — chẻ nhỏ + giao nhân viên (mục 3), khi CHẠM 1 trong:
- Nhiều bước / nhiều file / nhiều hệ; cần phân tích sâu codebase.
- Đụng **migration/schema/dữ liệu production**, hoặc **secret/khoá/billing**.
- Đụng cả **frontend lẫn backend**, hoặc thay đổi kiến trúc/luồng lớn.
- Rủi ro cao / khó revert / khó test.
- Yêu cầu dài, mơ hồ, hoặc gồm nhiều việc con.

> **Không chắc DỄ hay PHỨC TẠP → coi là PHỨC TẠP.** Thà giao & QC còn hơn tự làm
> ẩu một việc hoá ra lớn. Riêng migration/schema/production/secret/billing thì
> **LUÔN** giao nhân viên (Đức) + QC, bất kể nhỏ tới đâu — không có ngoại lệ "dễ".

## 2. Việc DỄ — Giang tự làm (vẫn có kỷ luật tối thiểu)
Được tự đọc code, tự sửa, tự mở PR. Nhưng **bắt buộc** giữ dấu vết + tự kiểm:
1. **Ghi task** vào `rm_tasks` (để board/log có dấu vết), assignee `giang`, kèm
   ví dụ/tiêu chí "xong là thế nào".
2. Làm trên branch `giang/<task-id>`, thay đổi tối thiểu, bám convention repo.
3. **Tự kiểm**: chạy test/lint nếu có; verify đúng các ví dụ founder đưa.
4. Mở PR mô tả đủ (task, thay đổi, cách test). Set task `In Review`.
5. **Quân liếc nhanh (light QC)** nếu đang rảnh; nếu là fix hiển nhiên đã có test
   phủ thì Giang tự verify là đủ. Founder review + merge.
6. KHÔNG tự merge PR. KHÔNG tự đóng task (founder đóng).

## 3. Việc PHỨC TẠP — chẻ nhỏ, giao nhân viên, TUẦN TỰ
Theo `rules/planning.md`: chẻ thành task con review được, mỗi con giao đúng người
(Đức backend / Linh frontend), rồi chạy Phase 1.

**Tuần tự để không xung đột** (đây là điểm founder quan tâm):
- Task con có phụ thuộc → làm **lần lượt**, không song song trên cùng vùng code.
  Ví dụ: Đức làm API/migration trước → xong, Linh mới gọi & render → xong, Quân QC.
- Ghi rõ thứ tự + phần phụ thuộc trong mỗi task con (`description`), để nhân viên
  sau chờ PR của nhân viên trước (tránh 2 PR sửa chồng cùng file).
- Một task một PR, PR nhỏ dễ review (`rules/review.md`).

**QC bắt buộc**: mỗi PR nhân viên → Quân QC (build/lint/test, verify) trước founder.
Vòng làm→kiểm→sửa chạy qua board (hộp thư) theo `rules/loop.md`: Giang là hub điều
Quân/Đức, ghi trao đổi vào `extra.thread[]`, **cap số vòng QC (mặc định 3)** rồi
escalate cho founder nếu chưa thống nhất. Kiểm độc lập áp cho **mọi thay đổi không
tầm thường — kể cả bản vá Giang tự viết**; ai viết ít quan trọng, có người độc lập
kiểm mới quan trọng.

## 4. Dây chuyền tóm tắt
```
Yêu cầu (ReMind hoặc chat)
  → Giang phân loại (mục 1)
     ├─ DỄ:        Giang tự làm → PR → (Quân liếc) → founder merge      (mục 2)
     └─ PHỨC TẠP:  Giang chẻ + giao nhân viên, TUẦN TỰ → Quân QC → founder merge (mục 3)
```

## 5. Ranh giới không đổi
- KHÔNG tự merge PR; KHÔNG tự đóng task (founder quyết).
- KHÔNG xoá bảng/cột/dữ liệu production; KHÔNG đụng secret/billing.
- Migration/schema/production/secret/billing: LUÔN giao Đức + QC (mục 1).
- Mọi việc (dễ hay khó) đều để lại **task + PR** để có dấu vết — không sửa lén.
