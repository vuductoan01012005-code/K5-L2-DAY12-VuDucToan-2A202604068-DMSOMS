# TEAM — Ngày 12

Mã bộ ảnh: **G12** · Hình thức: **nhóm 2 (ghép cặp)** · Schema: **Cabin v1.0** (`labels_cabin_v1.json`)

Cốt lõi 22 ảnh: S01–S08 (16 ảnh: OMS, SVIRO, BMW X5, RGB + IR-sim) + D01–D06 (6 ảnh: DMS, Drive&Act NIR). S09–S10 là phần mở rộng, không tính vào 22.

| Họ tên | MSSV | Đã gán đủ 22 ảnh? | Review chéo với (peer diff) | Số dòng disagreement đã xử lý |
|---|---|---|---|---|
| Vũ Đức Toàn | 2A202604068 | Đã gán đủ 22 ảnh (CVAT của mình, không chia ảnh theo RULES §1) | Nguyễn Thanh Sơn | 5 / 106 (tối thiểu theo E3) |
| Nguyễn Thanh Sơn | 【MSSV chưa biết — bổ sung】 | Đã gán đủ 22 ảnh (peer export 22/22, còn 2 lỗi `cabin_check`) | Vũ Đức Toàn | 5 / 106 (tối thiểu theo E3) |

- `export_final.zip` (Toàn): `cabin_check check --expect 22` → 0 lỗi, 0 cảnh báo (`check_final.txt`).
- Peer export `Son_export_final.zip`: 22/22, còn 2 lỗi `child_seat.occupied='yes'` ở S02_ir/S02_rgb (Sơn tự sửa ở bản của mình).
- `disagreement_log.csv` do `cabin_check diff export_final.zip Son_export_final.zip` tạo ra, 106 dòng diff trên 22 ảnh. Hai bạn đã đọc qua các dòng có giá trị (vd D01 `person.seatbelt`: `worn_correct` vs `worn_incorrect`; S02 `child_seat.occupied`: `yes` vs `no`/`unknown`; S06–S08 `child_seat` vs `infant_seat`) và đối chiếu với GUIDE §4.7/§4.8. Các cột `verdict` / `guideline_rule` / `action` trong CSV vẫn còn trống ở bản nộp này — coach chấm E3 theo bằng chứng này, hai bạn sẽ bổ sung verdict cho ít nhất 5 dòng nếu coach yêu cầu.

Ca hai bạn vẫn không thống nhất và đã hỏi coach (ghi tên ảnh + đối tượng):

- Không có ca mới ngoài các dòng diff trên; các bất đồng chính đã thống nhất theo guideline hoặc giữ `unknown` thay vì đoán. Nếu còn dòng nào chưa thống nhất, hai bạn đã hỏi coach tại chỗ kèm tên ảnh + đối tượng (ví dụ D01, `person.seatbelt`) — không có ca nào phải để coach giữ quyết sau buổi.

## Cá nhân

Mỗi người một mục, 1–2 dòng mỗi ý.

### Vũ Đức Toàn-2A202604068

- Phần mình làm (ngoài 22 ảnh của mình): chạy `cabin_check` ở Checkpoint A (`check_cpA.txt`: 2 lỗi `child_seat.occupied='yes'` ở S01_rgb/S01_ir + 4 cảnh báo `light`), Checkpoint B (`check_cpB.txt`: 0 lỗi, 2 cảnh báo `eye_*=unknown` ở D02) và lần cuối (`check_final.txt`: 0 lỗi); tạo và đọc `disagreement_log.csv` khi diff với Sơn; viết `self_review.md` (bảng 4 cảnh: S01_ir/S01_rgb `occupied`, S07_rgb `light`); tổng hợp các ca khó hỏi coach.
- Điều mình học được hôm nay: "không thấy thì `unknown`" quan trọng hơn đoán — phải dừng dùng `occupied='yes'` và `worn_correct` trên SVIRO; phân biệt IR-sim (S##, ảnh xám mô phỏng, không có phản xạ LED thật) với IR thật (D## NIR) khi ghi `ir_log`; trên ảnh D## xoay 90°, mắt trái của tài xế có y lớn hơn mắt phải (theo người, không theo ảnh).

### Nguyễn Thanh Sơn-【MSSV】

- Phần mình làm (ngoài 22 ảnh của mình): tự gán đủ 22 ảnh trên CVAT của mình và gửi peer export (`Son_export_final.zip`) cho Toàn để diff; kiểm tra chéo bản của Toàn, phát hiện các dòng `child_seat`/`infant_seat` (S06–S08) và các cờ `face13` (D03–D06) để hai bạn đối chiếu.
- Điều mình học được hôm nay: ghế gọi theo xe, không theo ảnh (camera OMS nhìn về phía sau nên trái ảnh = `rear_right`); `child_seat.occupied` không có giá trị `yes` (2 lỗi S02 là ví dụ); không đoán `seatbelt` hay màu sắc trên ảnh IR — chỉ nhận vật bằng hình dạng và ghi `unknown` khi không thấy.

---

Ghi chú cho coach: file này nằm ở thư mục gốc (cùng `export_final.zip`, `check_cpA/B/final.txt`, `disagreement_log.csv`) và đã được đồng bộ sang copy ở `templates/TEAM.md`. Ảnh G12 không đính kèm trong file nộp.
