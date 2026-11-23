# COMMON_ERRORS — lỗi hay gặp và cách gỡ

Tra bảng này trước khi hỏi coach. Cột "Tự kiểm" chỉ ra cách bạn tự phát hiện lỗi.

## OMS (S##, SVIRO)

| Lỗi | Dấu hiệu | Tự kiểm / cách gỡ |
|---|---|---|
| Ghế gán theo ảnh | Người bên trái ảnh lại ghi `rear_left` | Camera nhìn về phía sau xe: **trái ảnh = phải xe**. Đứng quay lưng về màn hình như camera rồi chỉ tay. `cabin_check check` **không** bắt được lỗi này (script không có đáp án); bạn tự soát, hoặc `diff` với bạn sẽ lộ ra |
| Chép thứ tự ghế trong tên file SVIRO | Mọi cảnh đều lật trái/phải | Tên file của SVIRO xếp theo ảnh, không theo xe. Đừng dựa vào tên file |
| `worn_correct` / `worn_incorrect` trên SVIRO | `cabin_check` WARN | Không ai trong SVIRO thắt dây. Chỉ dùng `not_worn` hoặc `unknown` |
| `light` khác `unknown` trên SVIRO | `cabin_check` WARN | Ảnh tổng hợp, không có giờ trong ngày thật. Ghi `unknown` |
| `rear_middle` trên xe chỉ có 2 ghế sau | `cabin_check` ERROR | Bộ cốt lõi là BMW X5 (3 ghế). Nếu gặp i3/Zoe ở phần mở rộng thì chỉ có `rear_left`/`rear_right` |
| Một box cho ghế trẻ em và túi bên trong | Thiếu box `object` | Hai vật, hai box |
| Bỏ sót người ghế sau trên ảnh IR tối | Ít box `person` hơn bản RGB | Tăng Brightness/Contrast trong Image settings của CVAT (không sửa file ảnh) |
| Ghi hiện tượng IR thật lên ảnh IR-sim | `ir_log` ghi "loá kính" ở S## | S##_ir là **ảnh mô phỏng**, không có phản xạ LED thật. Hiện tượng IR thật chỉ ghi cho D## |
| Đoán `seatbelt` trên ảnh RGB tối | RGB và IR cùng cảnh có giá trị giống nhau dù RGB không thấy dây | Luật chung 1: không thấy thì `unknown`. Ghi khác biệt vào `ir_log` |
| Chai nước hay ví ghi là `phone` | Box `phone` ở tay hoặc ghế | Chỉ ghi `phone` khi thấy cạnh, góc hoặc màn hình điện thoại. Không chắc: DMS thì không vẽ; OMS thì `object` (type `other`) |

## DMS (D##, Drive&Act)

| Lỗi | Dấu hiệu | Tự kiểm / cách gỡ |
|---|---|---|
| Xoay ảnh lại cho dễ nhìn | Toạ độ lệch khi export | **Không xoay ảnh.** Gán trên ảnh như đang thấy |
| Mắt trái/phải theo ảnh | `left_eye_*` có y nhỏ hơn `right_eye_*` | Ảnh xoay ~90°, đầu ở phía phải ảnh. Mắt **trái** của tài xế có **y lớn hơn** (nằm thấp hơn trên màn hình) |
| Cảnh báo "left_eye_outer bên trái ảnh" dù đã gán đúng | `cabin_check` WARN trên D## | Luật này so theo trục x, dành cho mặt nhìn thẳng. Kiểm bằng y như dòng trên; nếu đúng thì ghi một dòng giải thích vào `check_*.txt` |
| Mắt dưới đốm phản xạ ghi `closed` | `eye_* = closed` ở ảnh có kính | Hỏi mình: "thấy mí khép, hay chỉ không thấy?" Không thấy thì `unknown` |
| Vẽ face13 trên mặt quá nhỏ | Mặt < 60 px vẫn có skeleton | `cabin_check` ERROR. Chỉ vẽ box `face`, bỏ `face13` |
| Điểm bị che không có cờ | Điểm nằm "đoán" chỗ mắt sau tay | Đặt điểm ở vị trí ước lượng và bật `occluded`; ra ngoài khung thì `outside` |
| Kính cầm trên tay ghi `glasses = clear` | Thuộc tính mặt sai | Thuộc tính `glasses` chỉ nói kính **đang đeo**. Kính trên tay là `none` |
| `on_wheel = no` tưởng là lỗi | Sửa thành `yes` dù tay không ở vô lăng | Drive&Act quay xe ở chế độ tự lái: tay rời vô lăng là bình thường. Gán đúng cái thấy |

## Chung

| Lỗi | Dấu hiệu | Tự kiểm / cách gỡ |
|---|---|---|
| Box mới có sẵn `seat = driver` | CVAT điền giá trị đầu danh sách cho mọi thuộc tính | Vẽ xong box nào, mở **DETAILS** chọn lại `seat` ngay. Trên SVIRO, `cabin_check` cảnh báo `seat=driver` |
| `face13` mới vẽ nằm thẳng đứng như mặt nhìn thẳng | CVAT đặt template 13 điểm theo chiều đứng | Trên D## xoay 90°, kéo từng điểm về đúng chỗ: mắt trái có y lớn hơn. `cabin_check` chỉ so trục x nên không tự bắt được template đặt sai chiều |
| Quên tag `frame_meta` | `cabin_check` ERROR "thiếu frame_meta" | Đặt tag **trước** khi vẽ box đầu tiên của mỗi ảnh |
| `modality` sai | Ảnh `_ir` ghi `rgb` | Nhìn tên file. D## đều là `ir` |
| Export kèm ảnh | Zip nặng hàng chục MB | Bỏ chọn "Save images" khi export. `python -m zipfile -l export_final.zip` chỉ được thấy `annotations.xml` |
| Checkpoint báo hàng chục lỗi "cần đúng 1 tag frame_meta, đang có 0" | Lỗi nằm ở các ảnh bạn chưa gán | CVAT export cả task. Ở checkpoint thêm `--only S01,S02` (hoặc `--only D01,D02`) để chỉ kiểm ảnh đã làm |
| Lần cuối còn lỗi thiếu frame_meta ở S09/S10 | Task có 26 ảnh | Task chính chỉ có 22 ảnh cốt lõi (GUIDE Task 0). Nếu đã lỡ tạo 26 ảnh: kiểm lần cuối với `--only S01,S02,S03,S04,S05,S06,S07,S08,D01,D02,D03,D04,D05,D06` (thay `--expect 22`) và ghi một dòng giải thích ở cuối `check_final.txt` |
| File `check_*.txt` lỗi chữ, hoặc script báo `UnicodeEncodeError` | Chạy bằng `> file` trên Windows, hoặc dùng script cũ trong `Gxx/scripts` | Dùng `scripts/cabin_check.py` của repo và `--out check_xxx.txt` thay cho `> check_xxx.txt` |
| Script báo "--only ... không khớp ảnh nào" | Gõ thiếu chữ số (`S1`, `S0`) | Gõ đủ mã 2 chữ số: `S01,S02`, `D01`. `S01` đã gồm cả `S01_rgb` và `S01_ir` |
| Mở `disagreement_log.csv` bằng Excel rồi lưu, chữ tiếng Việt hỏng | Lưu ở dạng CSV thường | Lưu bằng **CSV UTF-8 (Comma delimited)** |
| Export mất thuộc tính | XML thiếu attribute | Không sửa XML bằng tay. Ghi vào `check_final.txt`, báo coach |
| `ir_log` chung chung | "IR rõ hơn" không có khung, vật | Mỗi dòng: một khung, một hiện tượng, một trường nhãn bị ảnh hưởng |
| Chụp màn hình gửi nhóm chat | Ảnh cabin ngoài kênh lớp | Xóa ngay. Hỏi bằng tên ảnh (xem RULES §2) |
| Hai bạn chia ảnh để làm nhanh | Mỗi export chỉ có 11 ảnh | Không được chia. Mỗi người gán đủ 22 ảnh (RULES §1) |
