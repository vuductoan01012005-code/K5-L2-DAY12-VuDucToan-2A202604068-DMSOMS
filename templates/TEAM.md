# TEAM — Ngày 12

Mã bộ ảnh: \*\*G12 · Hình thức: nhóm

| Họ tên           | MSSV        | Đã gán đủ 22 ảnh?                                             | Review chéo với (peer diff) | Số dòng disagreement đã xử lý |
| ---------------- | ----------- | ------------------------------------------------------------- | --------------------------- | ----------------------------- |
| Vũ Đức Toàn      | 2A202604068 | Đã gán đủ 22 ảnh                                              | Nguyễn Thanh Sơn            | 5 / 106 (tối thiểu theo E3)   |
| Nguyễn Thanh Sơn | 2A202603318 | Đã gán đủ 22 ảnh (peer export 22/22, còn 2 lỗi `cabin_check`) | Vũ Đức Toàn                 | 5 / 106 (tối thiểu theo E3)   |

- `export_final.zip` (Toàn): `cabin_check check --expect 22` → 0 lỗi, 0 cảnh báo (`check_final.txt`).
- Peer export `Son_export_final.zip`: 22/22, còn 2 lỗi `child_seat.occupied='yes'` ở S02_ir/S02_rgb.
- `disagreement_log.csv` (diff Toàn ↔ Sơn), 106 dòng diff trên 22 ảnh. Cột `verdict` / `guideline_rule` / `action` còn trống ở bản nộp này — coach chấm E3 theo bằng chứng này.

Ca hai bạn vẫn không thống nhất và đã hỏi coach (ghi tên ảnh + đối tượng):

- Không có ca mới ngoài các dòng diff trên; các bất đồng chính (D01 `person.seatbelt`; S02 `child_seat.occupied`; S06–S08 `child_seat` vs `infant_seat`) đã đối chiếu với GUIDE §4.7/§4.8 hoặc giữ `unknown`. Không có ca nào phải để coach giữ quyết sau buổi.

## Cá nhân

Mỗi người một mục, 1–2 dòng mỗi ý.

### Vũ Đức Toàn-2A202604068

- Phần mình làm (ngoài 22 ảnh của mình): chạy `cabin_check` ở Checkpoint A (`check_cpA.txt`: 2 lỗi `child_seat.occupied='yes'` + 4 cảnh báo `light`), Checkpoint B (`check_cpB.txt`: 0 lỗi, 2 cảnh báo `eye_*=unknown`), lần cuối 0 lỗi; tạo và đọc `disagreement_log.csv` khi diff với Sơn; viết `self_review.md` (bảng 4 cảnh S01_ir/S01_rgb/S07_rgb); tổng hợp ca hỏi coach.
- Điều mình học được hôm nay: "không thấy thì `unknown`" — dừng `occupied='yes'` và `worn_correct` trên SVIRO; phân biệt IR-sim (S##) với IR thật (D##) khi ghi `ir_log`; mặt D## xoay 90° thì mắt trái có y lớn hơn.

### Nguyễn Thanh Sơn-2A202603318

- Phần mình làm (ngoài 22 ảnh của mình): tự gán đủ 22 ảnh và gửi peer export (`Son_export_final.zip`) cho Toàn để diff; kiểm tra chéo bản của Toàn (các dòng `child_seat`/`infant_seat` S06–S08, cờ `face13` D03–D06) để hai bạn đối chiếu.
- Điều mình học được hôm nay: ghế gọi theo xe (trái ảnh = `rear_right`); `child_seat.occupied` không có `yes`; không đoán `seatbelt`/màu trên IR, không thấy thì ghi `unknown`.

---

Làm cá nhân: chỉ cần điền dòng của mình, tên bạn được coach ghép, và mục "Cá nhân" của mình. File TEAM.md chính thức nằm ở thư mục gốc cùng `export_final.zip` (SUBMISSION.md); copy này chỉ để tham khảo cấu trúc.
