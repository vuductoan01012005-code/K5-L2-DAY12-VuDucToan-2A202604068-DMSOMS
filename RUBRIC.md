# RUBRIC — Lab Ngày 12 (formative)

> **Trạng thái:** đề xuất của Lab Coach. Trọng số và ngưỡng đạt chính thức do **GV/VinUni** quyết định. Điểm dùng để phản hồi trong buổi, không phải điểm thi.

Chấm **từng người**, kể cả khi làm nhóm. Phần nhóm chỉ có `TEAM.md`, không cộng điểm.

| Tiêu chí | Điểm | Đạt khi |
|---|---|---|
| E1 · Export hợp lệ, đủ 22 ảnh | 10 | `cabin_check ... --expect 22` báo 0 lỗi. Mỗi ảnh có đúng 1 tag `frame_meta` |
| OMS · ghế + lớp (S01–S08) | 20 | Ghế gọi theo xe, không theo ảnh. Lớp ghế khớp đáp án SVIRO (coach giữ). `seatbelt` chỉ là `not_worn` hoặc `unknown` |
| IR · cặp IR-sim + `ir_log.csv` (E4) | 15 | Ít nhất 4 quan sát. Mỗi dòng có khung, hiện tượng, trường nhãn bị ảnh hưởng. Không lẫn IR-sim (S##) với IR thật (D##) |
| DMS · D01–D06 | 25 | **face13 (10):** điểm bị che có cờ, không đoán; trái/phải theo người lái dù ảnh xoay 90°; mặt < 60 px không vẽ. **Thuộc tính (15):** mắt, kính, vùng nhìn, tay, điện thoại; không vẽ `phone` khi không thấy rõ vật; `on_wheel = no` là bình thường (xe đang tự lái) |
| Review–rework (E2, E3) | 15 | Đủ `check_cpA.txt`, `check_cpB.txt`, `check_final.txt`. Lỗi gặp ở checkpoint không lặp lại ở các ảnh gán sau và đã hết ở lần cuối. Mỗi bất đồng gắn với một mục guideline. Có `export_final_v2.zip` nếu đã sửa sau review chéo |
| Danh sách lớp v0 + câu hỏi leo thang (E5) | 10 | Mỗi lớp nói rõ để làm gì cho DMS/OMS. Mỗi câu hỏi có khung ví dụ |
| Tự đánh giá | 5 | Nêu 1 lỗi mình lặp lại và cách tránh |
| **Tổng** | **100** | |

## Điều kiện tối thiểu

- Có E1–E5 của chính mình (nộp thiếu thì coach ghi "chưa đủ bằng chứng" và hẹn bổ sung, không chấm 0 ngay).
- Không có ảnh trong file nộp (vi phạm thì coach yêu cầu xóa và nộp lại).
- `prompts.md` có mặt, kể cả khi ghi "Không dùng".

## Phần mở rộng

S09–S10 và các lựa chọn mở rộng ở GUIDE §5 **không cộng điểm** vào thang 100. Coach phản hồi bằng nhận xét. Không làm phần mở rộng không bị trừ.

## Điểm khác biệt giữa hai bạn

Hai bạn gán giống nhau chỉ cho thấy **nhất quán**, chưa chắc **đúng**. Coach chấm theo guideline và đáp án, không theo số dòng hai bạn thống nhất.
