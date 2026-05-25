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

- Vào thư mục dự án của bài tập 3 ```wordpress-project```
- Sửa file ```nano docker-compose.yml```
- Thêm service n8n vào file và lưu lại.

```
n8n:
    image: n8nio/n8n:latest
    container_name: wp_n8n
    restart: always
    ports:
      - "5678:5678"                            # Port mặc định của n8n
    environment:
      - TZ=Asia/Ho_Chi_Minh
      # Điền sub-domain đã cấu hình trỏ về service n8n này trong Cloudflare Dashboard vào đây:
      - WEBHOOK_URL=https://n8n.appwebtuanha.io.vn/ 
    volumes:
      - ./n8n-data:/home/node/.n8n            # Lưu lại dữ liệu của n8n để không bị mất khi restart
```
#### Khởi động lại hệ thống
```
docker compose up -d
```

#### Kiểm tra các service
```
docker compose ps
```

<img width="1605" height="133" alt="image" src="https://github.com/user-attachments/assets/6d147e5f-a346-4a8e-a322-a9992d66079e" />


