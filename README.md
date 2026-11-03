# BÀI TẬP TỔNG HỢP: NGÀY ĐẦU THỰC TẬP CỦA DEV TẠI CÔNG TY CÔNG NGHỆ

---

## PHẦN A – THIẾT LẬP PROFILE VÀ BOOKMARK CHUẨN DEV

### 1. Rủi ro khi dùng chung 1 Profile trình duyệt cho cả Cá nhân và Công ty
* **Xung đột tài khoản (Session Conflict):** Đăng nhập song song hai tài khoản (ví dụ: Gmail cá nhân và Gmail công ty) trên cùng một profile dễ dẫn đến tình trạng chiếm quyền session, lỗi đăng xuất chéo hoặc nhảy sai tài khoản khi click vào các liên kết nội bộ.
* **Bảo mật dữ liệu (Data Leakage):** Đồng bộ hóa mật khẩu, lịch sử duyệt web cá nhân lên tài khoản công ty (hoặc ngược lại) làm tăng nguy cơ rò rỉ thông tin nhạy cảm của dự án ra ngoài hoặc lộ thông tin cá nhân cho quản trị viên IT của doanh nghiệp.
* **Phân tâm và giảm hiệu suất (Distraction):** Các bookmark giải trí, mạng xã hội cá nhân xuất hiện xen lẫn với tài liệu công việc làm giảm độ tập trung trong giờ làm việc.

### 2. Mô hình 3 Profile chuẩn Developer

| Tên Profile | Mục đích sử dụng | Các tài khoản / Nền tảng đăng nhập | Mẹo quản lý / Lưu ý |
| :--- | :--- | :--- | :--- |
| **1. Work** (Công việc) | Phục vụ toàn bộ tác vụ liên quan đến dự án và công ty. | Email công ty, GitHub công ty, Slack/Teams, Jira, AWS/Azure doanh nghiệp. | Bật đồng bộ bằng email công ty. Tuyệt đối không lưu mật khẩu cá nhân tại đây. |
| **2. Personal** (Cá nhân) | Sử dụng cho việc học tập riêng, giải trí và đời sống. | Gmail cá nhân, Facebook, YouTube, GitHub cá nhân, Banking. | Không đăng nhập trên thiết bị hoặc mạng VPN kiểm soát quá nghiêm ngặt của công ty nếu không cần thiết. |
| **3. Guest / Incognito** (Thử nghiệm) | Kiểm tra hiển thị, test tính năng ẩn danh, không lưu cache. | Không đăng nhập cố định tài khoản nào (dùng tài khoản test). | Dùng để test luồng Đăng nhập/Đăng xuất (Login/Logout) của dự án xem có bị dính session cũ không. |

### 3. Thiết kế cấu trúc Bookmark mẫu
* **MANAGEMENT** (Quản lý công việc)
  * Jira Dashboard *(Quản lý task)*
  * Confluence Workspace *(Tài liệu dự án)*
* **REPOSITORIES** (Mã nguồn & Deploy)
  * GitHub Company Organization *(Quản lý code)*
  * Jenkins / Vercel Dashboard *(Luồng CI/CD)*
* **ENVIRONMENTS** (Môi trường chạy thử)
  * Staging Environment URL *(Link test nội bộ)*
  * Production Admin Panel *(Hệ thống quản trị thực tế)*

### 4. Giải pháp cho quy định "Không quá 5 phút để tìm tài liệu"
Khi áp dụng cấu trúc bookmark phân cấp rõ ràng (như mục 3) kết hợp với tính năng ghim tab (Pin tab) cho các công cụ giao tiếp (Slack/Teams), lập trình viên sẽ không cần gõ tìm kiếm thủ công trên thanh địa chỉ hay lội ngược dòng tin nhắn cũ. Dev chỉ cần **1-click** vào đúng thư mục chuyên biệt là có thể truy cập ngay tài liệu trong vòng dưới 3 giây, đáp ứng hoàn hảo quy định về thời gian.

---

## PHẦN B – CHỌN ĐÚNG EXTENSION HỖ TRỢ

### 1. Tiện ích hỗ trợ đọc dữ liệu API JSON thô (Raw JSON)
* **Tên tiện ích đề xuất:** **JSON Viewer** hoặc **JSON Formatter**.
* **Tác dụng:** Tự động định dạng (format) chuỗi JSON thô thành cấu trúc cây (tree view) trực quan, có màu sắc phân biệt giữa Key/Value, hỗ trợ thu gọn/mở rộng các object giúp lập trình viên dễ dàng đọc và phân tích cấu trúc dữ liệu trả về từ API.

### 2. So sánh ngắn gọn hai loại tiện ích quản lý
* **Site Access Extensions (Quản lý quyền truy cập web):** Kiểm soát và giới hạn quyền đọc/ghi dữ liệu của các extension khác trên các trang web cụ thể (Ví dụ: Chỉ cho phép extension chạy trên `localhost` mà không được phép chạy trên các trang ngân hàng `ebanking`).
* **CORS / Request Changers (Bỏ qua chặn CORS):** Hỗ trợ can thiệp, chỉnh sửa hoặc thêm các header (như `Access-Control-Allow-Origin`) vào HTTP Request/Response, giúp dev bypass cơ chế chặn CORS khi gọi API trực tiếp từ môi trường Localhost lên Server Dev/Staging để test nhanh.

---

## PHẦN C – PHÂN TÍCH LUỒNG HTTP REQUEST

### 1. 4 Giai đoạn xử lý chính của một HTTP Request
1. **DNS Lookup (Phân giải tên miền):** Trình duyệt tìm kiếm địa chỉ IP thực tế của domain (Ví dụ: `google.com` -> `172.217.16.142`).
2. **TCP Handshake (Kết nối):** Thiết lập kết nối tin cậy giữa client (trình duyệt) và server thông qua giao thức TCP (quy trình bẻ tay ba bước).
3. **Server Processing (Xử lý tại Server):** Server tiếp nhận request, đọc thông tin headers/body, xử lý logic nghiệp vụ (gọi database, tính toán) và tạo dữ liệu phản hồi.
4. **Content Download (Tải dữ liệu về Client):** Server gửi gói dữ liệu (HTML, JSON, Hình ảnh...) về, trình duyệt tiến hành tải xuống và dựng giao diện (Render) hiển thị cho người dùng.

### 2. So sánh trạng thái HTTP Status Code phổ biến

| Mã lỗi | Nhóm lỗi | Ý nghĩa cụ thể | Cách xử lý cơ bản cho Dev |
| :--- | :--- | :--- | :--- |
| **401 Unauthorized** | Client Error | Chưa xác thực danh tính (Thiếu hoặc sai Token/Session). | Kiểm tra lại luồng đính kèm Access Token vào Header `Authorization`. |
| **403 Forbidden** | Client Error | Đã xác thực nhưng tài khoản không có quyền truy cập tài nguyên này. | Kiểm tra lại cấu hình phân quyền (Role/Permission) của user trong hệ thống. |
| **404 Not Found** | Client Error | Sai đường dẫn URL hệ thống hoặc tài nguyên yêu cầu đã bị xóa. | Kiểm tra kỹ lại chính tả route của API hoặc kiểm tra sự tồn tại của ID dưới DB. |
| **500 Internal Server Error** | Server Error | Lỗi logic hệ thống, crash code ở phía backend. | Truy cập hệ thống quản lý log của server (Backend logs) để tìm dòng code gây crash. |

### 3. Tìm hiểu các tab công cụ trong Inspect Element
* **Elements:** Xem và chỉnh sửa trực tiếp cấu trúc HTML/CSS của trang web hiện tại (chỉ có tác dụng tạm thời tại client, F5 sẽ mất).
* **Console:** Xem các log lỗi Javascript, chạy thử các đoạn code JS ngắn, debug lỗi runtime trực tiếp trên trình duyệt.
* **Network:** Giám sát toàn bộ các request gửi đi và phản hồi nhận về (tải ảnh, file js, gọi API), kiểm tra thời gian tải (Timing) và mã lỗi HTTP.
* **Application:** Quản lý không gian lưu trữ dữ liệu của trình duyệt bao gồm LocalStorage, SessionStorage, Cookies, Cache Storage.

---

## PHẦN D – CHẨN ĐOÁN CACHE VÀ COOKIES

### 1. Sửa file CSS nhưng nhấn F5 không đổi giao diện
* **Chẩn đoán:** Do cơ chế **Browser Caching** (Bộ nhớ đệm trình duyệt). Trình duyệt đã lưu lại file CSS cũ từ trước và tiếp tục lấy file đó ra sử dụng thay vì tải file mới từ server về để tiết kiệm băng thông.
* **Cách khắc phục:** 
  * Nhấn tổ hợp phím **Ctrl + F5** (hoặc `Cmd + Shift + R` trên Mac) để ép trình duyệt xóa cache trang đó và tải lại mới hoàn toàn (Hard Reload).
  * Mở Inspect Element -> Tab Network -> Tích chọn ô **"Disable cache"** (tính năng này chỉ có tác dụng khi cửa sổ Inspect đang mở).

### 2. Phân biệt Session Cookie và Persistent Cookie trong tab Application
* **Session Cookie:** Không có ngày hết hạn cụ thể (Cột Expires/Max-Age hiển thị là `Session`). Cookie này sẽ **tự động bị xóa** ngay khi người dùng đóng hoàn toàn trình duyệt.
* **Persistent Cookie (Cookie vĩnh cửu):** Có ngày giờ hết hạn rõ ràng (Ví dụ: `Expires: 2026-12-31`). Nó sẽ **tiếp tục tồn tại** trong bộ nhớ máy tính ngay cả khi bạn tắt trình duyệt hoặc khởi động lại máy, cho đến khi đạt mốc thời gian hết hạn hoặc bị xóa thủ công.

### 3. Sự khác biệt giữa Đăng xuất (Logout) hệ thống và Xóa lịch sử duyệt web
* **Đăng xuất (Logout):** Là hành động gửi request lên Server để **hủy bỏ Session ID** ở phía backend, đồng thời xóa token/cookie lưu trữ danh tính tại client để kết thúc phiên làm việc một cách an toàn.
* **Xóa lịch sử (Clear History):** Chỉ đơn thuần xóa danh sách các liên kết URL mà bạn đã từng truy cập trong quá khứ được lưu ở trình duyệt, **hoàn toàn không làm mất phiên đăng nhập** nếu các session/cookie đăng nhập đó chưa hết hạn hoặc không được chọn tích xóa cùng lúc.

---

## PHẦN E – THÀNH THẠO INSPECT ELEMENT VÀ NETWORK TAB

### 1. Chẩn đoán nút "Đăng nhập" bị lỗi không phản hồi
* **Bước 1:** Bật F12, chuyển sang tab **Console**. Nhấp vào nút Đăng nhập xem có xuất hiện dòng lỗi đỏ nào của Javascript không (lỗi cú pháp, biến undefined, lỗi runtime).
* **Bước 2:** Chuyển sang tab **Network**, nhấp nút Đăng nhập xem có sinh ra Request API nào không. Nếu có request, kiểm tra xem HTTP Status Code trả về là gì (Ví dụ: 400, 401, 500) và kiểm tra tab **Response** để đọc câu thông báo lỗi chi tiết từ Backend trả về.

### 2. Xử lý khi một Request API chạy rất chậm trên tab Network
* **Phân tích:** Nhấp chuột vào Request bị chậm đó trong tab Network, chọn tab **Timing**.
* **Đọc thông số để đưa ra hướng giải quyết:**
  * Nếu thời gian lớn nằm ở **Waiting for server response (TTFB):** Lỗi do phía Backend xử lý chậm (truy vấn DB chưa tối ưu, xử lý logic quá nặng). Giải pháp: Cần tối ưu lại code backend hoặc câu lệnh SQL.
  * Nếu thời gian lớn nằm ở **Content Download:** Do dung lượng file/dữ liệu trả về quá lớn hoặc do mạng nghẽn. Giải pháp: Cần tối ưu nén dữ liệu (Gzip), phân trang (Pagination) dữ liệu API trả về thay vì lấy toàn bộ một lúc.

### 3. Cách xuất dữ liệu API (JSON) thành file trên máy tính
Vào tab **Network** -> Tìm và chọn đúng Request API vừa gọi dữ liệu -> Nhấp chuột phải (Right-click) vào tên request đó -> Chọn **Save response as...** -> Chọn thư mục lưu trên máy tính dưới định dạng file đuôi `.json`.
