# Kế hoạch Thiết kế RESTful API (Node.js + Express + TypeScript + Prisma) - Phân Tách Chi Tiết

Tài liệu này trình bày các API Endpoint được phân tách rõ ràng thành từng bảng độc lập theo chức năng nghiệp vụ để đội ngũ Frontend và Backend dễ dàng phối hợp phát triển. Đặc biệt, toàn bộ các API yêu cầu quyền **Quản trị viên (Admin)** đã được nhóm riêng biệt ở phần cuối.

Tất cả các API sử dụng tiền tố mặc định: `/api/v1`

---

## I. NHÓM API DÀNH CHO NGƯỜI DÙNG & CÔNG KHAI (USER & PUBLIC APIs)

### 1. Phân hệ Xác thực (`/auth`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **POST** | `/auth/register` | Public | Đăng ký tài khoản người dùng mới |
| **POST** | `/auth/login` | Public | Đăng nhập hệ thống, trả về JWT Access Token & Refresh Token |
| **POST** | `/auth/refresh-token`| Public | Làm mới Access Token bằng Refresh Token khi hết hạn |
| **POST** | `/auth/logout` | User / Admin | Đăng xuất, vô hiệu hóa token hiện tại |
| **POST** | `/auth/forgot-password`| Public | Yêu cầu gửi email khôi phục mật khẩu |
| **POST** | `/auth/reset-password` | Public | Đặt lại mật khẩu mới thông qua token xác nhận từ email |

---

### 2. Phân hệ Người dùng (`/users`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/users/me` | User | Lấy thông tin tài khoản cá nhân hiện tại |
| **PUT** | `/users/me` | User | Cập nhật thông tin cá nhân (họ tên, số điện thoại) |
| **PATCH**| `/users/me/password` | User | Thay đổi mật khẩu tài khoản trực tiếp |
| **GET** | `/users/addresses` | User | Lấy danh sách địa chỉ giao hàng của người dùng |
| **POST** | `/users/addresses` | User | Thêm địa chỉ giao hàng mới |
| **PUT** | `/users/addresses/:id`| User | Cập nhật địa chỉ giao hàng theo ID |
| **DELETE**| `/users/addresses/:id`| User | Xóa địa chỉ giao hàng theo ID |

---

### 3. Phân hệ Hãng sản xuất (`/brands`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/brands` | Public | Lấy danh sách tất cả các hãng điện thoại đang kinh doanh |
| **GET** | `/brands/:slug` | Public | Lấy thông tin chi tiết của một hãng kèm các dòng sản phẩm của hãng đó |

---

### 4. Phân hệ Danh mục (`/categories`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/categories` | Public | Lấy danh sách tất cả các danh mục sản phẩm (Điện thoại, phụ kiện...) |

---

### 5. Phân hệ Sản phẩm (`/products`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/products` | Public | Lấy danh sách sản phẩm (Hỗ trợ tìm kiếm, lọc theo hãng, khoảng giá và phân trang) |
| **GET** | `/products/autocomplete`| Public | Tự động gợi ý tên sản phẩm khi người dùng gõ tìm kiếm |
| **GET** | `/products/compare` | Public | So sánh thông số kỹ thuật và giá cả của các sản phẩm điện thoại dựa trên danh sách ID |
| **GET** | `/products/:slug` | Public | Lấy chi tiết một sản phẩm và tất cả các biến thể cấu hình của sản phẩm đó |
| **GET** | `/products/:id/related`| Public | Gợi ý các sản phẩm liên quan (cùng hãng, cùng tầm giá) |

---

### 6. Phân hệ Biến thể sản phẩm (`/variants`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/variants/:id` | Public | Lấy thông tin chi tiết một biến thể cụ thể (RAM, Storage, Giá bán, Màu sắc) |

---

### 7. Phân hệ Giỏ hàng (`/cart`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/cart` | User | Lấy chi tiết giỏ hàng hiện tại của người dùng |
| **POST** | `/cart/items` | User | Thêm sản phẩm vào giỏ hàng hoặc cập nhật số lượng |
| **DELETE**| `/cart/items/:variantId`| User | Xóa một biến thể sản phẩm ra khỏi giỏ hàng |
| **DELETE**| `/cart/clear` | User | Xóa toàn bộ sản phẩm trong giỏ hàng |

---

### 8. Phân hệ Danh sách yêu thích (`/wishlist`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/wishlist` | User | Lấy danh sách tất cả sản phẩm mà người dùng đã yêu thích |
| **POST** | `/wishlist` | User | Thêm một sản phẩm vào danh sách yêu thích |
| **DELETE**| `/wishlist/:productId` | User | Xóa sản phẩm khỏi danh sách yêu thích |

---

### 9. Phân hệ Đơn hàng (`/orders`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **POST** | `/orders` | User | Tạo đơn hàng mới từ các mặt hàng trong giỏ hàng |
| **GET** | `/orders/my-orders` | User | Lấy lịch sử tất cả các đơn hàng đã đặt của người dùng hiện tại |
| **GET** | `/orders/:id` | User | Lấy chi tiết một đơn hàng cụ thể (thông tin vận chuyển, danh sách mặt hàng, thanh toán) |

---

### 10. Phân hệ Mã giảm giá (`/coupons`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/coupons/active` | Public | Lấy danh sách mã giảm giá công khai đang khả dụng |
| **POST** | `/coupons/validate` | User | Áp dụng thử mã giảm giá để tính toán số tiền được giảm |

---

### 11. Phân hệ Thanh toán (`/payments`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **POST** | `/payments/create-session`| User | Tạo phiên thanh toán (Stripe Checkout Session hoặc Cổng thanh toán khác) |
| **POST** | `/payments/webhook` | Public | Tiếp nhận phản hồi tự động (Callbacks) từ Stripe/Momo/VNPAY để cập nhật trạng thái đơn hàng |

---

### 12. Phân hệ Đánh giá (`/reviews`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/products/:id/reviews` | Public | Lấy toàn bộ đánh giá (Sao & bình luận) của một sản phẩm |
| **POST** | `/products/:id/reviews` | User | Gửi đánh giá cho sản phẩm đã mua |

---

## II. NHÓM API DÀNH RIÊNG CHO QUẢN TRỊ VIÊN (ADMIN APIs)

> [!IMPORTANT]
> Tất cả các API dưới đây đều yêu cầu Header xác thực **JWT Token** của tài khoản có quyền `admin` hoặc `staff`.

### 1. Quản lý Hãng & Danh mục (`/admin/brands` & `/admin/categories`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **POST** | `/admin/brands` | Admin | Tạo mới một thương hiệu điện thoại |
| **PUT** | `/admin/brands/:id` | Admin | Sửa thông tin thương hiệu |
| **DELETE**| `/admin/brands/:id` | Admin | Xóa thương hiệu khỏi hệ thống |
| **POST** | `/admin/categories` | Admin | Tạo mới danh mục thiết bị |
| **PUT** | `/admin/categories/:id` | Admin | Sửa tên hoặc slug danh mục |
| **DELETE**| `/admin/categories/:id` | Admin | Xóa danh mục |

### 2. Quản lý Sản phẩm & Biến thể (`/admin/products` & `/admin/variants`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **POST** | `/admin/products` | Admin | Đăng bán sản phẩm (dòng điện thoại) mới |
| **PUT** | `/admin/products/:id` | Admin | Cập nhật thông tin chi tiết sản phẩm |
| **DELETE**| `/admin/products/:id` | Admin | Xóa sản phẩm |
| **POST** | `/admin/products/:id/variants`| Admin | Thêm cấu hình biến thể (RAM/Storage/Color) cho dòng máy |
| **PUT** | `/admin/variants/:id` | Admin | Cập nhật giá bán, giá so sánh, số lượng kho của biến thế |
| **DELETE**| `/admin/variants/:id` | Admin | Xóa cấu hình biến thể |

### 3. Quản lý Đơn hàng & Mã giảm giá (`/admin/orders` & `/admin/coupons`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/admin/orders` | Admin / Staff | Lấy danh sách toàn bộ đơn hàng trên hệ thống (hỗ trợ lọc trạng thái) |
| **PUT** | `/admin/orders/:id/status`| Admin / Staff | Cập nhật trạng thái giao hàng (`confirmed`, `shipping`, `completed`, `cancelled`) |
| **POST** | `/admin/coupons` | Admin | Tạo mới mã giảm giá |
| **PUT** | `/admin/coupons/:id` | Admin | Thay đổi cấu hình mã giảm giá |
| **DELETE**| `/admin/coupons/:id` | Admin | Xóa mã giảm giá |

### 4. Thống kê & Quản lý Kho hàng (`/admin/stats` & `/admin/inventory`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **GET** | `/admin/stats/overview`| Admin | Thống kê doanh thu, đơn hàng, người dùng mới |
| **GET** | `/admin/stats/revenue` | Admin | Lấy biểu đồ doanh thu theo chu kỳ ngày/tháng/năm |
| **GET** | `/admin/stats/top-selling`| Admin | Thống kê danh sách sản phẩm bán chạy nhất |
| **GET** | `/admin/inventory/logs` | Admin | Xem lịch sử nhập kho, xuất kho |
| **POST** | `/admin/inventory/adjust` | Admin | Điều chỉnh trực tiếp số tồn kho của biến thể |

### 5. Quản lý Đánh giá & Upload hình ảnh (`/admin/reviews` & `/upload`)

| Method | Endpoint | Quyền truy cập | Mô tả |
| :--- | :--- | :--- | :--- |
| **DELETE**| `/admin/reviews/:id` | Admin | Xóa đánh giá spam của khách hàng khỏi trang web |
| **POST** | `/admin/upload/image` | Admin / Staff | Tải tệp hình ảnh sản phẩm/banner lên Cloudinary/S3 và nhận về link URL |
