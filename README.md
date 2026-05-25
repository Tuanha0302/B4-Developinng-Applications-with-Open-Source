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

### Bước 2: Cấu hình thêm route trên cloudflare tunnel
- Cấu hình Tunel cloudflare để truy cập vào các dịch vụ bằng các subdomain
- Thêm 2 route sau:

| Subdomain | Service URL |
|---|---|
| phpmyadmin.appwebtuanha.io.vn| http://phpmyadmin:80 |
| n8n.appwebtuanha.io.vn | http://n8n:5678 |

#### Thêm route cho phpmyadmin:
<img width="1918" height="940" alt="image" src="https://github.com/user-attachments/assets/241e81f5-767d-4b19-9bb9-d3fd6d4e2e5c" />

#### Thêm route cho n8n:
<img width="1912" height="945" alt="image" src="https://github.com/user-attachments/assets/9d63479d-6069-40db-8e81-9e76bb6d9f50" />

### Bước 3: Truy cập subdomain xem kết quả
#### Kết quả truy cập subdomain https://wordpress.webapptuanha.io.vn/wp-admin
<img width="1916" height="987" alt="image" src="https://github.com/user-attachments/assets/35968146-6538-4937-80a7-398e7b35b1e6" />

#### Kết quả truy cập subdomain https://n8n.appwebtuanha.io.vn/
<img width="1914" height="986" alt="image" src="https://github.com/user-attachments/assets/54c1ee8e-496d-4d58-a751-b2a2710635e7" />

#### Kết quả truy cập subdomain https://phpmyadmin.appwebtuanha.io.vn/
<img width="1916" height="994" alt="image" src="https://github.com/user-attachments/assets/c1369a9a-32d3-4e79-a13b-19308d1cd054" />

### Bước 4: Tạo bài viết trên Wordpress
#### Bước 4.1. Bài viết giới thiệu bản thân
##### Bài viết 1: đã thực hiện ở bài tập 3
<img width="1917" height="994" alt="image" src="https://github.com/user-attachments/assets/fdd0c8f5-13d3-4cfe-b5f1-bb3280937872" />

##### Bài viết 2: sử dụng html'
<img width="1908" height="991" alt="image" src="https://github.com/user-attachments/assets/2b910c5f-7d0a-4dde-9bbb-cd1cf20e4650" />

#### Bước 4.2. Bài viết giới thiệu những kiến thức đã học được ở môn học Phát triển ứng dụng với mã nguồn mở
<img width="1917" height="950" alt="image" src="https://github.com/user-attachments/assets/174897e9-2463-4677-bdd0-7e4903d605e1" />

<img width="1919" height="949" alt="image" src="https://github.com/user-attachments/assets/dab03e1d-5e38-46bc-a697-258f3721eb9d" />

### Bước 5: Cấu hình n8n
#### Bước 5.1. Truy cập subdomain https://n8n.nguyentrunghiieu.id.vn/ để tạo tài khoản và kích hoạt license
##### Điền các thông tin , đặc biệt email cần chính xác -> Next
<img width="1912" height="996" alt="image" src="https://github.com/user-attachments/assets/f4ac3ac5-7ae3-40c1-9abf-2c4223dbc007" />

##### Chọn Send me a free license key
<img width="1915" height="986" alt="image" src="https://github.com/user-attachments/assets/4cb416ae-c68a-4e4b-a5a8-560203d2e470" />

##### Trong Usage and plan -> Enter Activation Key -> Nhập Key lấy từ email -> Activate
<img width="1918" height="990" alt="Screenshot 2026-05-25 214931" src="https://github.com/user-attachments/assets/787ce675-0ccd-4e23-8b7d-2a2d9f9ba458" />

##### Nhận thông báo thành công:
<img width="1915" height="992" alt="Screenshot 2026-05-25 215115" src="https://github.com/user-attachments/assets/d38a7543-b84e-4c08-93be-4b2e2f95fbc2" />

#### Bước 5.2. Tạo workflow
##### Chọn Home page -> Overview -> New workflow
<img width="1915" height="990" alt="image" src="https://github.com/user-attachments/assets/e246e10b-dfb0-4362-a93c-162790be1228" />

##### Tạo thành công:
<img width="1918" height="992" alt="image" src="https://github.com/user-attachments/assets/0342a14e-fe9f-41e9-9269-8f78b1aabfcb" />

#### Bước 5.3. Tạo Telegram Bot
Mở Telegram và tìm kiếm BotFather có tích xanh chính chủ
Bấm /start
