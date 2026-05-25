<img width="1912" height="945" alt="image" src="https://github.com/user-attachments/assets/8c0a6cd7-be57-4063-adf8-ff771e88dbac" /># B4-Developinng-Applications-with-Open-Source
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
<img width="1436" height="943" alt="image" src="https://github.com/user-attachments/assets/53e43f59-ee9d-4b6d-9811-23a62e0948f3" />

##### Nhập lệnh /newbot
<img width="1435" height="943" alt="image" src="https://github.com/user-attachments/assets/933fa404-9548-4e57-9351-44c93d522a39" />

##### Đặt tên cho bot Bot_n8n_wordpress
##### Đặt usename cho bot, bắt buộc kết thúc bằng bot (tuanha_n8n_bot)
##### Copy token HTTP API
<img width="1437" height="949" alt="image" src="https://github.com/user-attachments/assets/83f24122-b69a-4c2e-87a8-afe0a7e014bb" />

##### Chat với bot mới lần đầu:
<img width="1442" height="949" alt="image" src="https://github.com/user-attachments/assets/e7fa07ae-64bd-4137-8bb3-d355f66533a8" />

#### Bước 5.4. Cấu hình node Telegram trong workflow
##### Add trigger node: tìm node: Telegram -> OnMessage ; cấu hình Credential: Set up Credential -> cần Nhập Access Token
<img width="1915" height="946" alt="image" src="https://github.com/user-attachments/assets/b0850663-fa77-4228-9e34-acfa8f0d5e03" />

##### Dán token HTTP API từ BotFather gửi -> Save
<img width="1919" height="942" alt="image" src="https://github.com/user-attachments/assets/cab48db3-b693-478f-9624-f45f182a164f" />

##### Nhấn Test this trigger để kiểm tra, vào Telegram Bot vừa tạo rồi gửi bất kỳ hello
<img width="1440" height="947" alt="image" src="https://github.com/user-attachments/assets/569d0efe-9441-48c4-8a83-3b93e1f52923" />

##### Nếu kết nối thành công, output của node sẽ hiện nội dung
<img width="1912" height="945" alt="Screenshot 2026-05-25 220826" src="https://github.com/user-attachments/assets/e66fa46f-f185-4041-bf88-708bc903d249" />

#### Bước 5.5. Cấu hình node AI Google Gemini
##### Add (nối tiếp vào sau node Telegram Trigger) node: Google Gemini => Message a model
<img width="1919" height="987" alt="Untitled" src="https://github.com/user-attachments/assets/72d061eb-586f-488b-8ff1-f9f75420db15" />

##### Thực hiện Set up Credential -> Cần nhập Token API Key
- Lấy API KEY tại trang: https://aistudio.google.com => https://aistudio.google.com/api-keys
- Tạo project mới, rồi tạo API KEY
