# Cài CVAT trên máy cá nhân (làm trước buổi học 3 ngày)

Mục tiêu: trước Ngày 12, máy bạn mở được `http://localhost:8080` và đăng nhập được CVAT. Cài lần đầu mất khoảng 20–40 phút, tuỳ mạng. Nếu kẹt, báo coach **trước buổi học**, đừng để đến sáng hôm đó.

Phiên bản lớp dùng: **CVAT v2.82.0** (coach đã chạy thử). v2.83.0 chưa thử.

Nguồn: hướng dẫn chính thức tại https://docs.cvat.ai/docs/administration/basics/installation/

## 1. Cần có

- **Docker Desktop** (macOS/Windows) hoặc Docker Engine + Docker Compose (Linux).
  - Windows: bật WSL 2 theo hướng dẫn của Docker Desktop.
  - Trong Docker Desktop → Settings → Resources, cấp RAM cho Docker khoảng 8 GB nếu máy cho phép. Đây là mức máy coach chạy ổn, không phải yêu cầu tối thiểu chính thức.
- **Git.**
- **Chrome hoặc Edge.** CVAT chỉ hỗ trợ trình duyệt nhân Chromium.
- Khoảng 10 GB đĩa trống cho các image Docker.

## 2. Cài

Mở Terminal (Windows: mở Ubuntu/WSL hoặc PowerShell), chạy lần lượt:

```bash
git clone --branch v2.82.0 https://github.com/cvat-ai/cvat
```

```bash
cd cvat
```

```bash
docker compose up -d
```

Lần đầu Docker tải image khá lâu. Chờ đến khi các container ở trạng thái Running:

```bash
docker compose ps
```

## 3. Tạo tài khoản quản trị

```bash
docker exec -it cvat_server bash -ic 'python3 ~/manage.py createsuperuser'
```

Nhập tên đăng nhập, email (có thể để trống) và mật khẩu. Mật khẩu này **chỉ dùng cho CVAT trên máy bạn**. Đừng dùng lại mật khẩu email hay LMS, và đừng gửi cho ai.

## 4. Kiểm tra

1. Mở Chrome/Edge, vào `http://localhost:8080`.
2. Đăng nhập bằng tài khoản vừa tạo.
3. Thấy trang **Projects** là xong.

Gửi coach (qua LMS) **ảnh chụp trang đăng nhập hoặc trang Projects trống**. Đây là ảnh duy nhất bạn chụp trong cả lab; sau khi có ảnh cabin thì không chụp màn hình nữa (RULES §2).

## 5. Bật / tắt

- Tắt khi không dùng (giữ dữ liệu): trong thư mục `cvat`, chạy
  ```bash
  docker compose down
  ```
- Bật lại: `docker compose up -d`, chờ khoảng 1 phút rồi mở `localhost:8080`.
- **Đừng** thêm `-v` vào lệnh `down` trước khi nộp bài: `-v` xoá luôn dữ liệu gán nhãn.

## 6. Lỗi hay gặp khi cài

| Hiện tượng | Cách gỡ |
|---|---|
| `port is already allocated` (8080) | Có chương trình khác dùng cổng 8080. Tắt nó, hoặc hỏi coach cách đổi cổng |
| `localhost:8080` trắng trang hoặc 502 | Server chưa khởi động xong. Chờ 1–2 phút, `docker compose ps` xem còn container nào Restarting |
| `cvat_server` không tồn tại | Chạy `docker compose ps` để xem tên container đúng |
| Máy rất chậm | Tăng RAM cho Docker; đóng bớt ứng dụng |
| Mac chip Apple, image báo lỗi kiến trúc | Cập nhật Docker Desktop bản mới; báo coach nếu vẫn lỗi |

## 7. Mẹo dùng giao diện (đọc trước, đỡ mất thời gian trong lớp)

- **Menu vẽ ở thanh bên trái:** tùy phiên bản CVAT, menu mở khi rê chuột (v2.82) hoặc phải bấm vào biểu tượng (bản 2.7x chỉ hiện tooltip khi rê). Mở menu, chọn label, rồi bấm **Shape** (tag thì bấm nút **+**).
- **Phím tắt chỉ chạy khi khung ảnh đang được chọn** (bấm vào ảnh một lần). `N` vẽ lại hình cùng loại vừa vẽ.
- **Đổi label** của một hình: chọn hình, đổi trong ô thả xuống ở danh sách đối tượng bên phải.
- **Thuộc tính** (seat, seatbelt, eye…) nằm trong phần **DETAILS** của từng đối tượng ở thanh bên phải. Bấm mũi tên để mở.
- `F` / `D` chuyển ảnh sau / trước. Lưu thường xuyên bằng nút **Save** trên thanh công cụ, hoặc `Ctrl+S` (macOS: `Cmd+S`) khi khung ảnh đang được chọn.
- **Tải file export:** sau khi bấm OK ở hộp thoại export, CVAT tạo một yêu cầu trong trang **Requests** (thanh menu trên cùng). Chờ dòng yêu cầu báo **Finished**, bấm **⋮** ở cuối dòng → **Download**.
