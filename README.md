# B4-Developinng-Applications-with-Open-Source
# KHAI THÁC N8N ĐỂ TỰ ĐỘNG ĐĂNG BÀI LÊN WORDPRESS
- Môn học: Phát triển ứng dụng với mã nguồn mở - TEE0421
- Họ và tên: Ngụy Đình Tuấn Hà
- Công nghệ sử dụng: Wordpress, Docker, MariaDB, PhpMyAdmin, Cloudflare, N8N

📌 MỤC LỤC
1. Giới thiệu

2. Các bước thực hiện

3. Kết quả đạt được

4. Nhận xét

## 1. GIỚI THIỆU
Bài tập này mở rộng từ hệ thống WordPress đã được triển khai từ bài tập trước bằng Docker Compose. Bổ sung thêm service n8n để xây dựng hệ thống tự động đăng bài lên WordPress thông qua Telegram Bot và Google Gemini AI.

Người dùng chỉ cần gửi nội dung qua Telegram Bot, hệ thống sẽ tự động:

- Nhận yêu cầu từ Telegram
- Gửi prompt tới Google Gemini AI
- Sinh nội dung bài viết dạng HTML
- Tự động đăng bài lên WordPress

Mô hình hệ thống:
```
Telegram User
      ↓
Telegram Bot
      ↓
Telegram Trigger (n8n)
      ↓
Google Gemini AI
      ↓
Code JavaScript
      ↓
WordPress API
      ↓
Website WordPress
```

Cách hoạt động:

- Người dùng gửi nội dung tới Telegram Bot
- Telegram Trigger trong n8n nhận được tin nhắn
- Nội dung được gửi tới Google Gemini AI
- AI sinh tiêu đề và nội dung bài viết dạng HTML
- Node Code in JavaScript xử lý dữ liệu JSON trả về
- Node WordPress sử dụng API để đăng bài viết
- Bài viết xuất hiện trực tiếp trên website WordPress

## 2. CÁC BƯỚC THỰC HIỆN
### Bước 1: Cập nhật docker-compose.yml
#### Sửa file docker-compose thêm service N8N

- Vào thư mục dự án của bài tập 3 wordpress-project
- Sửa file nano docker-compose.yml
- Thêm service n8n vào file và lưu lại.

