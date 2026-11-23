# Tự đánh giá — Vũ Đức Toàn · <G12>

## 1. Tự rà soát 4 cảnh

Nghỉ tay 1 phút, mở lại bài của mình, chọn 4 ảnh (2 S##, 2 D##), đọc như người lạ.

Loại lỗi: `seat_flip`, `wrong_class`, `guess`, `box`, `missing`, `extra`, `lr_flip` (trái/phải mặt), `flag` (thiếu cờ điểm), `ok`.

| Ảnh | Đối tượng | Loại lỗi | Ghi chú | Đã sửa? |
| S01_ir.png | child_seat | occupied='yes' không hợp lệ | cho phép: unknown, no | occupied='unknown' |
| S01_rgb.png | child_seat | occupied='yes' không hợp lệ | cho phép: unknown, no | occupied='unknown' |
| S07_rgb.png | child_seat | ảnh SVIRO là ảnh dựng | light nên là 'unknown' | light='unknown' |
| S07_rgb.png | child_seat | ảnh SVIRO là ảnh dựng | light nên là 'unknown' | light='unknown' |

## 2. Lỗi mình lặp lại nhiều nhất hôm nay

Lỗi: ảnh SVIRO là ảnh dựng: light nên là 'unknown'

Vì sao mình mắc: chưa hiểu rõ ảnh dựng thì light='unknown'

Lần sau tránh bằng cách: ảnh dựng thì light='unknown'

## 3. Ghi chú về disagreement_log

Một dòng bạn của mình đúng và mình sai. Mình học được gì từ dòng đó:

D01.png | person | seatbelt | khác giá trị (IoU=0.73) | worn_correct | worn_incorrect
