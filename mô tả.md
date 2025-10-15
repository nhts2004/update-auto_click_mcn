# Hướng dẫn sử dụng RioMCN Auto Renew

## 1. Giới thiệu
RioMCN Auto Renew là tiện ích mở rộng giúp tự động gia hạn giờ live trên hệ thống [mcn.riomedia.vn](https://mcn.riomedia.vn) bằng cách giám sát các tab đang mở trang giỏ live và nhấn nút gia hạn thay cho bạn khi đến thời điểm phù hợp.【F:manifest.json†L3-L45】【F:content.js†L23-L188】

Tiện ích hoạt động hoàn toàn trên trình duyệt Chrome/Edge ở chế độ nền (background service worker) và hỗ trợ gửi thông báo trên trình duyệt cũng như email khi gia hạn thành công hoặc thất bại.【F:manifest.json†L6-L15】【F:background.js†L559-L612】【F:background.js†L682-L747】

## 2. Điều kiện trước khi cài đặt
- Máy tính sử dụng trình duyệt Chromium (Chrome, Microsoft Edge, Brave...).
- Có quyền truy cập trang https://mcn.riomedia.vn/giolive/ và sẵn sàng đăng nhập tài khoản MCN.
- Đã tải mã nguồn dự án về máy (ví dụ: clone từ Git hoặc tải ZIP và giải nén).

## 3. Cài đặt tiện ích ở chế độ Developer
1. Mở trình duyệt Chrome/Edge → nhập `chrome://extensions/` (hoặc `edge://extensions/`).
2. Bật **Developer mode** (Chế độ dành cho nhà phát triển) ở góc phải.
3. Chọn **Load unpacked** (Tải tiện ích đã giải nén) và trỏ tới thư mục `auto-click-mcn/auto-click-mcn` chứa file `manifest.json`.
4. Sau khi thêm thành công, biểu tượng RioMCN Auto Renew sẽ xuất hiện trên thanh tiện ích.【F:manifest.json†L44-L53】

## 4. Chuẩn bị tab làm việc
- Mở trang giỏ live cần giám sát ở địa chỉ `https://mcn.riomedia.vn/giolive/...` và giữ tab này mở trong suốt quá trình tự động gia hạn.【F:manifest.json†L16-L43】
- Mỗi lần bạn truy cập/refresh trang, content script sẽ đọc lại thông tin thời gian và link gia hạn để phục vụ cho lần xử lý tiếp theo.【F:content.js†L23-L188】

## 5. Cấu hình lần đầu
Nhấp vào biểu tượng tiện ích để mở popup và thực hiện các bước sau:

### 5.1 Nhập email bắt buộc
- Trường **Email nhận thông báo** là bắt buộc; tiện ích sẽ không cho phép bật tự động nếu email không hợp lệ.【F:popup.js†L105-L145】
- Email dùng để gửi báo cáo khi gia hạn thành công/thất bại và các lỗi phát sinh.【F:background.js†L682-L747】【F:background.js†L749-L805】

### 5.2 Chọn chế độ lập lịch
- **Trước (Trước giờ hết hạn)**: tiện ích sẽ bấm gia hạn trước khi hết hạn `wait_minutes` phút. Đây là chế độ mặc định.【F:popup.js†L183-L188】【F:background.js†L651-L656】【F:background.js†L1048-L1059】
- **Sau (Sau khi hết hạn)**: tiện ích chờ đến khi giờ live hết hạn rồi thử gia hạn trong khoảng `wait_minutes` phút sau đó.【F:popup.js†L183-L188】【F:background.js†L651-L656】【F:background.js†L1055-L1059】

### 5.3 Thiết lập thời gian chờ
- **Thời gian chờ (phút)**: khoảng lệch so với thời điểm hết hạn. Nhập `0` nếu muốn gia hạn ngay sát thời điểm hệ thống báo hết hạn.【F:popup.js†L68-L73】【F:background.js†L651-L675】

### 5.4 Cấu hình kiểm tra lại khi lỗi
- **Thời gian chờ kiểm tra lại sau lỗi (phút)** và **Số lần kiểm tra lại** cho phép hệ thống tự động thử lại nếu thao tác gia hạn gặp lỗi. Số lần = 0 nghĩa là thử lại không giới hạn, mỗi lần cách nhau `retry_interval_minutes` phút.【F:popup.js†L74-L84】【F:background.js†L346-L366】【F:background.js†L1020-L1030】

### 5.5 Tùy chọn email thông báo
- Bật/tắt gửi email bằng checkbox **Muốn nhận email thông báo**. Nếu tắt, tiện ích chỉ hiển thị thông báo trên trình duyệt.【F:popup.js†L98-L103】【F:background.js†L559-L612】【F:background.js†L749-L805】

### 5.6 Bật/tắt tự động
- Nhấn **Kích hoạt** để bắt đầu giám sát. Trạng thái sẽ chuyển sang “Đang hoạt động” và nút **Kiểm tra ngay** xuất hiện để bạn chủ động chạy lại một lần kiểm tra.【F:popup.js†L125-L157】【F:popup.js†L272-L289】
- Nhấn **Tắt** khi muốn dừng tự động và hủy các lịch đã đặt.【F:popup.js†L125-L157】【F:background.js†L371-L377】

### 5.7 Đồng bộ và lịch chạy nền
Khi bật tự động, tiện ích sẽ:
1. Tìm tab phù hợp đang mở trang giỏ live và lấy thời điểm hết hạn từ DOM.【F:background.js†L379-L426】
2. Đặt lịch (Chrome alarm) để quay lại tab đó và bấm nút gia hạn vào thời điểm tương ứng với chế độ bạn chọn.【F:background.js†L633-L680】
3. Gửi thông báo kết quả và sắp lịch lại cho lần tiếp theo sau mỗi lần xử lý.【F:background.js†L559-L616】

## 6. Theo dõi và xử lý sự cố

### 6.1 Tab “Nhật ký”
- Chuyển sang tab **Nhật ký** trong popup để xem tối đa 200 bản ghi gần nhất gồm thời gian, mức độ (INFO/WARN/ERROR) và chi tiết kèm theo.【F:popup.js†L159-L270】
- Dùng nút **Làm mới** để tải lại log, **Làm sạch** để xóa log đang hiển thị.【F:popup.js†L220-L238】

### 6.2 Nút “Kiểm tra ngay”
- Khi trạng thái đang hoạt động, bạn có thể nhấn **Kiểm tra ngay** để yêu cầu tiện ích lập tức kiểm tra thời gian hết hạn và gia hạn nếu đủ điều kiện.【F:popup.js†L148-L157】【F:background.js†L379-L408】

### 6.3 Nút “reset”
- Nút **reset** đặt lại toàn bộ cấu hình tạm thời: đưa thời gian chờ về 0, chọn lại chế độ “Trước”, bỏ chọn email, xóa bộ đếm thành công và log, đồng thời dừng tự động hóa.【F:popup.js†L239-L252】

### 6.4 Thông báo lỗi và email
- Mỗi khi thao tác gia hạn thất bại (không tìm thấy tab, trang chưa sẵn sàng, không bấm được nút...), tiện ích sẽ hiển thị thông báo cảnh báo trong trình duyệt và (nếu bật) gửi email mô tả chi tiết lỗi, số lần thử lại...【F:background.js†L477-L616】【F:background.js†L749-L805】
- Bạn có thể dựa vào nội dung log và email để xử lý (ví dụ: đảm bảo tab vẫn mở, đăng nhập chưa hết hạn, nút gia hạn vẫn hiển thị).

## 7. Hệ thống gửi email
Tiện ích sử dụng một endpoint Google Apps Script khai báo trong `config.json` để gửi email trạng thái. Nếu cần thay đổi địa chỉ nhận, chỉnh sửa trực tiếp trường `url` trong file cấu hình trước khi tải tiện ích.【F:config.json†L1-L3】【F:background.js†L736-L744】

## 8. Mẹo vận hành
- Giữ ít nhất một tab giỏ live mở liên tục; nếu tab bị đóng, tiện ích sẽ ghi nhận lỗi “Không tìm thấy tab đủ điều kiện”.【F:background.js†L389-L435】【F:background.js†L633-L648】
- Tránh reload trang ngay trong thời điểm tiện ích đang thao tác để hạn chế mất dữ liệu đọc từ DOM.【F:content.js†L23-L188】
- Theo dõi bộ đếm **Số lần tự động thành công** để đánh giá hiệu quả vận hành và sử dụng nút reset khi muốn bắt đầu lại từ đầu.【F:popup.js†L200-L299】

Chúc bạn vận hành hệ thống gia hạn giờ live hiệu quả với RioMCN Auto Renew