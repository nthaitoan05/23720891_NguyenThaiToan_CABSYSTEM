# Module 6 — Notification API — Test Cases (Draft)

> Nguồn tham chiếu: `6_notification_api.yaml` — `GET /customers/{customerId}/notifications`,
> `GET /drivers/{driverId}/notifications`, `PATCH/DELETE /notifications/{notificationId}` (FR12).

## Scenario: Gửi & nhận thông báo (khách hàng, tài xế) (FR12)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-NOTI-001 | Gửi & nhận thông báo | Xem danh sách thông báo của khách hàng thành công | Khách hàng đã có thông báo trong hệ thống | 1. Gửi request GET /customers/{customerId}/notifications | customerId: (id khách hàng) | Trả về status 200; danh sách thông báo (type, channel, message, isRead, createdAt) | High |
| TC-NOTI-002 | Gửi & nhận thông báo | Xem danh sách thông báo của tài xế thành công | Tài xế đã có thông báo trong hệ thống | 1. Gửi request GET /drivers/{driverId}/notifications | driverId: (id tài xế) | Trả về status 200; danh sách thông báo (chuyến mới, thay đổi chuyến) | High |
| TC-NOTI-003 | Gửi & nhận thông báo | Đánh dấu thông báo đã đọc | Thông báo tồn tại, đang ở trạng thái chưa đọc | 1. Gửi request PATCH /notifications/{notificationId} | isRead: true | Cập nhật thành công; status 200; isRead = true | High |
| TC-NOTI-004 | Gửi & nhận thông báo | Đánh dấu thông báo về trạng thái chưa đọc | Thông báo tồn tại, đang ở trạng thái đã đọc | 1. Gửi request PATCH /notifications/{notificationId} | isRead: false | Cập nhật thành công; status 200; isRead = false | Low |
| TC-NOTI-005 | Gửi & nhận thông báo | Xóa thông báo thành công | Thông báo tồn tại và thuộc về người dùng hiện tại | 1. Gửi request DELETE /notifications/{notificationId} | notificationId: (id thông báo hợp lệ) | Xóa thành công; status 204 | Medium |
| TC-NOTI-006 | Gửi & nhận thông báo | Khách hàng A xem danh sách thông báo của khách hàng B | Đăng nhập bằng khách hàng A, thao tác trên customerId của khách hàng B | 1. Dùng token khách hàng A\n2. Gửi request GET /customers/{customerId}/notifications với id khách hàng B | customerId: (id khách hàng B) | Từ chối truy cập; status 403 | High |
| TC-NOTI-007 | Gửi & nhận thông báo | Tài xế A xem danh sách thông báo của tài xế B | Đăng nhập bằng tài xế A, thao tác trên driverId của tài xế B | 1. Dùng token tài xế A\n2. Gửi request GET /drivers/{driverId}/notifications với id tài xế B | driverId: (id tài xế B) | Từ chối truy cập; status 403 | High |
| TC-NOTI-008 | Gửi & nhận thông báo | Đánh dấu đã đọc cho notificationId không tồn tại | notificationId không có trong hệ thống | 1. Gửi request PATCH /notifications/{notificationId} với id không tồn tại | notificationId: 00000000-0000-0000-0000-000000000000<br>isRead: true | Trả về status 404 | Medium |
| TC-NOTI-009 | Gửi & nhận thông báo | Xóa thông báo với notificationId không tồn tại | notificationId không có trong hệ thống | 1. Gửi request DELETE /notifications/{notificationId} với id không tồn tại | notificationId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-NOTI-010 | Gửi & nhận thông báo | Đánh dấu đã đọc/xóa thông báo không thuộc về người dùng hiện tại | Thông báo thuộc về người dùng khác | 1. Gửi request PATCH hoặc DELETE /notifications/{notificationId} của người dùng khác | notificationId: (thông báo của người khác) | Từ chối truy cập; status 403 | High |
| TC-NOTI-011 | Gửi & nhận thông báo | Khách hàng/tài xế chưa có thông báo nào | Tài khoản mới, chưa phát sinh sự kiện nào | 1. Gửi request GET /customers/{customerId}/notifications | customerId: (khách hàng mới) | Trả về status 200; danh sách rỗng [] | Medium |
| TC-NOTI-012 | Gửi & nhận thông báo | Khách hàng có số lượng thông báo lớn | Khách hàng có hơn 1000 thông báo tích lũy | 1. Gửi request GET /customers/{customerId}/notifications | customerId: (khách hàng nhiều thông báo) | Trả về status 200; hệ thống trả về đầy đủ/đúng cơ chế phân trang, không lỗi timeout | Low |
| TC-NOTI-013 | Gửi & nhận thông báo | Gửi PATCH với body rỗng (thiếu isRead) | Thông báo tồn tại | 1. Gửi request PATCH /notifications/{notificationId} với body {} | (body rỗng) | Trả về status 400; lỗi thiếu isRead (required) | High |
| TC-NOTI-014 | Gửi & nhận thông báo | Gọi PATCH với notificationId để trống trên URL | Client gọi sai route | 1. Gửi request PATCH /notifications/ (không có id) | (notificationId rỗng) | Trả về status 404 (route không hợp lệ) | Low |
| TC-NOTI-015 | Gửi & nhận thông báo | isRead gửi dạng chuỗi thay vì boolean | Thông báo tồn tại | 1. Gửi request PATCH /notifications/{notificationId} với isRead là chuỗi | isRead: "true" (string) | Trả về status 400; lỗi sai kiểu dữ liệu (yêu cầu boolean) | Medium |
| TC-NOTI-016 | Gửi & nhận thông báo | notificationId sai định dạng UUID | Người dùng đã đăng nhập | 1. Gửi request PATCH/DELETE /notifications/{notificationId} với id không đúng định dạng | notificationId: not-a-uuid | Trả về status 400 | Medium |
