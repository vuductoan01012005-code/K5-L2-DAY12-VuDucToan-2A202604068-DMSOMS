# CHECKPOINTS — tự kiểm bằng `cabin_check`

`cabin_check` đọc file export CVAT và đối chiếu với Guideline Cabin v1.0. Script chỉ dùng thư viện chuẩn của Python. Chạy mọi lệnh dưới đây **từ thư mục gốc của repo này**, sau khi đã chép thư mục `Gxx/` coach phát vào trong repo (xem README).

```bash
python scripts/cabin_check.py --help
```

macOS/Linux dùng `python3` nếu máy không có lệnh `python`.

**Cách export:** trong CVAT, chọn task → **Actions** → **Export task dataset** → định dạng **CVAT for images 1.1** → **bỏ chọn "Save images"** → đặt tên file → OK → vào trang **Requests**, chờ **Finished**, bấm **⋮** → **Download**. File tải về là một zip vài chục KB. CVAT luôn export **cả task**, kể cả ảnh bạn chưa gán; vì vậy ở checkpoint bạn dùng `--only` để chỉ kiểm những ảnh đã làm. Gõ **đủ mã 2 chữ số** (`S01`, `D01`, không gõ `S1`); `S01` gồm cả `S01_rgb` và `S01_ir`. Mã không khớp ảnh nào thì script báo lỗi và thoát mã `2`.

**Ghi kết quả ra file bằng `--out`**, không dùng `> file`. Trên Windows, `>` làm hỏng chữ tiếng Việt trong file.

Đọc kết quả:
- `[ERROR]` là lỗi phải sửa.
- `[WARN]` là cảnh báo, cần bạn tự xét. Cảnh báo không tự động là sai.
- Mã thoát: `0` không có lỗi, `1` có lỗi, `2` không đọc được file.

## Checkpoint A — 14:45 (sau S01–S02)

```bash
python scripts/cabin_check.py check part_A.zip --only S01,S02 --expect 4 --manifest Gxx/oms/manifest.csv --out check_cpA.txt
```

- **Làm gì:** gán xong 4 ảnh S01_rgb, S01_ir, S02_rgb, S02_ir; export thành `part_A.zip`; chạy lệnh trên.
- **Sản phẩm:** `check_cpA.txt` (E2).
- **Cần hiểu:** ghế gọi theo xe (camera nhìn về sau nên trái ảnh = `rear_right`); không thấy thì `unknown`.
- **Tự kiểm:** dòng `Ảnh: 4 · đã có frame_meta: 4`. Coach xem các dòng `[ERROR]` trên màn hình bạn. Hay gặp:
  - Ghế gán theo ảnh (người bên trái ảnh lại ghi `rear_left`).
  - `worn_correct` trên ảnh SVIRO.
  - Thiếu tag `frame_meta`, hoặc `modality` sai (`_ir` lại ghi rgb).
  - Một box cho cả ghế trẻ em lẫn túi bên trong.

Trên 3 lỗi ERROR **trong 4 ảnh này**: chuyển sang lộ trình **Hỗ trợ** (xem GUIDE §5) trước khi gán tiếp.

## Checkpoint B — 16:05 (sau D01–D02)

```bash
python scripts/cabin_check.py check part_B.zip --only D01,D02 --expect 2 --manifest Gxx/oms/manifest.csv --out check_cpB.txt
```

- **Làm gì:** gán xong D01, D02; export thành `part_B.zip`; chạy lệnh trên.
- **Sản phẩm:** `check_cpB.txt` (E2).
- **Cần hiểu và tự kiểm:**
  - `face13` đủ 13 điểm, mỗi điểm có cờ `occluded`/`outside`.
  - Trái/phải theo người. Trên ảnh D## xoay 90°, mắt trái có **y lớn hơn**.
  - Mặt hẹp hơn 60 px thì không vẽ `face13`.
  - Phân biệt mắt `unknown` (không thấy) với `closed` (thấy mí khép).

**Lưu ý về cảnh báo "left_eye_outer nằm bên trái ảnh so với right_eye_outer":** luật này so theo trục x, dành cho mặt nhìn thẳng. Trên ảnh D## xoay 90°, hai mắt xếp theo trục y, nên cảnh báo có thể hiện ra dù bạn gán đúng. Kiểm bằng trục y: `left_eye_*` phải có y lớn hơn `right_eye_*`. Nếu đúng như vậy, ghi một dòng giải thích ở cuối `check_cpB.txt`.

Muốn soát lại cả phần OMS đã làm: chạy thêm `--only S01,S02,S03,S04,S05,S06,S07,S08,D01,D02` (sau khi đã gán đủ các ảnh trong phạm vi này).

## Lần cuối — 16:50

```bash
python scripts/cabin_check.py check export_final.zip --expect 22 --manifest Gxx/oms/manifest.csv --out check_final.txt
```

- **Sản phẩm:** `export_final.zip` (E1), `check_final.txt` (E2).
- **Yêu cầu:** dòng cuối là `Kết quả: 0 lỗi`. Mỗi cảnh báo còn lại có một dòng giải thích ở cuối `check_final.txt` (mở file và gõ thêm).
- Task chính chỉ có 22 ảnh cốt lõi (GUIDE Task 0), nên không cần `--only`. Nếu làm phần mở rộng S09–S10 ở task riêng, kiểm file đó bằng `--expect 4 --out check_extra.txt`.

## Review chéo — 17:05

```bash
python scripts/cabin_check.py diff export_final.zip peer_export_final.zip --csv disagreement_log.csv
```

1. Mở `disagreement_log.csv` (Excel hoặc trình soạn thảo). Với mỗi dòng (ít nhất 5 dòng, hoặc tất cả nếu ít hơn 5), cùng bạn điền:
   - `verdict`: `mine` / `peer` / `both_wrong` / `ambiguous`
   - `guideline_rule`: mục guideline, ví dụ `4.4-3` hoặc `4.6`
   - `action`: sửa gì
2. Dòng nào hai bạn vẫn không thống nhất thì hỏi coach. Số liệu diff giúp tìm bất đồng về nhãn và thuộc tính. Tự so vị trí các điểm `face13` trên CVAT.
3. Nếu review phát hiện chỗ cần sửa, sửa trên CVAT của mình, export lại thành `export_final_v2.zip`, chạy lại:

```bash
python scripts/cabin_check.py check export_final_v2.zip --expect 22 --manifest Gxx/oms/manifest.csv --out check_final_v2.txt
```

Excel lưu lại CSV: chọn **CSV UTF-8**, nếu không chữ tiếng Việt sẽ hỏng.

## Không chạy được Python?

Dùng checklist tay ở GUIDE §4.9 và báo coach. Coach có checklist dự phòng.
