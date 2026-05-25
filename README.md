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

<img width="1916" height="992" alt="image" src="https://github.com/user-attachments/assets/34dbb55a-c256-4563-aca9-930c89557c53" />

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
- Wordpress URL: điền giá trị https://wordpress.nguyentrunghiieu.id.vn/ (subdomain1)
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


