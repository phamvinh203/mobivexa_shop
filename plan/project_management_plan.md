# Kế hoạch Vận hành & Phân rã Công việc (Project Management Plan)

Tài liệu này đóng vai trò là **Project Manager (Quản lý dự án)**, hướng dẫn cách triển khai thực tế từ các bản thiết kế Cơ sở dữ liệu và REST API đã thống nhất. Chúng tôi áp dụng mô hình **Agile/Scrum** để chia dự án thành các **Sprint (chu kỳ phát triển từ 1-2 tuần)** giúp kiểm soát tiến độ và chất lượng tốt nhất.

---

## 1. Phân chia Lộ trình Phát triển (Sprint Roadmap)

Dự án được chia làm 5 Sprint chính để phát triển toàn diện hệ thống Backend và Database:

### 🚀 SPRINT 1: Thiết lập Môi trường & Phân hệ Xác thực (Thời gian: Tuần 1)
*   **Mục tiêu**: Có hệ thống khung chạy được, kết nối Database thành công và đăng ký/đăng nhập an toàn.
*   **Đầu việc Database & DevOps**:
    1.  Cài đặt PostgreSQL cục bộ hoặc trên cloud (Supabase/Neon).
    2.  Khởi tạo dự án Express + TypeScript + Prisma.
    3.  Tạo file `schema.prisma` và chạy lệnh `prisma migrate dev` để tạo các bảng ban đầu.
*   **Đầu việc Backend**:
    1.  Cấu hình cơ chế xử lý lỗi tập trung (Global Error Handler) và Middleware kiểm tra xác thực JWT.
    2.  Hiện thực hóa nhóm API Auth (`/auth/register`, `/auth/login`, `/auth/refresh-token`).
    3.  Hiện thực hóa API lấy thông tin người dùng (`GET /users/me`).
*   **Kết quả bàn giao**: API Auth hoạt động, đăng ký đăng nhập trả về Token chính xác.

### 📦 SPRINT 2: Phân hệ Danh mục, Sản phẩm & Biến thể (Thời gian: Tuần 2)
*   **Mục tiêu**: Hoàn thành phần quản lý sản phẩm điện thoại (Core Business).
*   **Đầu việc Database**:
    1.  Viết file `seed.ts` để nạp dữ liệu mẫu của các hãng (Apple, Samsung, Xiaomi...) và các mẫu máy flagship (iPhone 16 Pro Max, Galaxy S25...) vào database.
    2.  Tạo các index tăng tốc tìm kiếm trên bảng `products` và `product_variants`.
*   **Đầu việc Backend**:
    1.  Hiện thực hóa các API Public: Danh sách sản phẩm, chi tiết sản phẩm kèm biến thể cấu hình RAM/Storage/Màu sắc.
    2.  Xây dựng API so sánh sản phẩm (`GET /products/compare`) và API gợi ý (`GET /products/autocomplete`).
    3.  Hiện thực hóa API Admin: Thêm/Sửa/Xóa sản phẩm và biến thể (`/admin/products`, `/admin/variants`).
*   **Kết quả bàn giao**: Giao diện Frontend có thể gọi API để hiển thị danh sách và chi tiết cấu hình điện thoại.

### 🛒 SPRINT 3: Giỏ hàng, Đơn hàng & Thanh toán (Thời gian: Tuần 3)
*   **Mục tiêu**: Hoàn thành luồng mua hàng và giao dịch trực tuyến.
*   **Đầu việc Backend**:
    1.  Hiện thực hóa API Giỏ hàng (`/cart`) lưu trữ ở phía máy chủ.
    2.  Xây dựng API đặt hàng (`POST /orders`) để chốt đơn và trừ số lượng tồn kho của biến thể sản phẩm.
    3.  Tích hợp cổng thanh toán (ví dụ: Stripe API) để tạo Session thanh toán.
    4.  Xây dựng API Webhook (`/payments/webhook`) nhận thông báo từ cổng thanh toán để tự động cập nhật trạng thái đơn hàng sang `paid`.
*   **Kết quả bàn giao**: Người dùng có thể chọn mua điện thoại và thực hiện thanh toán online giả lập thành công.

### 🎁 SPRINT 4: Tương tác & Khuyến mãi (Thời gian: Tuần 4)
*   **Mục tiêu**: Tối ưu hóa chuyển đổi và giữ chân khách hàng.
*   **Đầu việc Backend**:
    1.  Hiện thực hóa API Danh sách yêu thích (`/wishlist`).
    2.  Hiện thực hóa API Mã giảm giá (`/coupons/validate` và `/admin/coupons` quản trị).
    3.  Hiện thực hóa API Đánh giá sản phẩm (`/reviews`).
*   **Kết quả bàn giao**: Các tính năng bổ trợ hoạt động tốt, kích thích người dùng mua sắm.

### 📊 SPRINT 5: Admin Dashboard, Lịch sử Kho & Bàn giao (Thời gian: Tuần 5)
*   **Mục tiêu**: Hoàn thiện công cụ quản lý cho quản trị viên và tối ưu hóa hệ thống.
*   **Đầu việc Database & Backend**:
    1.  Viết các câu lệnh truy vấn tổng hợp doanh thu, đơn hàng, khách hàng mới để phục vụ API thống kê dashboard (`/admin/stats/overview`).
    2.  Xây dựng API quản lý kho hàng và ghi nhận lịch sử biến động kho (`/admin/inventory/logs`).
    3.  Cấu hình tải ảnh sản phẩm trực tiếp lên Cloudinary/S3 qua API (`/admin/upload/image`).
*   **Đầu việc QA/QC & DevOps**:
    1.  Viết và chạy các kịch bản test tự động E2E bằng Playwright.
    2.  Docker hóa ứng dụng Backend và thiết lập luồng CI/CD Deploy lên production (AWS/Vercel/Render).
*   **Kết quả bàn giao**: Hệ thống thương mại điện tử hoàn chỉnh sẵn sàng go-live.

---

## 2. Phân công Vai trò trong Đội ngũ IT (Roles Assignment)

| Vai trò | Nhiệm vụ chính trong dự án |
| :--- | :--- |
| **Project Manager** | Theo dõi tiến độ Sprint, giải quyết các điểm nghẽn (blockers) và tổ chức các buổi họp review cuối tuần. |
| **Database Administrator** | Đảm bảo tính toàn vẹn của dữ liệu, cấu hình Backup DB định kỳ, giám sát và tối ưu các câu lệnh SQL chạy chậm (Slow Queries). |
| **Backend Developer** | Lập trình các Controller, Service theo đúng thiết kế REST API, viết các test case đơn vị (Unit Tests). |
| **Frontend Developer** | Thiết kế UI/UX theo phong cách tối giản, hiện đại của nước ngoài, kết nối API và xử lý giỏ hàng phía Client. |
| **QA / Tester** | Viết kịch bản kiểm thử, test tay luồng mua hàng và lập báo cáo lỗi (Bug Reports). |
| **DevOps Engineer** | Quản lý môi trường Server (Staging/Production), thiết lập CI/CD và giám sát log lỗi trực tiếp (Sentry/Grafana). |

---

## 3. Các bước Hành động Tiếp theo (Next Steps for PM)

Để bắt đầu dự án ngay lập tức, bạn (ở vai trò PM) nên thực hiện 3 hành động sau:
1.  **Hành động 1**: Tạo một bảng Kanban (trên Trello hoặc Jira) và tạo các thẻ công việc (Cards) tương ứng với các đầu việc trong **Sprint 1**.
2.  **Hành động 2**: Thiết lập kho lưu trữ mã nguồn chung (ví dụ: tạo repo Git trên GitHub) và mời các lập trình viên tham gia.
3.  **Hành động 3**: Tạo file cấu hình cơ bản cho dự án Node.js (khởi tạo `package.json`, cài đặt TypeScript và Prisma) để chuyển giao cho bộ phận Backend bắt tay vào việc.
