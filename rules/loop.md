# Rule — Vòng làm việc trên board: hub-mediated, board-logged (hybrid A+B)

Cách Giang chạy dây chuyền **làm → kiểm độc lập → sửa** dùng ReMind (`rm_tasks`)
làm **bộ nhớ chung + hộp thư**, để founder soi được cả cuộc trao đổi giữa các
agent live. Đây là bản lai: **native subagent (A)** làm cơ bắp, **board (B)** giữ
bền + dấu vết + cho founder chốt.

## 1. Nguyên tắc cốt lõi
- **Giang là HUB.** Nhân viên KHÔNG gọi thẳng nhau. Đức làm xong → ghi report lên
  board; Giang đọc → điều Quân; Quân verdict lên board; Giang đọc → điều lại Đức
  hoặc tổng hợp. Một bộ não giữ toàn cảnh → loop bó được, báo cáo cuối trọn vẹn.
- **Board là hộp thư.** Mọi trao đổi ghi vào `extra.thread[]` dạng **report NGẮN**
  (vài dòng: đã làm gì / lỗi gì / sửa gì), KHÔNG đổ transcript. Report ngắn là hợp
  đồng giữa agent — giữ ngữ cảnh mỗi con nhẹ.
- **Kiểm độc lập là bắt buộc cho việc không tầm thường** — dù tay ai viết bản vá
  (kể cả Giang tự sửa, xem `rules/roles.md`). Ai viết ít quan trọng; có người độc
  lập kiểm mới quan trọng.
- **Mặc định in-process**: Giang dàn subagent Đức→Quân→Đức trong CÙNG một run (rẻ,
  nhanh), vẫn ghi board cho founder xem. Chỉ tách cross-process khi task quá dài
  một run không kham, hoặc chạy lúc founder vắng.

## 2. Hợp đồng dữ liệu (ghi vào task.extra — KHÔNG cần cột mới)
```jsonc
extra.thread = [               // hộp thư, append-only, sắp theo ts
  { "ts": 1730000000000,       // mốc ms
    "by": "giang|duc|linh|quan|founder",
    "kind": "assign|report|qc|fix|note|done",
    "text": "<report ngắn>",
    "verdict": "pass|fail",    // chỉ khi kind=qc
    "round": 2 }               // vòng QC hiện tại (nếu có)
]
extra.qc_round = 2             // vòng QC đang chạy
extra.qc_max   = 3             // trần vòng (mặc định 3)
extra.qc_state = "pending|fail|pass|escalated"
extra.qc_note  = "<vì sao escalate / tóm tắt bất đồng>"   // khi escalated
```
ReMind đọc các key này để hiện timeline + badge (`🔁 QC vòng x/y`, `✅ QC pass`,
`⚠ QC bí — cần bạn quyết`). Xem `remind/migration_team.sql` (phần ghi chú extra).

**Cách append 1 dòng thread an toàn (giữ dòng cũ):**
```sql
update tool.rm_tasks
set extra = coalesce(extra,'{}'::jsonb)
      || jsonb_build_object('thread',
           coalesce(extra->'thread','[]'::jsonb) || jsonb_build_array(
             jsonb_build_object('ts', (extract(epoch from now())*1000)::bigint,
               'by','quan','kind','qc','verdict','fail','round',2,
               'text','<mô tả ngắn lỗi>'))),
    updated_at = now()
where id = '<task-id>';
```

## 3. Luồng chuẩn (mediated)
```
1. Giang phân tích → chẻ việc (rules/planning.md), tạo task con.
   append thread: {by:giang, kind:assign, text:"giao Đức: <spec + tiêu chí>"}
2. Giang spawn Đức (subagent) thực thi → Đức mở PR.
   append thread: {by:duc, kind:report, text:"đã làm X, PR #.., cách test .."}
   Giang set task In Review + pr_link.
3. Giang spawn Quân QC (đưa PR + tiêu chí + các ví dụ founder đưa).
   Quân verdict: append {by:quan, kind:qc, round:N, verdict:pass|fail, text:".."}
   - FAIL → Giang tăng qc_round, qc_state='fail', spawn lại Đức KÈM ghi chú Quân.
            append {by:duc, kind:fix, text:".."} rồi quay lại bước 3. (đếm vòng!)
   - PASS → qc_state='pass'.
4. Giang tổng hợp: append {by:giang, kind:done, text:"tóm tắt trọn task"} →
   báo founder (chat/Slack) kèm link PR + verdict. Founder review + merge.
```

## 4. Phanh loop (BẮT BUỘC)
- Trước mỗi vòng QC mới, kiểm `qc_round`. Nếu `qc_round >= qc_max` (mặc định 3)
  mà chưa pass → **DỪNG**: set `qc_state='escalated'`, ghi `qc_note` (tóm tắt bất
  đồng / chỗ kẹt), append thread `{by:giang, kind:note, text:"escalate: .."}`,
  **ping founder** đích danh. KHÔNG lặp tiếp, KHÔNG tự quyết thay founder.
- Không bao giờ để Đức↔Quân ping-pong vô hạn. Trần vòng là cứng.

## 5. Máy trạng thái — tác giả (`by`) vs người GHI board
Trường `by` đánh dấu **tác giả nội dung**; cột ai chạy lệnh ghi board thì tuỳ ai
có kết nối Supabase (giữ nhân viên đơn giản):

| Nội dung | Tác giả (`by`) | Ai GHI vào board |
|----------|----------------|------------------|
| tạo task, assign, tăng qc_round, qc_state, escalate, done, tổng hợp | giang | **Giang** |
| kind=report / kind=fix (đã làm gì) | duc/linh | **Giang** ghi hộ từ report subagent trả về — Đức/Linh KHÔNG đụng board |
| kind=qc + verdict | quan | **Quân tự ghi** (Quân vốn đã ghi board) |
| review & merge PR, đóng task (In Review→Done) | founder | founder |

- **Đức/Linh chỉ TRẢ report ngắn cho Giang; Giang append `report`/`fix` giúp.**
  Quân tự append `qc` (nhất quán với việc Quân vốn ghi `extra.quan_verdict`).
- `extra.quan_verdict`/`quan_reviewed_sha` vẫn giữ làm **marker chống review trùng**;
  dòng `qc` trong `thread` là bản cho founder soi. Hai cái bổ trợ, không mâu thuẫn.
- Chống trùng vẫn dùng claim (ORCHESTRATOR B4). `extra.thread` là append-only —
  luôn `|| jsonb_build_array(...)`, không ghi đè mảng cũ.
- Mọi update kèm `updated_at=now()` để Realtime đẩy timeline về UI.

## 6. Vì sao mediated chứ không peer-to-peer
Đức tự gọi Quân, Quân tự gọi Đức nghe "tự chủ" hơn nhưng: không ai đếm vòng →
loop khó chặn; không con nào giữ toàn cảnh → báo cáo cuối rời rạc; mỗi nhân viên
phải mang logic điều phối. Hub (Giang) đơn giản, kiểm soát được, hợp bản chất AI
(hub rẻ và nhanh). Peer-to-peer chỉ cân nhắc khi đã có nhu cầu thật + cơ chế đánh
thức cross-process ổn định.
