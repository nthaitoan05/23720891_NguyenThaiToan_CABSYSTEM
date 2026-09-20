# MÔ TẢ CHI TIẾT API SPECIFICATION
## Dự án: CAB System — Nền tảng đặt xe (Công ty ABC)

Tài liệu này mô tả chi tiết toàn bộ 11 API được chia theo domain nghiệp vụ trong thư mục
`api_specification/`. Mỗi API tương ứng với một file `.yaml` (OpenAPI 3.0.3) độc lập,
có thể triển khai như một microservice riêng biệt.

## Tổng quan

| # | File | API | Số endpoint | FR liên quan |
|---|---|---|---|---|
| 1 | `1_auth_api.yaml` | Auth API | 3 | FR01 |
| 2 | `2_customer_profile_api.yaml` | Customer Profile API | 2 | FR02 |
| 3 | `3_driver_profile_api.yaml` | Driver Profile API | 8 | FR02 |
| 4 | `4_trip_api.yaml` | Trip API | 7 | FR03, FR04, FR05, FR06, FR07, FR08 |
| 5 | `5_location_api.yaml` | Location API | 1 | FR07 |
| 6 | `6_notification_api.yaml` | Notification API | 4 | FR12 |
| 7 | `7_payment_api.yaml` | Payment API | 5 | FR09, FR10, FR11 |
| 8 | `8_rating_history_api.yaml` | Rating & History API | 7 | FR13, FR14 |
| 9 | `9_admin_users_api.yaml` | Admin Users API | 13 | FR15 |
| 10 | `10_admin_staff_api.yaml` | Admin Staff & Roles API | 5 | FR18 |
| 11 | `11_admin_operations_api.yaml` | Admin Operations API | 5 | FR16, FR17, FR19 |

---

## 1. Auth API
**File:** `1_auth_api.yaml`

Đăng ký tài khoản và xác thực cho Khách hàng và Tài xế (FR01, NFR05).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| POST | `/customers/register` *(public)* | Đăng ký tài khoản khách hàng | FR01 - Đăng ký & đăng nhập. Khách hàng tự đăng ký tài khoản. | `CustomerRegisterRequest` | 201, 400, 409 |
| POST | `/drivers/register` *(public)* | Đăng ký tài khoản tài xế | FR01 - Đăng ký & đăng nhập. Tài xế tự đăng ký; có thể được nhân viên vận hành tạo thay (xem Operator - FR15). | `DriverRegisterRequest` | 201, 400 |
| POST | `/auth/login` *(public)* | Đăng nhập (khách hàng hoặc tài xế) | FR01 - Đăng ký & đăng nhập. Xác thực người dùng trước khi sử dụng các chức năng yêu cầu tài khoản (NFR05). | `LoginRequest` | 200, 401 |

---

## 2. Customer Profile API
**File:** `2_customer_profile_api.yaml`

Quản lý hồ sơ cá nhân của khách hàng (FR02).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| GET | `/customers/{customerId}` | Xem thông tin cá nhân khách hàng | FR02 - Cập nhật hồ sơ. | — | 200, 404 |
| PUT | `/customers/{customerId}` | Cập nhật thông tin cá nhân khách hàng | FR02 - Cập nhật hồ sơ. | `CustomerUpdateRequest` | 200, 400, 404 |

---

## 3. Driver Profile API
**File:** `3_driver_profile_api.yaml`

Quản lý hồ sơ, phương tiện và trạng thái sẵn sàng của tài xế (FR02).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| GET | `/drivers/{driverId}` | Xem hồ sơ tài xế | FR02 - Cập nhật hồ sơ. | — | 200, 404 |
| PUT | `/drivers/{driverId}` | Cập nhật hồ sơ tài xế | FR02 - Cập nhật hồ sơ. | `DriverUpdateRequest` | 200, 400 |
| GET | `/drivers/{driverId}/vehicles` | Xem danh sách phương tiện của tài xế | FR02 - Cập nhật hồ sơ. | — | 200 |
| POST | `/drivers/{driverId}/vehicles` | Thêm phương tiện mới | FR02 - Cập nhật hồ sơ. | `VehicleRequest` | 201, 400 |
| GET | `/drivers/{driverId}/vehicles/{vehicleId}` | Xem chi tiết phương tiện | FR02 - Cập nhật hồ sơ. | — | 200, 404 |
| PUT | `/drivers/{driverId}/vehicles/{vehicleId}` | Cập nhật thông tin phương tiện | FR02 - Cập nhật hồ sơ. | `VehicleRequest` | 200, 400, 404 |
| DELETE | `/drivers/{driverId}/vehicles/{vehicleId}` | Xóa phương tiện | FR02 - Cập nhật hồ sơ. | — | 204, 404, 409 |
| PUT | `/drivers/{driverId}/availability` | Cập nhật trạng thái sẵn sàng nhận chuyến | FR02 - Cập nhật hồ sơ (Business Rule RL01: chỉ tài xế 'available' mới được phân công chuyến). | — | 200 |

---

## 4. Trip API
**File:** `4_trip_api.yaml`

Toàn bộ vòng đời một chuyến đi: tạo yêu cầu đặt xe, tìm/ưu tiên tài xế, tài xế phản hồi, tìm tài xế thay thế, cập nhật trạng thái, theo dõi và hủy chuyến (FR03, FR04, FR05, FR06, FR07-trạng thái, FR08).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| POST | `/trips` | Tạo yêu cầu đặt xe | FR03 - Tạo yêu cầu đặt xe. Khách hàng nhập điểm đón, điểm đến, chọn loại xe và gửi yêu cầu; hệ thống tiếp nhận yêu cầu và kích hoạt quy trình tìm tài xế (nội bộ, xem Internal Services - FR04). | `TripRequest` | 201, 400 |
| GET | `/trips/{tripId}` | Theo dõi trạng thái chuyến đi | FR08 - Theo dõi chuyến. Trả về trạng thái hiện tại của chuyến, thông tin tài xế đã nhận chuyến (nếu có) và thời gian dự kiến đến (ETA). | — | 200, 404 |
| DELETE | `/trips/{tripId}` | Hủy yêu cầu đặt xe | FR03 - Tạo yêu cầu đặt xe. Cho phép khách hàng hủy chuyến khi còn ở trạng thái sớm (requested, finding_driver, driver_assigned). Chính sách hủy chi tiết (phí hủy, mốc thời gian cho phép) là điểm cần xác nhận thêm với khách hàng (Open Question OQ04). | — | 204, 404, 409 |
| POST | `/internal/matching/find-driver` | Xác định & ưu tiên tài xế phù hợp cho chuyến đi | FR04 - Xác định & ưu tiên tài xế phù hợp. Được Trip Service gọi ngay sau khi chuyến đi được tạo (POST /trips). Trả về danh sách tài xế đã ưu tiên theo vị trí và trạng thái sẵn sàng (Business Rule RL01). | — | 200, 404 |
| POST | `/internal/matching/next-driver` | Tìm tài xế thay thế | FR06 - Tìm tài xế thay thế. Được gọi khi tài xế được đề xuất từ chối hoặc không phản hồi trong thời gian quy định (Business Rule RL02). | — | 200, 404 |
| POST | `/trips/{tripId}/response` | Chấp nhận hoặc từ chối yêu cầu chuyến | FR05 - Gửi yêu cầu & xử lý phản hồi tài xế. Nếu tài xế từ chối hoặc không phản hồi trong thời gian quy định, hệ thống tự động tìm tài xế thay thế (FR06 - Internal Services). | — | 200, 409 |
| PUT | `/trips/{tripId}/status` | Cập nhật trạng thái thực hiện chuyến | FR07 - Cập nhật trạng thái & vị trí chuyến. Các trạng thái hợp lệ: arrived_at_pickup -> picked_up -> in_progress -> completed (theo đúng thứ tự). | — | 200, 400 |

---

## 5. Location API
**File:** `5_location_api.yaml`

Ghi nhận vị trí tài xế theo thời gian thực, phục vụ tìm tài xế gần khách hàng và tính thời gian dự kiến đến (FR07). Tách riêng khỏi Trip API do tần suất cập nhật rất cao (mỗi vài giây), cần khả năng mở rộng độc lập (NFR02).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| PUT | `/drivers/{driverId}/location` | Cập nhật vị trí hiện tại của tài xế | FR07 - Cập nhật trạng thái & vị trí chuyến. Hỗ trợ tìm tài xế gần khách hàng và tính ETA. | `Location` | 204 |

---

## 6. Notification API
**File:** `6_notification_api.yaml`

Gửi và quản lý thông báo cho khách hàng và tài xế trong suốt quy trình đặt xe, thực hiện chuyến và thanh toán (FR12). Được các service khác (Trip, Payment, Matching) gọi đến khi có sự kiện cần thông báo.

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| GET | `/customers/{customerId}/notifications` | Xem danh sách thông báo của khách hàng | FR12 - Gửi thông báo. Bao gồm các sự kiện: tiếp nhận yêu cầu, tài xế nhận chuyến, tài xế đến điểm đón, hoàn thành chuyến, kết quả thanh toán, không tìm được tài xế. | — | 200 |
| GET | `/drivers/{driverId}/notifications` | Xem danh sách thông báo của tài xế | FR12 - Gửi thông báo. Bao gồm: chuyến mới, thay đổi liên quan đến chuyến đang thực hiện. | — | 200 |
| PATCH | `/notifications/{notificationId}` | Đánh dấu thông báo đã đọc | FR12 - Gửi thông báo. | — | 200, 404 |
| DELETE | `/notifications/{notificationId}` | Xóa thông báo | FR12 - Gửi thông báo. | — | 204, 404 |

---

## 7. Payment API
**File:** `7_payment_api.yaml`

Tính cước, thanh toán (tiền mặt/điện tử), xử lý kết quả giao dịch và webhook từ nhà cung cấp thanh toán (FR09, FR10, FR11).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| POST | `/internal/trips/{tripId}/calculate-fare` | Tính cước chuyến đi | FR09 - Tính cước. Được gọi ngay sau khi tài xế cập nhật trạng thái "completed" (PUT /trips/{tripId}/status), trước khi khách hàng thanh toán. | — | 200 |
| GET | `/trips/{tripId}/payment` | Xem chi tiết thanh toán của chuyến đi | FR10 - Thanh toán. Xem trạng thái và chi tiết giao dịch thanh toán. | — | 200, 404 |
| POST | `/trips/{tripId}/payment` | Thực hiện thanh toán cho chuyến đi | FR10 - Thanh toán. Hỗ trợ thanh toán tiền mặt hoặc điện tử qua nhà cung cấp thanh toán bên ngoài. Không lưu trực tiếp thông tin thẻ/tài khoản thanh toán nhạy cảm trong hệ thống (NFR08, Business Rule RL04). | `PaymentRequest` | 202, 400, 404 |
| POST | `/trips/{tripId}/payment/retry` | Xử lý lại thanh toán điện tử sau khi thất bại | FR11 - Ghi nhận & xử lý kết quả thanh toán (Business Rule RL07). | — | 202, 404, 409 |
| POST | `/webhooks/payments/callback` *(public)* | Nhận kết quả giao dịch từ nhà cung cấp thanh toán | FR11 - Ghi nhận & xử lý kết quả thanh toán. Nhà cung cấp thanh toán bên ngoài gọi endpoint này để trả kết quả giao dịch điện tử. Khi thất bại, hệ thống kích hoạt thông báo cho khách hàng (FR12, Business Rule RL07). | — | 200, 400, 401 |

---

## 8. Rating & History API
**File:** `8_rating_history_api.yaml`

Xem lịch sử chuyến đi, số tiền phải trả và đánh giá tài xế sau chuyến (FR13, FR14).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| GET | `/customers/{customerId}/trips` | Xem lịch sử các chuyến đã thực hiện | FR13 - Xem lịch sử & số tiền phải trả. | — | 200 |
| GET | `/drivers/{driverId}/trips` | Xem lịch sử các chuyến đã thực hiện | FR07 - Cập nhật trạng thái & vị trí chuyến. | — | 200 |
| GET | `/trips/{tripId}/fare` | Xem số tiền phải trả của chuyến | FR13 - Xem lịch sử & số tiền phải trả. Yêu cầu chuyến đã được tính cước (FR09 - Internal Services). | — | 200, 404 |
| GET | `/trips/{tripId}/rating` | Xem đánh giá đã gửi cho chuyến đi | FR14 - Đánh giá tài xế. | — | 200, 404 |
| POST | `/trips/{tripId}/rating` | Đánh giá tài xế sau khi hoàn thành chuyến | FR14 - Đánh giá tài xế. Chỉ áp dụng khi chuyến đã hoàn thành (Business Rule RL06). | `RatingRequest` | 201, 400, 409 |
| PUT | `/trips/{tripId}/rating` | Chỉnh sửa đánh giá đã gửi | FR14 - Đánh giá tài xế. | `RatingRequest` | 200, 404 |
| DELETE | `/trips/{tripId}/rating` | Xóa đánh giá đã gửi | FR14 - Đánh giá tài xế. | — | 204, 404 |

---

## 9. Admin Users API
**File:** `9_admin_users_api.yaml`

Nhân viên vận hành quản lý tài khoản khách hàng, tài xế và phương tiện (FR15).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| GET | `/operator/customers` | Xem danh sách khách hàng | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 200 |
| GET | `/operator/customers/{customerId}` | Xem chi tiết một khách hàng | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 200, 404 |
| PUT | `/operator/customers/{customerId}` | Cập nhật/khóa thông tin khách hàng | FR15 - Quản lý khách hàng, tài xế, phương tiện. | `CustomerUpdateRequest` | 200, 404 |
| DELETE | `/operator/customers/{customerId}` | Vô hiệu hóa tài khoản khách hàng | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 204, 404 |
| GET | `/operator/drivers` | Xem danh sách tài xế | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 200 |
| POST | `/operator/drivers` | Tạo tài khoản tài xế thay cho tài xế | FR15 - Quản lý khách hàng, tài xế, phương tiện (tài xế được nhân viên vận hành tạo tài khoản, xem FR01). | `DriverRegisterRequest` | 201, 400 |
| GET | `/operator/drivers/{driverId}` | Xem chi tiết một tài xế | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 200, 404 |
| PUT | `/operator/drivers/{driverId}` | Cập nhật/khóa thông tin tài xế | FR15 - Quản lý khách hàng, tài xế, phương tiện. | `DriverUpdateRequest` | 200, 404 |
| DELETE | `/operator/drivers/{driverId}` | Vô hiệu hóa tài khoản tài xế | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 204, 404 |
| GET | `/operator/vehicles` | Xem danh sách phương tiện | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 200 |
| GET | `/operator/vehicles/{vehicleId}` | Xem chi tiết một phương tiện | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 200, 404 |
| PUT | `/operator/vehicles/{vehicleId}` | Cập nhật/khóa thông tin phương tiện | FR15 - Quản lý khách hàng, tài xế, phương tiện. | `VehicleRequest` | 200, 404 |
| DELETE | `/operator/vehicles/{vehicleId}` | Gỡ bỏ phương tiện khỏi hệ thống | FR15 - Quản lý khách hàng, tài xế, phương tiện. | — | 204, 404 |

---

## 10. Admin Staff & Roles API
**File:** `10_admin_staff_api.yaml`

Quản lý tài khoản nhân viên vận hành và phân quyền truy cập nội bộ (FR18, NFR06). Tách riêng khỏi Admin Users API vì đây là bounded context về danh tính & quyền truy cập nội bộ (IAM), không phải dữ liệu nghiệp vụ khách hàng/tài xế.

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| GET | `/operator/staff` | Xem danh sách nhân viên vận hành | FR18 - Phân quyền quản trị. | — | 200 |
| POST | `/operator/staff` | Tạo tài khoản nhân viên vận hành | FR18 - Phân quyền quản trị. Chỉ nhân viên có quyền quản trị (operator_admin) mới được tạo tài khoản nhân viên mới (NFR06, Business Rule RL08). | `StaffRequest` | 201, 400, 403 |
| GET | `/operator/staff/{staffId}` | Xem chi tiết một nhân viên vận hành | FR18 - Phân quyền quản trị. | — | 200, 404 |
| DELETE | `/operator/staff/{staffId}` | Vô hiệu hóa tài khoản nhân viên vận hành | FR18 - Phân quyền quản trị (NFR06, Business Rule RL08). | — | 204, 403, 404 |
| PUT | `/operator/staff/{staffId}/roles` | Phân quyền cho nhân viên vận hành | FR18 - Phân quyền quản trị (NFR06, Business Rule RL08). Chỉ nhân viên có quyền quản trị mới được gọi API này. | — | 200, 403 |

---

## 11. Admin Operations API
**File:** `11_admin_operations_api.yaml`

Nhân viên vận hành theo dõi/xử lý chuyến đi, tra cứu giao dịch và xem báo cáo hoạt động (FR16, FR17, FR19).

| Method | Endpoint | Chức năng | Mô tả | Request Body | Response codes |
|---|---|---|---|---|---|
| GET | `/operator/trips` | Xem danh sách chuyến đi | FR16 - Quản lý & xử lý chuyến đi. | — | 200 |
| GET | `/operator/trips/ongoing` | Xem danh sách chuyến đang diễn ra theo thời gian thực | FR16 - Quản lý & xử lý chuyến đi. Bao gồm trạng thái tài xế liên quan. | — | 200 |
| POST | `/operator/trips/{tripId}/resolve` | Xử lý chuyến đi gặp sự cố | FR16 - Quản lý & xử lý chuyến đi (Exception EX05). | — | 200 |
| GET | `/operator/transactions` | Tra cứu lịch sử giao dịch thanh toán | FR17 - Tra cứu lịch sử giao dịch. | — | 200 |
| GET | `/operator/reports` | Xem báo cáo hoạt động hệ thống | FR19 - Báo cáo hoạt động. Bao gồm số lượng chuyến, doanh thu, tỷ lệ hoàn thành, tỷ lệ hủy và hiệu quả hoạt động của tài xế. | — | 200 |

---
