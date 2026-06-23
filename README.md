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
#### Bước 5.1. Truy cập subdomain https://n8n.appwebtuanha.io.vn/ để tạo tài khoản và kích hoạt license
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
<img width="1164" height="724" alt="Screenshot 2026-06-14 220956" src="https://github.com/user-attachments/assets/d57a837b-b963-4e32-ba32-4cd3ddc2fd48" />

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

<img width="1172" height="608" alt="Screenshot 2026-06-14 221132" src="https://github.com/user-attachments/assets/2d8f384f-4916-4bda-a3b8-05c5c84ede40" />


- Nhập API key lên giao diện setup trong n8n rồi nhấn save.

<img width="1914" height="995" alt="image" src="https://github.com/user-attachments/assets/6f49f804-fd83-4b28-bb1a-0f69d86c19c0" />

- Kéo thả nội dung đã chát với bot của telegram (phía bên trái) vào nội dung phần PROMPT kết quả được {{ $json.message.text }}, cần gõ thêm vào sau {{ $json.message.text }} để promt dài hơn
```
{{ $json.message.text }}.Kết quả sinh ra ở định dạng HTML+CSS để tôi dùng HTML+CSS này tạo bài viết cho wordpress.
Trả về JSON với 2 trường:
- post_title
- post_content
```
- Turn on Output Content as JSON : để kết quả trả về dạng json

<img width="1919" height="989" alt="image" src="https://github.com/user-attachments/assets/de95b83c-3e0f-411b-b4a3-d88967387028" />

#### Bước 5.6. Cấu hình node Code in JavaSript
- Add (nối tiếp vào sau node Message a model) node: Code in JavaScript
- Dán code sau:
```
// 1. lấy dữ liệu gốc
const rawText = $input.first().json.content.parts[0].text;

// 2. Làm sạch chuỗi JSON
let cleanText = rawText.trim();

// --- 🌟 BƯỚC SỬA ĐỔI 1: Cắt bỏ chữ thừa của AI trước khối ```json nếu có ---
const markdownIndex = cleanText.indexOf("```json");
if (markdownIndex !== -1) {
    cleanText = cleanText.substring(markdownIndex);
}

// --- 🌟 BƯỚC SỬA ĐỔI 2: Cắt bỏ chữ thừa của AI sau dấu ngoặc nhọn đóng } cuối cùng nếu có ---
const lastBracketIndex = cleanText.lastIndexOf("}");
const lastMarkdownIndex = cleanText.lastIndexOf("```");
if (lastBracketIndex !== -1) {
    // Nếu AI có viết ``` ở cuối, ta giữ lại đến hết dấu ```, nếu không thì giữ đến dấu }
    const cutEndIndex = (lastMarkdownIndex > lastBracketIndex) ? lastMarkdownIndex + 3 : lastBracketIndex + 1;
    cleanText = cleanText.substring(0, cutEndIndex);
}
// -----------------------------------------------------------------------------

// Xóa đoạn ```json ở đầu nếu có (Đoạn này giữ nguyên 100% code cũ của bạn)
if (cleanText.startsWith("```json")) {
  cleanText = cleanText.substring(7);
} else if (cleanText.startsWith("```")) {
  cleanText = cleanText.substring(3);
}

// Xóa đoạn ``` ở cuối nếu có (Đoạn này giữ nguyên 100% code cũ của bạn)
if (cleanText.endsWith("```")) {
  cleanText = cleanText.substring(0, cleanText.length - 3);
}

cleanText = cleanText.trim();

// 3. Chuyển đổi chuỗi thành Object trong JavaScript (Đoạn này giữ nguyên 100% code cũ của bạn)
const cleanData = JSON.parse(cleanText);

// 4. Trả về kết quả định dạng lại gọn gàng cho node WordPress sử dụng (Đoạn này giữ nguyên 100% code cũ của bạn)
return {
  title: cleanData.post_title,
  content: cleanData.post_content
};
```
##### Test output node:

<img width="1914" height="979" alt="image" src="https://github.com/user-attachments/assets/8a6b6cf7-572c-405e-9caf-258e80c66201" />

#### Bước 5.7. Cấu hình node WordPress
- Add (nối tiếp vào sau node Code in JavaScript) node: WordPress => Create a Post
- Lấy "Mật khẩu ứng dụng" (Application Password) từ WordPress
  - Truy cập trang quản trị: https://wordpress.appwebtuanha.io.vn/wp-admin
  - Vào Tài khoản -> chọn user lúc thiết lập -> Mật khẩu ứng dụng -> Nhập tên n8n -> Thêm mật khẩu ứng dụng

<img width="1919" height="737" alt="image" src="https://github.com/user-attachments/assets/fe72cec1-85cb-4b5f-bc52-cefecada3186" />

<img width="1758" height="490" alt="image" src="https://github.com/user-attachments/assets/0e15f1e9-ce7a-47c1-8534-b4a06ea786d4" />

##### Copy mật khẩu rồi dán vào password của n8n credential, điền các thông tin -> save
- Ignore SSL Issues (Insecure): TURN ON
- Wordpress URL: điền giá trị https://wordpress.appwebtuanha.io.vn/ (subdomain1)
<img width="1917" height="991" alt="image" src="https://github.com/user-attachments/assets/d404c867-c41e-423a-b820-c893e1ff9c9f" />

##### Mapping dữ liệu
- Kéo thả tiêu đề (Title): Nhìn sang cột dữ liệu bên trái của node JavaScript trước đó, bạn sẽ thấy cột title. Nhấp giữ chuột vào trường title này và kéo thả trực tiếp vào ô nhập liệu Title của node WordPress. (Nó sẽ tự động điền mã biểu thức dạng {{ $json.title }}).
- Kéo thả nội dung (Content): Tương tự, nhấp giữ chuột vào trường content ở cột bên trái và kéo thả vào ô nhập liệu Content của node WordPress. (Nó sẽ tự động điền {{ $('Message a model').item.json.content }}).
- Cấu hình Trạng thái xuất bản (Status):

##### Nhấp vào nút Add Field (Thêm thuộc tính) ở cuối cấu hình.
- Chọn thuộc tính Status.
- Ở ô giá trị của Status, chọn là Publish (để bài viết hiển thị công khai trên web ngay lập tức thay vì nằm ở mục Bản nháp - Draft).

<img width="1912" height="984" alt="image" src="https://github.com/user-attachments/assets/cb2f640c-5f27-4dc0-9ecd-95a916406e79" />

##### Workflow hoàn chỉnh

<img width="1917" height="983" alt="image" src="https://github.com/user-attachments/assets/785d0ecc-420a-4eb6-9f49-53d3029e4a16" />

#### Bước 5.8. PUBLISH FLOW

##### Nút này thực hiện việc xuất bản flow <=> flow sẽ tự động thực thi khi thoả mãn điều kiện trigger

<img width="1916" height="995" alt="image" src="https://github.com/user-attachments/assets/02d1f169-c378-49e4-be23-a6e9d55c52c5" />

### 3. KẾT QUẢ ĐẠT ĐƯỢC
#### 3.1. Kết quả demo
##### Chat với tele bot với yêu cầu : 5 Lợi Ích Tuyệt Vời Của Việc Đọc Sách Mỗi Ngày Bạn Nên Biết

<img width="1439" height="946" alt="image" src="https://github.com/user-attachments/assets/f92af5f7-94e1-4c2d-9236-7ace204492a7" />

##### Flow n8n:

<img width="1913" height="987" alt="image" src="https://github.com/user-attachments/assets/c2e36385-0c60-4e84-aa18-27dce702ce2e" />

##### Bài đăng trên Wordpress

<img width="1911" height="952" alt="image" src="https://github.com/user-attachments/assets/1f6ca9b8-4794-42f6-9473-6721093b0c5e" />

### 4. NHẬN XÉT
#### 4.1. Quy trình vận hành hệ thống
Hệ thống hoạt động theo mô hình khép kín tự động hóa hoàn toàn:

- Yêu cầu đầu vào: Người dùng gửi yêu cầu viết bài bằng tiếng Việt qua Telegram Bot của cá nhân.

- Kích hoạt & Xử lý: Node Telegram Trigger trên n8n nhận tin nhắn và chuyển tiếp tới Google Gemini AI.

- Sinh nội dung: Gemini AI nhận diện ngữ cảnh, tự động thiết kế bài viết chuẩn định dạng HTML/CSS và đóng gói dưới dạng cấu trúc JSON.

- Lọc dữ liệu: Node Code JavaScript lọc sạch các ký tự markdown thừa, chuẩn hóa hai trường dữ liệu title và content.

- Xuất bản: Node WordPress kết nối qua Application Password bảo mật, tự động tạo và xuất bản bài viết trực tiếp lên website.

#### 4.2. Những điều đã đạt được & Kiến thức tích lũy
- Triển khai hạ tầng Container: Làm chủ kỹ thuật cấu trúc file docker-compose.yml để chạy đồng thời nhiều service liên kết chặt chẽ (MariaDB, WordPress, phpMyAdmin, n8n, Cloudflared).

- Tự động hóa & Tích hợp AI: Làm quen với nền tảng n8n, cách xây dựng kịch bản (workflow) tích hợp các API hiện đại như Telegram API, Google Gemini API và WordPress Rest API.

- Xử lý dữ liệu: Ứng dụng JavaScript cơ bản để xử lý, làm sạch chuỗi và phân tích cú pháp (parse JSON) thực tế, khắc phục triệt để lỗi phân quyền (permission) dữ liệu trong Docker.

#### 4.3. Những vấn đề tồn tại & Hướng phát triển
Tính ổn định của AI: Đôi khi Gemini AI có thể phản hồi sai cấu trúc JSON mong muốn dẫn đến lỗi phân tích cú pháp ở node JS (đã được khắc phục tạm thời bằng mã xử lý lỗi nâng cao).

Quản lý tài nguyên: Việc chạy nhiều service nặng trên môi trường ảo hóa Ubuntu cần được tối ưu cấu hình để tránh hiện tượng tràn RAM.

Hướng phát triển: Tích hợp thêm AI tạo ảnh (như Imagen) để tự động sinh ảnh đại diện (Featured Image) cho bài viết WordPress giúp bài đăng sinh động hơn.

# Bài 5: APP-MONITOR-ALERT-DATA-REALTIME
# Lý thuyết
## 1. Docker
Docker là một nền tảng mã nguồn mở cho phép bạn đóng gói ứng dụng và tất cả các thành phần phụ thuộc của nó (libraries, môi trường runtime, cấu hình hệ thống...) vào trong một đơn vị duy nhất gọi là Container.

Để dễ hình dung:

- Trước khi có Docker: Bạn chạy ứng dụng trực tiếp trên hệ điều hành (hoặc Máy ảo - VM). Việc này dễ dẫn đến lỗi "Trên máy tôi chạy được nhưng lên server lại lỗi" do khác biệt về phiên bản thư viện hoặc môi trường.

- Khi có Docker: Ứng dụng chạy trong một môi trường cô lập (Container). Nó hoạt động y hệt nhau dù ở trên laptop của bạn, trên máy của đồng nghiệp, hay trên server production.

## 2. Các keyword trong docker-compose.yml
File docker-compose.yml dùng để định nghĩa và quản lý một ứng dụng gồm nhiều container (multi-container). Dưới đây là các từ khóa phổ biến chia theo từng thành phần:

### Định nghĩa Service (Dịch vụ / Container)
Mỗi service đại diện cho một container sẽ được khởi tạo.

- image: Chỉ định Docker Image được sử dụng để tạo container.

  - Ý nghĩa: Tải từ Docker Hub hoặc local registry nếu có sẵn.
  - Ví dụ: image: postgres:15-alpine

- build: Chỉ định đường dẫn đến thư mục chứa Dockerfile để Docker Compose tự build image tại chỗ thay vì tải image có sẵn.
  - Ví dụ: build: ./backend

- ports: Ánh xạ (mapping) cổng từ Máy host (máy thật) vào trong Container. Cấu trúc là HOST:CONTAINER.
  - Ví dụ: ports: - "8080:80" (Truy cập từ bên ngoài qua cổng 8080 sẽ dẫn vào cổng 80 của container).

- environment: Khai báo các biến môi trường (Environment Variables) bên trong container.
  - Ví dụ: ```yaml

    environment:
    - DB_HOST=db
    - DB_USER=root

- volumes: Gắn một thư mục/file từ máy host vào container, hoặc gắn một Docker Volume vào container để lưu trữ dữ liệu bền vững.
  - Ví dụ: - ./data:/var/lib/mysql (Lưu dữ liệu MySQL ra máy host để không bị mất khi xóa container).

- networks: Chỉ định container này tham gia vào mạng nội bộ nào để giao tiếp với các container khác.
  - Ví dụ: ```yaml
  
    networks:
    - app-network

- depends_on: Thiết lập thứ tự khởi động giữa các service.
  - Ví dụ: depends_on: - db (Ứng dụng web sẽ đợi container database khởi động trước rồi mới khởi động sau).

### Định nghĩa Network (Mạng)
Dùng để tạo một không gian mạng cô lập cho các container giao tiếp với nhau bằng tên service thay vì địa chỉ IP.

- networks (cấp cao nhất): Khai báo các mạng sẽ dùng trong file compose.

- driver: Chỉ định kiểu mạng (thường mặc định là bridge trên một máy đơn lẻ).

  - Ví dụ:
  ```
    networks:
      my-net:
        driver: bridge
  ```
### Định nghĩa Volume (Phần lưu trữ dữ liệu)
Dùng để quản lý vòng đời của dữ liệu một cách độc lập với container. Khi container bị xóa, dữ liệu trong volume vẫn an toàn.

- volumes (cấp cao nhất): Khai báo các volume được Docker quản lý.

  - Ví dụ:
  ```
  volumes:
    db_data: # Tạo một vùng lưu trữ tên là db_data
  ```
### 📝 Ví dụ minh họa một file docker-compose.yml hoàn chỉnh:
```
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db
    networks:
      - back-tier

  db:
    image: postgres:15
    volumes:
      - db_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=mysecretpassword
    networks:
      - back-tier

volumes:
  db_data:

networks:
  back-tier:
    driver: bridge
```

### 3. Ưu điểm khi triển khai ứng dụng sử dụng Docker
- Nhất quán môi trường (Consistency): Loại bỏ hoàn toàn lỗi "chạy được trên máy em nhưng lỗi trên server". Dev, test, production dùng chung một môi trường 100%.
- Triển khai nhanh chóng và gọn nhẹ (Speed & Light): Container khởi động chỉ trong vài giây vì chúng chia sẻ chung nhân (kernel) của hệ điều hành host, không cần khởi động lại toàn bộ OS như máy ảo (VM).
- Cô lập an toàn (Isolation): Mỗi container hoạt động độc lập. Một container bị lỗi hay sập nguồn không làm ảnh hưởng đến các container khác trên cùng một máy chủ.
- Dễ dàng mở rộng (Scalability): Rất dễ dàng tăng số lượng container (scale up) khi lượng traffic tăng đột biến và giảm xuống (scale down) khi hết tải.
- Tiết kiệm tài nguyên: Sử dụng RAM và CPU hiệu quả hơn nhiều so với việc chạy nhiều Máy ảo (Virtual Machines).

## 4. Các bước triển khai ứng dụng bằng Docker lên Server KHÔNG CÓ INTERNET (Offline Deployment)
Khi server thật không có internet (môi trường Air-gapped), bạn không thể dùng lệnh docker pull hay docker-compose up để tải image từ Docker Hub trực tiếp trên server đó. Quy trình chuẩn sẽ bao gồm các bước sau:

### Bước 1: Chuẩn bị tại Laptop cá nhân (Có Internet)
Bạn cần chuẩn bị sẵn các Docker Image và bộ cài đặt Docker.

1. Build ứng dụng thành Image: Chạy lệnh build ứng dụng của bạn thành Docker Image hoàn chỉnh.

2. Đóng gói các Image thành file .tar: Sử dụng lệnh docker save để xuất các image (bao gồm cả image ứng dụng của bạn và các image phụ thuộc như Nginx, Postgres, MySQL...) ra thành file nén.

- Lệnh: docker save -o my-app-image.tar my-app:latest

- Lệnh cho DB: docker save -o postgres-image.tar postgres:15

3. Tải file cài đặt Docker Offline cho Server: Tải file cài đặt Docker dạng .rpm hoặc .deb (tùy thuộc OS của server là CentOS hay Ubuntu) cùng với công cụ docker-compose dạng binary về laptop.

### Bước 2: Chuyển dữ liệu sang Server thật (Offline)
- Sử dụng USB, ổ cứng di động hoặc mạng LAN nội bộ (nếu có kết nối an toàn với server) để copy các file sau sang server:
  - Các file .tar (chứa Docker Images).
  - Bộ cài đặt Docker Offline và Docker Compose binary.
  - Thư mục mã nguồn chứa file docker-compose.yml (đã chỉnh sửa đường dẫn image để trỏ đúng vào các image local).

### Bước 3: Cài đặt và Triển khai trên Server thật
1. Cài đặt Docker Offline: Cài đặt Docker từ các file package (.rpm / .deb) đã copy sang. Di chuyển file docker-compose vào thư mục /usr/local/bin/ và cấp quyền thực thi (chmod +x).

2. Nạp (Load) các Image vào Docker: Sử dụng lệnh docker load để giải nén các file .tar ngược trở lại vào bộ quản lý image của server.

- Lệnh: docker load -i my-app-image.tar

- Lệnh: docker load -i postgres-image.tar

3. Khởi chạy ứng dụng: Di chuyển vào thư mục chứa file docker-compose.yml và chạy lệnh như bình thường:

- Lệnh: docker-compose up -d
  Docker Compose sẽ phát hiện các image đã có sẵn ở local (do bạn vừa load xong) và tiến hành dựng container ngay lập tức mà không cần kết nối Internet để tải gì thêm.



# Thực hành 
## 1. Tổng quan hệ thống
### 1.1. Giới thiệu
Dự án xây dựng một hệ thống giám sát và cảnh báo tự động theo thời gian thực (App Monitor + Alert Data Realtime) chạy trên nền tảng Docker Container. Đối tượng giám sát thực tế được lựa chọn ở đây là dữ liệu thời tiết thu thập trực tiếp thông qua API.

Các thành phần cốt lõi trong hệ thống:

- Node-RED: Đóng vai trò là trung tâm điều phối dữ liệu (ETL Workflow). Thực hiện tác vụ lấy dữ liệu động liên tục, phân tích dị thường, ghi đồng thời vào 2 cơ sở dữ liệu và kích hoạt bot Telegram gửi cảnh báo khi có sự cố về giá.
- MariaDB: Cơ sở dữ liệu quan hệ (RDBMS) dùng để lưu trữ trạng thái tức thời (giá trị mới nhất) nhằm phục vụ các truy vấn nhanh của ứng dụng Web Client.
- InfluxDB (v1.8): Cơ sở dữ liệu chuỗi thời gian (Time-series Database) tối ưu cho việc lưu trữ dữ liệu lịch sử, phục vụ vẽ biểu đồ phân tích xu hướng theo thời gian.
- Flask API (Python): Xây dựng dịch vụ API nội bộ kết nối trực tiếp với MariaDB, cung cấp endpoint endpoint trả về dữ liệu giá mới nhất dạng JSON cho giao diện người dùng.
- Nginx: Web Server phân phối giao diện Frontend tĩnh và đồng thời đóng vai trò làm Reverse Proxy điều hướng luồng request từ trình duyệt client sang Flask API một cách an toàn thông qua cấu hình mạng nội bộ Docker.
- Grafana: Nền tảng trực quan hóa dữ liệu mạnh mẽ, kết nối với InfluxDB để vẽ biểu đồ kỹ thuật và cho phép nhúng trực tiếp vào giao diện Frontend qua thẻ Iframe không cần đăng nhập.

### 1.2. Cấu trúc thư mục dự án
```
weather-monitor/
├── docker-compose.yml
├── nginx/
│   ├── default.conf
│   └── html/
│       ├── index.html
├── flask_api/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── app.py
├── nodered_data/          
├── influxdb_data/         
└── mariadb_data/
```
### 1.3. Danh sách service và cổng
## Cấu hình Port Mapping các Service Docker

| Tên Service (Docker) | Cổng Nội bộ (Container) | Cổng Công khai (Host) | Mục đích sử dụng |
|----------------------|-------------------------|-----------------------|------------------|
| `web_nginx` | `80` | `8085` | Giao diện Frontend (HTML/JS) & Reverse Proxy |
| `weather_flask` | `5000` | Không mở (Ẩn) | Cung cấp API lấy dữ liệu tức thời từ MariaDB |
| `weather_nodered` | `1880` | `1882` | Thu thập dữ liệu thời tiết, lưu DB, gửi Telegram |
| `weather_mariadb` | `3306` | `3309` | Lưu trữ dữ liệu thời tiết tức thời (Latest) |
| `weather_influxdb` | `8086` | `8089` | Lưu trữ dữ liệu lịch sử (Time-series) |
| `weather_grafana` | `3000` | `3002` | Trực quan hóa biểu đồ lịch sử (Nhúng Iframe) |

> **Lưu ý mạng nội bộ:** Các service giao tiếp với nhau bằng tên service bên trong mạng Docker chung `weather_network`.

## 2. Cấu hình
### 2.1. Cấu hình file docker-compose.yml
```
# version: '3.8'

networks:
  weather_network:
    driver: bridge

services:
  # 1. Database Lưu Tức Thời
  weather_mariadb:
    image: mariadb:10.11
    container_name: weather_mariadb
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: weather_db
      MYSQL_USER: weather_user
      MYSQL_PASSWORD: weather_password
    ports:
      - "3309:3306" 
    volumes:
      - ./mariadb_data:/var/lib/mysql
    networks:
      - weather_network
    restart: always

  # 2. Database Lưu Lịch Sử
  weather_influxdb:
    image: influxdb:1.8
    container_name: weather_influxdb
    environment:
      - INFLUXDB_DB=weather_history
      - INFLUXDB_ADMIN_USER=admin
      - INFLUXDB_ADMIN_PASSWORD=adminpassword
    ports:
      - "8089:8086"
    volumes:
      - ./influxdb_data:/var/lib/influxdb
    networks:
      - weather_network
    restart: always

  # 3. Node-RED (Trung tâm xử lý ETL)
  weather_nodered:
    image: nodered/node-red:latest
    container_name: weather_nodered
    ports:
      - "1882:1880"
    volumes:
      - ./nodered_data:/data
    networks:
      - weather_network
    restart: always

  # 4. Grafana (Vẽ biểu đồ và cho phép nhúng Iframe)
  weather_grafana:
    image: grafana/grafana:latest
    container_name: weather_grafana
    ports:
      - "3002:3000"
    environment:
      - GF_SECURITY_ALLOW_EMBEDDING=true # Cho phép nhúng vào Iframe HTML
      - GF_AUTH_ANONYMOUS_ENABLED=true   # Cho phép xem biểu đồ không cần login
      - GF_AUTH_ANONYMOUS_ORG_ROLE=Viewer
    volumes:
      - ./grafana_data:/var/lib/grafana
    networks:
      - weather_network
    restart: always
    depends_on:
      - weather_influxdb

  # 5. Flask API (Sẽ build từ code ở Giai đoạn sau)
  weather_flask:
    build: ./flask_api
    container_name: weather_flask
    # Không cần export ports ra ngoài vì Nginx sẽ gọi nó từ bên trong mạng Docker
    networks:
      - weather_network
    depends_on:
      - weather_mariadb
    restart: always

  # 6. Nginx Web Server & Reverse Proxy
  web_nginx:
    image: nginx:latest
    container_name: web_nginx_weather
    ports:
      - "8085:80" 
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./nginx/html:/usr/share/nginx/html
    networks:
      - weather_network
    depends_on:
      - weather_flask
    restart: always
```
<img width="1112" height="623" alt="image" src="https://github.com/user-attachments/assets/4837cefe-648f-4cdb-afbf-9eeac8df07f0" />

### 2.2. Xây dựng Flask API
#### Bước 1: Khai báo các thư viện cần thiết trong flask_api/requirements.txt
Khai báo các thư viện Python cần thiết để kết nối MariaDB và chạy API.
```
Flask==3.0.3
mysql-connector-python==8.3.0
Flask-Cors==4.0.1
```
<img width="1108" height="624" alt="image" src="https://github.com/user-attachments/assets/ed1dd034-3c91-4a39-bb0e-2174b5cbae40" />

### Bước 2: Edit file app.py
Đoạn code này sẽ tạo một API endpoint /api/weather để kết nối vào MariaDB (sử dụng tên service weather_mariadb làm host) và lấy ra bản ghi thời tiết mới nhất.
```
from flask import Flask, jsonify
from flask_cors import CORS
import mysql.connector as mysql
import os
import time

app = Flask(__name__)
CORS(app) # Cho phép gọi API từ bên ngoài nếu cần

def get_db_connection():
    # Thử kết nối lại nhiều lần đề phòng MariaDB khởi động chậm hơn Flask
    for i in range(5):
        try:
            conn = mysql.connect(
                host='weather_mariadb', # Tên service trong docker-compose
                user='weather_user',
                password='weather_password',
                database='weather_db'
            )
            return conn
        except mysql.Error:
            time.sleep(2)
    return None

# Tạo bảng tự động nếu chưa có (để hệ thống không bị lỗi khi mới start)
def init_db():
    conn = get_db_connection()
    if conn:
        cursor = conn.cursor()
        cursor.execute("""
            CREATE TABLE IF NOT EXISTS weather_live (
                id INT AUTO_INCREMENT PRIMARY KEY,
                temperature FLOAT,
                humidity FLOAT,
                rainfall FLOAT,
                wind_speed FLOAT,
                timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
            )
        """)
        # Chèn thử 1 dòng dữ liệu mẫu ban đầu nếu bảng trống
        cursor.execute("SELECT COUNT(*) FROM weather_live")
        if cursor.fetchone()[0] == 0:
            cursor.execute("""
                INSERT INTO weather_live (temperature, humidity, rainfall, wind_speed) 
                VALUES (27.5, 80.0, 0.0, 3.5)
            """)
        conn.commit()
        cursor.close()
        conn.close()

@app.route('/api/weather', methods=['GET'])
def get_weather():
    conn = get_db_connection()
    if not conn:
        return jsonify({"error": "Không thể kết nối cơ sở dữ liệu"}), 500
    
    cursor = conn.cursor(dictionary=True)
    # Lấy bản ghi mới nhất dựa vào timestamp hoặc ID
    cursor.execute("SELECT temperature, humidity, rainfall, wind_speed, timestamp FROM weather_live ORDER BY id DESC LIMIT 1")
    result = cursor.fetchone()
    
    cursor.close()
    conn.close()
    
    if result:
        # Định dạng lại thời gian thành chuỗi để JSON hóa
        result['timestamp'] = result['timestamp'].strftime('%Y-%m-%d %H:%M:%S')
        return jsonify(result)
    return jsonify({"message": "Chưa có dữ liệu"}), 404

if __name__ == '__main__':
    init_db()
    # Chạy ở port 5000 bên trong container
    app.run(host='0.0.0.0', port=5000)
```

<img width="1118" height="625" alt="image" src="https://github.com/user-attachments/assets/82fd6ea2-6217-4d17-b640-d4947ce53f99" />


### Bước 3: Edit file Dockerfile
File này dùng để Docker đóng gói ứng dụng Flask thành một Image riêng.
```
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

<img width="1117" height="554" alt="image" src="https://github.com/user-attachments/assets/5e829bcd-be63-4ec6-8629-ab2c943f2638" />

## 2.3. Cấu hình Nginx làm Web Server & Reverse Proxy
- File: nginx/default.conf
- File này cấu hình Nginx lắng nghe ở cổng 80 (nội bộ container). Nếu user vào trang chủ / thì trả về file HTML, nếu vào tuyến đường /api/ thì Nginx sẽ "bắn" request đó sang cho container Flask xử lý.
```
server {
    listen 80;
    server_name localhost;

    # 1. Định tuyến cho Frontend (HTML/JS/CSS tĩnh)
    location / {
        root /usr/share/nginx/html;
        index index.html;
        try_files $uri $uri/ =404;
    }

    # 2. Định tuyến Reverse Proxy cho Flask API
    location /api/ {
        proxy_pass http://weather_flask:5000; # Tên service Flask và port nội bộ của nó
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
<img width="1112" height="585" alt="image" src="https://github.com/user-attachments/assets/467df6d9-4384-4d3b-a4ef-a85f350dc482" />

## 2.4. Tạo file giao diện web
Viết file index.html làm frontend cho hệ thống.
```
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>WEATHER MONITORING DASHBOARD | Hệ thống Giám sát Thời tiết IoT</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,500;14..32,600;14..32,700;14..32,800&display=swap" rel="stylesheet">
    <style>
        /* ----- RESET & GLOBAL ----- */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --bg-gradient: linear-gradient(145deg, #eef2fa 0%, #e0e8f0 100%);
            --card-white: rgba(255, 255, 255, 0.96);
            --glass-border: 1px solid rgba(255,255,255,0.6);
            --text-main: #1a2c3e;
            --text-soft: #4a627a;
            --shadow-card: 0 12px 28px -8px rgba(0, 0, 0, 0.08), 0 4px 12px rgba(0, 0, 0, 0.02);
            --shadow-hover: 0 20px 32px -12px rgba(0, 0, 0, 0.15);
            --accent-temp: #ff6b4a;
            --accent-humid: #2b8cbe;
            --accent-rain: #3b9eff;
            --accent-wind: #48b884;
        }

        body {
            font-family: 'Inter', sans-serif;
            background: var(--bg-gradient);
            color: var(--text-main);
            padding: 32px 20px;
            min-height: 100vh;
        }

        .container {
            max-width: 1280px;
            margin: 0 auto;
            width: 100%;
        }

        /* ----- HEADER ----- */
        header {
            text-align: center;
            margin-bottom: 32px;
        }

        header h1 {
            font-size: 2.4rem;
            font-weight: 700;
            letter-spacing: -0.02em;
            background: linear-gradient(120deg, #1e4668, #0f2f48);
            background-clip: text;
            -webkit-background-clip: text;
            color: transparent;
            display: inline-block;
        }

        header p {
            color: var(--text-soft);
            font-weight: 500;
            margin-top: 8px;
            font-size: 0.95rem;
            border-top: 1px solid rgba(0,0,0,0.05);
            display: inline-block;
            padding-top: 6px;
        }

        /* ----- STATUS BAR (đẹp hơn, giữ nguyên nội dung) ----- */
        .status-wrapper {
            display: flex;
            justify-content: center;
            margin-bottom: 36px;
        }

        .status-bar {
            background: rgba(255, 255, 255, 0.85);
            backdrop-filter: blur(6px);
            border-radius: 60px;
            padding: 10px 24px;
            display: inline-flex;
            align-items: center;
            gap: 24px;
            flex-wrap: wrap;
            justify-content: center;
            box-shadow: var(--shadow-card);
            border: var(--glass-border);
            font-size: 0.85rem;
            font-weight: 500;
            color: var(--text-soft);
        }

        .pulse-dot {
            width: 10px;
            height: 10px;
            background-color: #1fbc73;
            border-radius: 50%;
            box-shadow: 0 0 0 0 #1fbc73;
            animation: pulse-green 1.8s infinite;
            margin-right: 6px;
        }

        @keyframes pulse-green {
            0% { transform: scale(0.95); opacity: 1; box-shadow: 0 0 0 0 rgba(31, 188, 115, 0.6); }
            70% { transform: scale(1.1); opacity: 0.8; box-shadow: 0 0 0 8px rgba(31, 188, 115, 0); }
            100% { transform: scale(0.95); opacity: 1; box-shadow: 0 0 0 0 rgba(31, 188, 115, 0); }
        }

        .status-bar span#update-countdown, .status-bar span#time-stamp {
            font-weight: 700;
            color: #1f4870;
            background: rgba(0,0,0,0.03);
            padding: 2px 8px;
            border-radius: 40px;
        }

        /* ----- GRID 4 CARD ----- */
        .live-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
            gap: 24px;
            margin-bottom: 48px;
        }

        /* Card đẹp hơn, bo góc mềm, chuyển động nhẹ */
        .card {
            background: var(--card-white);
            backdrop-filter: blur(2px);
            border-radius: 28px;
            padding: 24px 20px;
            box-shadow: var(--shadow-card);
            transition: all 0.25s ease;
            border: 1px solid rgba(255,255,255,0.7);
            position: relative;
            overflow: hidden;
        }

        .card:hover {
            transform: translateY(-5px);
            box-shadow: var(--shadow-hover);
        }

        /* Đường viền màu trên cùng thanh lịch */
        .card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 5px;
            background: var(--primary);
        }
        .card.temp::before { background: linear-gradient(90deg, #ff8a5c, #ff5e62); }
        .card.humidity::before { background: linear-gradient(90deg, #3a9bd5, #1e88e5); }
        .card.rain::before { background: linear-gradient(90deg, #4ea8de, #2b6fbf); }
        .card.wind::before { background: linear-gradient(90deg, #5cb85c, #2e8b57); }

        .card-title {
            font-size: 0.85rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: var(--text-soft);
            margin-bottom: 14px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        /* Icon nhẹ nhàng thêm vào (chỉ trang trí, không ảnh hưởng logic) */
        .card-title i {
            font-size: 1.2rem;
            opacity: 0.7;
        }

        .card-value {
            font-size: 2.8rem;
            font-weight: 800;
            color: var(--text-main);
            line-height: 1.1;
            display: flex;
            align-items: baseline;
            gap: 4px;
            margin-bottom: 6px;
        }

        .card-value span.unit {
            font-size: 1.1rem;
            font-weight: 500;
            color: var(--text-soft);
        }

        /* Thêm micro text trang trí (không thay đổi chức năng) */
        .card-footer-note {
            font-size: 0.7rem;
            color: #8ba0b2;
            margin-top: 12px;
            border-top: 1px dashed rgba(0,0,0,0.05);
            padding-top: 8px;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        /* ----- CHART SECTION (giữ nguyên iframe, chỉ làm đẹp khung) ----- */
        .chart-section {
            background: rgba(255, 255, 255, 0.92);
            backdrop-filter: blur(4px);
            border-radius: 28px;
            padding: 24px 28px;
            box-shadow: var(--shadow-card);
            border: 1px solid rgba(255,255,255,0.6);
            transition: all 0.2s;
        }

        .chart-section h2 {
            font-size: 1.35rem;
            font-weight: 600;
            margin-bottom: 20px;
            color: #1e3a5f;
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .iframe-container {
            position: relative;
            width: 100%;
            padding-top: 46%;
            overflow: hidden;
            border-radius: 20px;
            background: #f1f5f9;
            box-shadow: inset 0 1px 4px rgba(0,0,0,0.02), 0 2px 6px rgba(0,0,0,0.05);
        }

        .iframe-container iframe {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border: none;
            border-radius: 16px;
        }

        /* Responsive tinh tế */
        @media (max-width: 680px) {
            body {
                padding: 20px 16px;
            }
            .card-value {
                font-size: 2.2rem;
            }
            .status-bar {
                gap: 12px;
                padding: 8px 18px;
                font-size: 0.75rem;
            }
            .chart-section {
                padding: 18px;
            }
            .iframe-container {
                padding-top: 65%;
            }
        }

        /* footer nhẹ nhàng (không thay đổi tính năng) */
        .footer-credit {
            text-align: center;
            margin-top: 32px;
            font-size: 0.7rem;
            color: #7c8f9f;
            border-top: 1px solid rgba(0,0,0,0.05);
            padding-top: 20px;
        }
    </style>
    <!-- Font Awesome nhẹ cho icon (chỉ làm đẹp) -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
</head>
<body>

<div class="container">
    <header>
        <h1><i class="fas fa-cloud-sun-rain" style="margin-right: 10px; color: #2c7da0;"></i> WEATHER MONITORING DASHBOARD</h1>
        <p>Hệ thống giám sát và phân tích dữ liệu thời tiết thời gian thực</p>
    </header>

    <div class="status-wrapper">
        <div class="status-bar">
            <div style="display: flex; align-items: center; gap: 6px;">
                <div class="pulse-dot"></div>
                <span>Dữ liệu tức thời (MariaDB):</span>
            </div>
            <span>Cập nhật sau <span id="update-countdown" style="font-weight:700;">5</span> giây</span>
            <span>Lúc: <span id="time-stamp" style="font-weight: 600;">--:--:--</span></span>
        </div>
    </div>

    <section class="live-grid">
        <!-- Card Nhiệt độ -->
        <div class="card temp">
            <div class="card-title">
                <i class="fas fa-thermometer-half"></i> Nhiệt độ
            </div>
            <div class="card-value">
                <span id="temp-val">--</span><span class="unit">°C</span>
            </div>
            <div class="card-footer-note">
                <i class="fas fa-chart-line"></i> Cảm biến DS18B20
            </div>
        </div>

        <!-- Card Độ ẩm -->
        <div class="card humidity">
            <div class="card-title">
                <i class="fas fa-tint"></i> Độ ẩm
            </div>
            <div class="card-value">
                <span id="humid-val">--</span><span class="unit">%</span>
            </div>
            <div class="card-footer-note">
                <i class="fas fa-water"></i> Độ ẩm tương đối
            </div>
        </div>

        <!-- Card Lượng mưa -->
        <div class="card rain">
            <div class="card-title">
                <i class="fas fa-cloud-rain"></i> Lượng mưa
            </div>
            <div class="card-value">
                <span id="rain-val">--</span><span class="unit">mm</span>
            </div>
            <div class="card-footer-note">
                <i class="fas fa-umbrella"></i> 24h qua
            </div>
        </div>

        <!-- Card Tốc độ gió -->
        <div class="card wind">
            <div class="card-title">
                <i class="fas fa-wind"></i> Tốc độ gió
            </div>
            <div class="card-value">
                <span id="wind-val">--</span><span class="unit">m/s</span>
            </div>
            <div class="card-footer-note">
                <i class="fas fa-compass"></i> Gió giật tức thời
            </div>
        </div>
    </section>

    <section class="chart-section">
        <h2><i class="fas fa-chart-area"></i> Phân tích Xu hướng Lịch sử (Grafana - InfluxDB)</h2>
        <div class="iframe-container">
            <iframe src="http://localhost:3002/d-solo/your-dashboard-id/weather-history?orgId=1&refresh=5s&panelId=1" width="100%" height="100%" frameborder="0" title="Grafana Weather History"></iframe>
        </div>
    </section>

    <div class="footer-credit">
        <i class="fas fa-microchip"></i> IoT Weather Station | Dữ liệu đồng bộ qua API /api/weather
    </div>
</div>

<script>
    // === GIỮ NGUYÊN TOÀN BỘ LOGIC GỐC, CHỈ LÀM ĐẸP GIAO DIỆN ===
    async function fetchLiveWeather() {
        try {
            // Gọi API thông qua Reverse Proxy của Nginx (giữ nguyên endpoint)
            const response = await fetch('/api/weather');
            if (response.ok) {
                const data = await response.json();
                
                // Cập nhật giá trị vào các thẻ HTML tương ứng
                document.getElementById('temp-val').innerText = data.temperature !== undefined ? data.temperature : '--';
                document.getElementById('humid-val').innerText = data.humidity !== undefined ? data.humidity : '--';
                document.getElementById('rain-val').innerText = data.rainfall !== undefined ? data.rainfall : '--';
                document.getElementById('wind-val').innerText = data.wind_speed !== undefined ? data.wind_speed : '--';
                
                // Cập nhật nhãn thời gian hiển thị
                document.getElementById('time-stamp').innerText = data.timestamp || new Date().toLocaleTimeString();
            } else {
                console.error("API trả về lỗi hoặc chưa có dữ liệu.");
                // Không thay đổi dữ liệu cũ, giữ nguyên --
            }
        } catch (error) {
            console.error("Lỗi kết nối tới API endpoint:", error);
        }
    }

    // Gọi ngay lần đầu tiên khi tải trang
    fetchLiveWeather();

    // Thiết lập vòng lặp tự động lấy dữ liệu sau mỗi 5 giây (5000ms)
    let intervalId = setInterval(fetchLiveWeather, 5000);

    // Optional: Đếm ngược hiển thị trên thanh status (giữ nguyên hiệu ứng đồng hồ đếm ngược)
    let countdown = 5;
    const countdownSpan = document.getElementById('update-countdown');
    if (countdownSpan) {
        setInterval(() => {
            if (countdown <= 1) {
                countdown = 5;
            } else {
                countdown--;
            }
            countdownSpan.innerText = countdown;
        }, 1000);
    }
</script>
</body>
</html>
```
<img width="1111" height="588" alt="image" src="https://github.com/user-attachments/assets/dcc4f926-9188-464d-86b3-25bfb499984e" />

### 2.5. Khởi động hệ thống
Sau khi hoàn tất cấu hình, build và khởi động toàn bộ hệ thống:
```
docker compose up -d --build
```
<img width="1116" height="590" alt="image" src="https://github.com/user-attachments/assets/53e4ae3c-d3f0-45c3-9744-3cb5c7df5145" />
Kiểm tra trạng thái các container
```
docker ps
```
<img width="1898" height="333" alt="image" src="https://github.com/user-attachments/assets/b0b02213-6723-49df-b9ce-13430f4ac3fe" />

### 2.6. Cấu hình NODERED để tự động hóa luồng dữ liệu
Trong giai đoạn này, Node-RED sẽ đóng vai trò đầu não thực hiện 4 nhiệm vụ liên tục:

- Cào dữ liệu thời tiết thực tế từ API công khai. (mỗi 10-30 giây)
- Lưu trạng thái mới nhất vào MariaDB.
- Lưu lịch sử vào InfluxDB (để Grafana vẽ biểu đồ).
- Phân tích dị thường và kích hoạt Telegram Bot gửi tin nhắn cảnh báo vào Group.
### Bước 1: Chuẩn bị thư viện (nodes) trong Node-RED
Truy cập Node-RED qua địa chỉ http://<IP_máy_chủ_Ubuntu>:1882
```
http://192.168.183.130:1882
```
Click vào Menu (3 dấu gạch ngang góc trên bên phải) -> Chọn Manage palette.Chuyển sang thẻ Install, tìm kiếm và nhấn Install lần lượt 3 thư viện sau:
```
node-red-node-mysql (Kết nối MariaDB)

node-red-contrib-influxdb (Kết nối InfluxDB)

node-red-contrib-telegrambot (Kết nối Telegram)
```
<img width="1914" height="989" alt="image" src="https://github.com/user-attachments/assets/ffb39255-1de5-4dfb-a7ee-eae316f3bb40" />

### Bước 2: Chuẩn bị thông tin Telegram Bot
Trước khi viết Flow, cần chuẩn bị thông tin từ Telegram:

Bot Token: Chat với @BotFather trên Telegram, gõ lệnh /newbot, đặt tên cho bot. Sau khi tạo xong, @BotFather sẽ cấp một chuỗi Token. Copy token này để bước sau dán vào Nodered.
<img width="1440" height="941" alt="image" src="https://github.com/user-attachments/assets/de11b713-b269-434d-83e8-d28333f16b65" />

Tạo nhóm chat có bot để cảnh báo:

- Tạo một Group mới trên Telegram, thêm các thành viên vào (bao gồm cả tài khoản ID 1875746636 theo yêu cầu bài tập).
- Thêm cả con Bot vừa tạo ở trên vào nhóm này với quyền Admin (để nó có quyền gửi tin nhắn).
<img width="1917" height="946" alt="image" src="https://github.com/user-attachments/assets/28891cae-01d3-46fb-8424-05641dcac2b6" />

### Bước 3: Thiết kế luồng dữ liệu
kéo các node và điền các thông tin:

> Node inject để thiết lập mỗi 5s lấy dữ liệu một lần
<img width="1912" height="938" alt="image" src="https://github.com/user-attachments/assets/60472ac6-7503-4446-a39e-b04eb2cbe9b7" />

> Node http request để cào dữ liệu thực
<img width="1919" height="946" alt="image" src="https://github.com/user-attachments/assets/e5d9264b-8271-4c9c-b137-d7c9349c76de" />

> Node function chuẩn hóa và tách luồng dữ liệu
<img width="1916" height="948" alt="image" src="https://github.com/user-attachments/assets/87d0e6cb-266f-4314-a68a-02e00799ccd4" />

> Node mysql
<img width="1916" height="942" alt="image" src="https://github.com/user-attachments/assets/23487d86-decd-4184-ae8f-4aeedb6b05da" />

> Node influxdb out
<img width="1919" height="945" alt="image" src="https://github.com/user-attachments/assets/5c1e2a8c-d87d-41fd-b1df-5457e9aa1458" />

> Node switch kiểm tra ngưỡng nhiệt độ
<img width="1914" height="993" alt="image" src="https://github.com/user-attachments/assets/4c36e253-94e4-4c7b-a17b-a00692c20434" />

> Function node tạo cảnh báo low, high
<img width="1915" height="992" alt="image" src="https://github.com/user-attachments/assets/84ef2c38-0e4b-47b8-9de1-151d6473acd4" />

> Node sender để bắn cảnh báo tới telegram
<img width="1918" height="949" alt="Screenshot 2026-06-10 170621" src="https://github.com/user-attachments/assets/93b032e2-9760-45f7-aba5-a2b916fdb7d8" />

### Bước 4: Deploy và kiểm tra
Bấm nút Deploy màu đỏ trên góc phải màn hình để lưu và chạy.
<img width="1916" height="946" alt="image" src="https://github.com/user-attachments/assets/ccf371a6-7b98-44cd-a19c-383c7f1733a1" />

Truy cập vào giao diện web để xem kết quả http://192.168.183.130:8085
<img width="1917" height="990" alt="image" src="https://github.com/user-attachments/assets/80162633-2957-468f-b5fc-ecefd13f7286" />

> Chú ý: Vì nhiệt độ đang ở mức bình thường, nên để có cảnh báo đẩy về telegram, em sẽ sửa lại ngưỡng bất thường high từ 40 độ còn 10 độ.

Kết quả cảnh báo khi nhiệt độ vượt ngưỡng 10 độ:
<img width="1438" height="950" alt="image" src="https://github.com/user-attachments/assets/b4cfd48d-f583-4ff4-b0c5-a92b8325f0f1" />

### 2.7. Cấu hình Grafana kết nối InfluxDB
### Bước 1: Đăng nhập grafana
- Truy cập http://192.168.183.130:3002 để vào Grafana
- Đăng nhập và đổi mật khẩu (nếu cần)
<img width="1918" height="986" alt="image" src="https://github.com/user-attachments/assets/fb873464-dd45-4c60-b401-ca78e0cd540c" />

### Bước 2: Thêm datasource
- Tại thanh menu bên trái, chọn Connections -> Data sources -> Add data source.
<img width="1914" height="989" alt="image" src="https://github.com/user-attachments/assets/baa57674-44bc-4b98-a8e9-1f31dfe3cdf4" />

- Chọn InfluxDB.
- Cấu hình các thông số chính xác như sau:
  
| Trường   | Thông tin                 |
|-----------|---------------------------|
| URL       | http://weather_influxdb:8086 |
| Database  | weather_history           |
| User      | admin                     |
| Password  | adminpassword            |

- Kéo xuống dưới cùng ấn Save & test. Nếu hiện thông báo màu xanh "Data source is working" là thành công!
<img width="1916" height="981" alt="image" src="https://github.com/user-attachments/assets/43334511-1f4c-4f46-95e2-1c7a39cc678b" />

<img width="1916" height="981" alt="image" src="https://github.com/user-attachments/assets/d148c438-f7ab-4e7b-8b92-e3d1847ca87f" />

<img width="1910" height="946" alt="image" src="https://github.com/user-attachments/assets/7dddc3b8-608f-481e-a73f-d3477da97f42" />

### Bước 3: Tạo biểu đồ
- Nhấn vào biểu tượng + ở phía trên bên phải, chọn New Dashboard -> add Panel -> Configure Visualization
<img width="1918" height="994" alt="image" src="https://github.com/user-attachments/assets/844fb7c8-c655-4814-8706-283ead727887" />

Cấu hình khung Queries:

- FROM: bấm vào chữ select measurement -> Chọn tên bảng là weather_metrics.
- SELECT: bấm vào chữ field(value) -> Sửa/chọn thành field(temperature).
- Hãy bấm vào dấu X của hàm mean(), time($__interval) để xóa đi

> Xóa mean() và group by time giúp dữ liệu thực hiển thị lên ngay lập tức mà không bị Grafana tự động gom nhóm trung bình theo thời gian (vốn dễ làm mất nét biểu đồ khi dữ liệu mới nạp).

Tại danh sách bên phải mục Suggestions / All visualizations, bạn chọn kiểu Time series (đồ thị đường thẳng theo thời gian, mặc định ở đầu danh sách).

Bấm nút Save -> Đặt tên cho Dashboard (ví dụ: Weather Monitor).


