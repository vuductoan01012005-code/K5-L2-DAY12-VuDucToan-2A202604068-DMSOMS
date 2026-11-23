# GUIDE — Lab Ngày 12: DMS & OMS

## 1. Mục tiêu

Hết buổi, bạn làm được những việc sau:

1. Gán nhãn khung hình cabin theo một guideline chung: người, ghế, đồ vật (OMS) và mặt, mắt, 13 điểm mặt, tay, điện thoại (DMS).
2. Gọi ghế **theo xe**, gọi trái/phải **theo người**, kể cả khi camera nhìn ngược chiều hoặc ảnh bị xoay.
3. Ghi `unknown` khi không thấy, thay vì đoán.
4. Chỉ ra trường nhãn nào mất đi trên ảnh IR so với ảnh màu, và tách IR thật với IR mô phỏng.
5. Dùng một script tự kiểm và một lượt đối chiếu với bạn để tìm lỗi rồi sửa.

## 2. Đề bài

| Task | Ảnh | Bạn làm | Bằng chứng |
|---|---|---|---|
| **0 · Môi trường** | — | Tạo project `Day12` (dán `labels_cabin_v1.json` vào tab **Raw**). Tạo task `Day12-Gxx` với **22 ảnh cốt lõi**: chọn `Gxx/oms/S01_*.png` … `S08_*.png` và `Gxx/driver/D01.png` … `D06.png` (không chọn `S09_*`, `S10_*`, không chọn `manifest.csv`). Chạy `python scripts/cabin_check.py --help` | Project thấy đủ 9 nhãn; task có 22 ảnh |
| **1 · OMS hàng ghế sau** | S01–S08: 8 RGB + 8 IR-sim = 16 | Mỗi ghế có người hoặc vật: một box đúng loại và `seat` theo xe. Gán ảnh `_rgb` trước, rồi copy shape sang ảnh `_ir` và sửa thuộc tính | Checkpoint A, phần IR-sim của `ir_log` |
| **2 · DMS tài xế** | D01–D06: 6 khung NIR | `person`, `face` đủ thuộc tính, `face13`, `hand`, `phone` | Checkpoint B, phần IR thật của `ir_log` |
| **3 · Review chéo + sửa** | cả 22 | `cabin_check diff` với một bạn cùng mã `Gxx`, xử lý từng dòng bất đồng theo guideline, sửa, export lại | `disagreement_log.csv`, `export_final_v2.zip` nếu đã sửa |
| **4 · Danh sách lớp v0** | — | Viết tiếp danh sách lớp từ slide 54, kèm 2 câu hỏi leo thang | `class_list_v0.md` |
| Mở rộng (tùy chọn) | S09–S10 | Tạo task riêng `Day12-Gxx-extra` (4 ảnh) trong cùng project. Xem [§5 Lộ trình](#5-ba-lộ-trình) | `export_extra.zip` |

**Lịch trong buổi:**

| Giờ | Việc |
|---|---|
| 14:00 | Task 0 |
| 14:15 | Coach demo, ví dụ mẫu S00 |
| 14:30 | Task 1a: S01–S02 |
| 14:45 | **Checkpoint A** |
| 14:55 | Task 1b: S03–S08, ghi `ir_log` |
| 15:30 | Nghỉ |
| 15:45 | Task 2a: D01–D02 |
| 16:05 | **Checkpoint B** |
| 16:15 | Task 2b: D03–D06 |
| 16:50 | Export cuối, tự kiểm |
| 17:05 | Task 3: review chéo |
| 17:25 | Task 4 |
| 17:40 | Exit ticket |

Mỗi khung tài xế mất khoảng 6–10 phút. Khung nào quá 10 phút thì gán box và thuộc tính trước, quay lại `face13` sau.

## 3. Hai bộ ảnh bạn cần biết trước

### SVIRO (OMS, ảnh `S##_rgb.png` / `S##_ir.png`)

- Ảnh dựng bằng máy tính của một chiếc **BMW X5**. Hàng ghế sau có 3 chỗ: `rear_left`, `rear_middle`, `rear_right`.
- Camera đặt phía trước, **nhìn về phía sau xe**. Vì vậy **bên trái ảnh là `rear_right`**.
- Ảnh `_ir` là IR **mô phỏng** (IR-sim): đèn đỏ và kênh đỏ, không phải camera NIR thật. Hiện tượng của IR thật (chói kính, mắt sáng, ánh sáng yếu dần theo khoảng cách) **không** được tái hiện đúng. Đừng ghi các hiện tượng đó từ ảnh S##.
- **Không ai trong SVIRO thắt dây an toàn.** `seatbelt` chỉ có thể là `not_worn` hoặc `unknown`. Ghi `worn_correct` là sai.
- Ảnh dựng nên `light = unknown`.
- Bộ ảnh không có trẻ em ngồi. Thấy hình giống trẻ em thì báo coach, không gán.

### Drive&Act (DMS, ảnh `D##.png`)

- Ảnh hồng ngoại gần (NIR) thật, chụp trong xe lái tự động thử nghiệm. Tài xế làm việc khác: uống nước, gọi điện, tháo kính, thắt dây…
- **Ảnh xoay khoảng 90°**: đầu tài xế ở phía **phải ảnh**, mặt hướng về phía x nhỏ. **Đừng xoay ảnh lại.**
- Trên ảnh xoay này, **mắt trái của tài xế có y lớn hơn** (nằm thấp hơn trong ảnh) so với mắt phải. Muốn chắc thì xoay đầu mình theo ảnh rồi chỉ vào mắt trái của mình.
- Vì là xe lái tự động thử nghiệm, tay **không** trên vô lăng (`on_wheel = no`) là chuyện bình thường, không phải lỗi.
- Tên D01–D06 đã được xáo, nên tên file không cho biết tài xế đang làm gì.

---

## 4. Guideline Cabin v1.0

Guideline này do khóa học soạn. Nó **không** phải guideline của SVIRO hay Drive&Act; hai bộ đó chỉ cho ảnh. Khi guideline này khác cách gán gốc của dataset (ví dụ SVIRO đánh số ghế theo ảnh), bạn làm theo guideline này.

Đọc kèm: bài giảng sáng, slide 15–26 (ảnh hồng ngoại) và slide 40–54 (model cần nhận ra gì, mắt, vùng nhìn, ghế, lỗi gán nhãn). Ngày 21 (DMS) và Ngày 22 (OMS) sẽ mở rộng guideline thành bản 1.x. Mã và nghĩa của các quy tắc dưới đây giữ nguyên.

### 4.1 Gán gì, vẽ hình gì

| Label | Dạng CVAT | Vẽ cái gì | Thuộc tính | Dùng ở |
|---|---|---|---|---|
| `person` | rectangle | Một người: hộp sát phần cơ thể thấy được | `seat`: driver, front_passenger, rear_left, rear_middle, rear_right · `seatbelt`: worn_correct, worn_incorrect, not_worn, unknown | OMS, DMS |
| `face` | rectangle | Mặt một người: trán đến cằm, tai đến tai, không gồm tóc phía trên | `eye_left`, `eye_right`: open, closed, unknown · `glasses`: unknown, none, clear, sun · `gaze_zone`: road, mirror_l, mirror_r, mirror_c, cluster, center, lap, other | DMS (đủ thuộc tính); OMS (chỉ box) |
| `face13` | skeleton 13 điểm | 13 điểm mặt của Ngày 4, trên mặt đã có box `face` | cờ `occluded` / `outside` cho từng điểm | DMS |
| `hand` | rectangle | Một bàn tay: đầu ngón đến cổ tay | `whose`: driver, passenger · `side`: left, right (của người) · `on_wheel`: yes, no, unknown | DMS |
| `phone` | rectangle | Cả chiếc điện thoại, kể cả phần bị ngón tay che | `state`: held, mounted, lap | DMS |
| `child_seat` | rectangle | Cả ghế trẻ em, ghế đang trống | `seat` · `occupied`: no, unknown (**không có `yes`**) | OMS |
| `infant_seat` | rectangle | Cả ghế em bé (nôi xách), ghế đang trống | `seat` · `facing`: rear, front | OMS |
| `object` | rectangle | Một đồ vật mang theo, hộp sát vật | `seat` · `type`: bag, box, cup, other | OMS |
| tag `frame_meta` | tag (cả ảnh) | Mỗi ảnh đúng một lần, đặt **trước** box đầu tiên | `modality`: rgb, ir · `light`: day, night, unknown · `camera_blocked`: yes, no | OMS, DMS |

### 4.2 Gán và không gán

| | Gán | Không gán |
|---|---|---|
| **DMS** | Tài xế (`person`, `face`, `face13`); mỗi bàn tay thấy được (`hand`; tay hành khách cũng gán, `whose = passenger`); điện thoại nếu thấy rõ (`phone`) | Vô lăng, ghế, cửa, đồng hồ; mặt hoặc điện thoại trên áp phích, màn hình; mặt phản chiếu trên kính; chai, cốc, kính và vật khác trên tay (DMS không vẽ `object`, cũng không ghi là `phone`) |
| **OMS** | Mỗi ghế có người hoặc vật: một box đúng loại. Người lớn: `person` kèm box `face`; ghế trẻ em trống: `child_seat`; ghế em bé trống: `infant_seat`; đồ vật: `object` | Ghế trống (không vẽ box, chỉ đếm vào ghi chú); ghế, cửa, cột xe; dây an toàn không có người |

Quy đổi lớp SVIRO (chỉ để hiểu, bạn vẫn tự gán theo ảnh): người lớn → `person`; đồ vật → `object`; ghế em bé trống → `infant_seat`; ghế trẻ em trống → `child_seat`, `occupied = no`; ghế trống → không box.

### 4.3 Thứ tự làm trên mỗi ảnh

1. Tag `frame_meta`: `modality` đúng loại ảnh (`_rgb` → rgb; `_ir` và `D##` → ir), rồi `light` và `camera_blocked`.
2. OMS: nói to vị trí ghế **theo xe**, rồi mỗi ghế có người hoặc vật một box kèm `seat`. Vật nằm trong ghế trẻ em có box riêng.
3. DMS: `person` (`seat = driver`, `seatbelt`) → `face` (đủ thuộc tính) → `face13` (nếu mặt rộng ít nhất 60 px) → `hand` → `phone`.
4. Ctrl+S.

Chỉ gán những label bảng 4.1 ghi cho phần đó, không gán thêm loại khác.

### 4.4 Sáu quy tắc chung

1. **Không thấy thì `unknown`.** Mắt dưới đốm phản xạ, tròng kính râm tối, dây an toàn chìm trong bóng tối, ghế trẻ em bị phủ: đều `unknown`. Không suy từ mắt còn lại, từ ảnh kia của cùng cảnh, hay từ tên file.
2. **Trái/phải theo người trong ảnh** (mắt, tay, khóe miệng). Người quay mặt về camera thì mắt trái của họ nằm bên phải ảnh. Trên ảnh D## xoay 90°, mắt trái có y lớn hơn.
3. **Ghế gọi theo xe**, như người ngồi trong xe nhìn về phía trước. Camera OMS nhìn về phía sau, nên trái ảnh là phải của xe.
4. **Đừng đoán bằng màu trên ảnh IR.** Ảnh IR chỉ có độ sáng, nên nhận vật bằng hình dạng, vị trí, ngữ cảnh. Ảnh quá tối thì chỉnh brightness/contrast hiển thị trên CVAT (không sửa file ảnh), và không đoán.
5. **Hộp sát mép**, ôm đủ phần thấy được. Hộp lỏng hay lệch là lỗi.
6. **Ảnh ở yên trong dự án.** Không chụp màn hình gửi chat, không tải ra ngoài. Hỏi ca khó bằng tên ảnh, ví dụ "G03 D04".

### 4.5 Mặt, mắt, vùng nhìn

- `face`: không thấy mặt (quay đi, bị che gần hết, không thấy cả mắt lẫn mũi) thì **không vẽ box**. Ghi lý do vào ghi chú.
- `eye_left`, `eye_right` gán **riêng từng mắt**:
  - `open`: thấy khe mắt mở.
  - `closed`: thấy rõ hai mí khép.
  - `unknown`: không phân biệt được (phản xạ, kính râm tối, bị che, mặt quá nhỏ).
- Liếc xuống mà còn thấy khe mắt là `open`. Chỉ ghi `closed` khi mí thật sự khép. `closed` mô tả **khung hình này**, không phải "tài xế buồn ngủ".
- `glasses`: `none` là không đeo kính (kính cầm trên tay vẫn tính `none`); `clear` là kính cận, nhìn xuyên được tròng; `sun` là kính râm; `unknown` khi không nhìn rõ vùng mắt (cúi đầu, quay đi, tóc hoặc tay che, ảnh tối). Trên NIR, kính râm có thể trông trong suốt. Không chắc thì chọn `unknown`, đừng chọn `none` cho nhanh: ghi `none` sai cho người đang đeo kính là dạy model rằng người đeo kính không có kính.
- `gaze_zone` theo hướng mắt và đầu:
  - `road`: nhìn qua kính chắn gió phía trước
  - `mirror_l`, `mirror_r`, `mirror_c`: gương trái, gương phải, gương giữa
  - `cluster`: đồng hồ sau vô lăng
  - `center`: màn hình hoặc bảng giữa xe
  - `lap`: nhìn xuống đùi
  - `other`: ngoài các vùng trên, hoặc không chắc. Ghi lý do vào ghi chú.

### 4.6 13 điểm mặt (`face13`)

- Đủ 13 điểm, đúng tên như Ngày 4. Mỗi mắt có 4 điểm: khóe ngoài, khóe trong, giữa mép mí trên, giữa mép mí dưới. Còn lại là chóp mũi, hai khóe miệng, giữa mép ngoài môi trên và giữa mép ngoài môi dưới.
- Điểm mí và khóe đặt ở **mép**, nơi mí gặp mắt, không đặt giữa tròng.
- Điểm bị che (đốm phản xạ, tròng kính râm, tay, chai nước, sống mũi khi mặt nghiêng): đánh `occluded` nếu bạn đoán chắc vị trí, `outside` nếu không đoán được. **Không đặt điểm "cho đủ".**
- Mặt rộng dưới **60 px** thì chỉ vẽ box `face`, không vẽ `face13`.
- Chỉ vẽ `face13` trên ảnh D##. Người lớn trong SVIRO chỉ có box `face`.
- Ảnh D## xoay 90°: trục hai mắt chạy **dọc** (theo y). Mí "trên" nằm về phía x lớn (phía trán), mí "dưới" về phía x nhỏ.

### 4.7 Tay, điện thoại, dây an toàn

- `hand`: `side` là bàn tay **của người**, không theo bên trái ảnh. `on_wheel = yes` khi tay đang chạm vô lăng; `unknown` khi không thấy rõ. Một người không có hai tay cùng `side`.
- `phone`: ôm cả máy. `state` là `held` (tay cầm, kể cả áp tai), `mounted` (trên giá) hoặc `lap` (trên đùi). Chỉ vẽ khi thấy ít nhất một phần hình dạng của điện thoại (cạnh, góc hoặc màn hình). Tay áp gần tai cầm vật tối mà không thấy phần nào như vậy thì không vẽ `phone`. Hộp sáng trên màn hình hay áp phích không phải điện thoại.
- `seatbelt`:
  - `worn_correct`: dải chéo từ vai xuống hông phía đối diện
  - `worn_incorrect`: dải dưới nách, sau lưng, hoặc lệch khỏi vai
  - `not_worn`: thấy rõ không có dây
  - `unknown`: không thấy (tối, bị che, đang thắt dở)

  Không đoán từ ảnh kia của cùng cảnh. Trên SVIRO chỉ có `not_worn` hoặc `unknown`.

### 4.8 Ghế trẻ em, ghế em bé, đồ vật (OMS)

- Mỗi ghế có người hoặc vật: một box đúng label, `seat` theo xe (`rear_right` là ghế bên trái ảnh).
- Vật nằm trong ghế trẻ em: **hai box riêng** (`child_seat` và `object`), cùng `seat`. Không gộp thành một.
- `child_seat`: `occupied = no` khi thấy ghế trống; `unknown` khi không thấy rõ bên trong. Không dùng `yes`.
- Đồ vật: `type` theo hình dạng (bag, box, cup). Không chắc thì ghi `other`.
- Người lớn bị che một phần: vẫn vẽ `person` cho phần thấy được. `seatbelt` và mắt ghi `unknown` nếu không thấy.
- Không vẽ box cho ghế trống. Đếm số ghế trống vào ghi chú.

### 4.9 Tự kiểm trước khi nộp

- [ ] Mọi ảnh có đúng một tag `frame_meta`, `modality` đúng loại ảnh.
- [ ] Mọi box OMS có `seat`. Ghế gọi theo xe, không theo ảnh. Không có `occupied = yes`. SVIRO không có `worn_*`.
- [ ] Không còn giá trị đoán: chỗ không thấy là `unknown` (hoặc cờ `occluded`/`outside` ở điểm mặt).
- [ ] Mắt và tay trái/phải theo người. `face13` đúng trái/phải, mặt dưới 60 px không có `face13`.
- [ ] Vật trong ghế trẻ em có hai box. Ghế trống không có box.
- [ ] Export **CVAT for images 1.1**, **bỏ chọn "Save images"**, mở lại được.
- [ ] `cabin_check check --expect 22` báo 0 lỗi. Mỗi cảnh báo còn lại có giải thích trong `check_final.txt` hoặc ghi chú.

---

## 5. Ba lộ trình

Cùng guideline, cùng bằng chứng tối thiểu E1–E5, cùng rubric. Chỉ mức hỗ trợ và phần đào sâu là khác nhau.

| | Hỗ trợ | Độc lập (mặc định) | Mở rộng |
|---|---|---|---|
| Dành cho | Lần đầu dùng CVAT/keypoint, hoặc Checkpoint A còn trên 3 lỗi | Phần lớn lớp | Xong E1–E5 trước 17:05 |
| Bạn có | Ví dụ S00 mở song song, thẻ ánh xạ ghế in sẵn, coach kèm 2 cảnh đầu; được làm Task 2 trước nếu kẹt ở OMS | Guideline + thẻ quy tắc | Không thêm hướng dẫn |
| Làm thêm | Không | Không | Chọn **một**: (a) gán S09–S10, rồi nhờ coach so lớp ghế với nhãn gốc SVIRO và phân loại lỗi (sai ánh xạ / sai lớp / mơ hồ thật); (b) đề xuất sửa guideline có bằng chứng, ví dụ SVIRO không có ai thắt dây nên không dạy được lớp `worn_correct`; (c) chạy `python scripts/rgb_ir_diff.py Gxx/oms --out ir_pairs` (cần `pip install pillow numpy`), chọn 2 cảnh RGB tối và giải thích trường nào IR-sim còn giữ được |

Phần mở rộng chỉ nhận nhận xét của coach, không cộng điểm và không phải nhánh chấm thứ hai (RUBRIC).

## 6. Mẹo CVAT nhanh

- **Copy shape từ RGB sang IR-sim:** chọn shape trên `S01_rgb`, Ctrl+C, sang `S01_ir`, Ctrl+V, rồi sửa thuộc tính (trường nào không thấy thì đổi thành `unknown`). Kiểm lại vị trí box vì hai ảnh có thể lệch vài pixel.
- Ảnh tối: chỉnh brightness/contrast ở menu **Image settings** (biểu tượng bánh răng trên canvas). Thao tác này chỉ đổi hiển thị, không sửa file.
- Lỗi giao diện hay gặp (popover vẽ, phím tắt `N`, đổi label, thuộc tính ở **DETAILS**): xem cuối [INSTALL_CVAT.md](INSTALL_CVAT.md).
