# Module 9 — Admin Users API — Test Cases (Draft)

> Nguồn tham chiếu: `9_admin_users_api.yaml` — CRUD `/operator/customers`, `/operator/drivers`,
> `/operator/vehicles` (FR15). Yêu cầu vai trò `operator_basic`/`operator_admin` (NFR06, Business Rule RL08).

## Scenario 1: Quản lý khách hàng (operator) (FR15)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-ADM-CUST-001 | Quản lý khách hàng (operator) | Xem danh sách khách hàng | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/customers | (token operator) | Trả về status 200; danh sách khách hàng | High |
| TC-ADM-CUST-002 | Quản lý khách hàng (operator) | Xem chi tiết một khách hàng | Khách hàng tồn tại trong hệ thống | 1. Gửi request GET /operator/customers/{customerId} | customerId: (id hợp lệ) | Trả về status 200; đầy đủ thông tin khách hàng | High |
| TC-ADM-CUST-003 | Quản lý khách hàng (operator) | Cập nhật thông tin khách hàng | Khách hàng tồn tại | 1. Gửi request PUT /operator/customers/{customerId} | fullName: Nguyen Van Cap Nhat<br>address: 789 Tran Hung Dao | Cập nhật thành công; status 200 | High |
| TC-ADM-CUST-004 | Quản lý khách hàng (operator) | Vô hiệu hóa tài khoản khách hàng | Khách hàng đang ở trạng thái active | 1. Gửi request DELETE /operator/customers/{customerId} | customerId: (id hợp lệ) | Vô hiệu hóa thành công; status 204; status khách hàng chuyển sang locked | High |
| TC-ADM-CUST-005 | Quản lý khách hàng (operator) | Xem chi tiết khách hàng với customerId không tồn tại | customerId không có trong hệ thống | 1. Gửi request GET /operator/customers/{customerId} với id không tồn tại | customerId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-CUST-006 | Quản lý khách hàng (operator) | Cập nhật khách hàng với customerId không tồn tại | customerId không có trong hệ thống | 1. Gửi request PUT /operator/customers/{customerId} với id không tồn tại | customerId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-CUST-007 | Quản lý khách hàng (operator) | Vô hiệu hóa khách hàng với customerId không tồn tại | customerId không có trong hệ thống | 1. Gửi request DELETE /operator/customers/{customerId} với id không tồn tại | customerId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-CUST-008 | Quản lý khách hàng (operator) | Nhân viên không đủ quyền (operator_basic) cố vô hiệu hóa tài khoản khách hàng | Đăng nhập bằng vai trò operator_basic (không có quyền admin) | 1. Gửi request DELETE /operator/customers/{customerId} bằng tài khoản operator_basic | customerId: (id hợp lệ) | Từ chối; status 403; thiếu quyền thực hiện thao tác nhạy cảm | High |
| TC-ADM-CUST-009 | Quản lý khách hàng (operator) | Danh sách khách hàng có số lượng lớn | Hệ thống có hơn 1000 khách hàng | 1. Gửi request GET /operator/customers | (dữ liệu lớn) | Trả về status 200; hệ thống phản hồi ổn định, không timeout | Low |
| TC-ADM-CUST-010 | Quản lý khách hàng (operator) | Cập nhật khách hàng với body rỗng | Khách hàng tồn tại | 1. Gửi request PUT /operator/customers/{customerId} với body {} | (body rỗng) | Cập nhật thành công; status 200; dữ liệu giữ nguyên | Low |
| TC-ADM-CUST-011 | Quản lý khách hàng (operator) | customerId sai định dạng UUID | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET/PUT/DELETE /operator/customers/{customerId} với id không đúng định dạng | customerId: not-a-uuid | Từ chối; status 400 | Medium |

## Scenario 2: Quản lý tài xế (operator) (FR15)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-ADM-DRV-001 | Quản lý tài xế (operator) | Xem danh sách tài xế (không lọc) | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/drivers | (token operator) | Trả về status 200; danh sách toàn bộ tài xế | High |
| TC-ADM-DRV-002 | Quản lý tài xế (operator) | Xem danh sách tài xế lọc theo status=active | Có tài xế ở trạng thái active | 1. Gửi request GET /operator/drivers?status=active | status: active | Trả về status 200; chỉ gồm tài xế active | Medium |
| TC-ADM-DRV-003 | Quản lý tài xế (operator) | Xem chi tiết một tài xế | Tài xế tồn tại trong hệ thống | 1. Gửi request GET /operator/drivers/{driverId} | driverId: (id hợp lệ) | Trả về status 200; đầy đủ thông tin tài xế | High |
| TC-ADM-DRV-004 | Quản lý tài xế (operator) | Tạo tài khoản tài xế thay cho tài xế | phone và licenseNumber chưa tồn tại | 1. Gửi request POST /operator/drivers | fullName: Le Van Y<br>phone: 0987654321<br>password: Password@123<br>licenseNumber: B2-777777 | Tạo thành công; status 201 | High |
| TC-ADM-DRV-005 | Quản lý tài xế (operator) | Cập nhật thông tin tài xế | Tài xế tồn tại | 1. Gửi request PUT /operator/drivers/{driverId} | fullName: Le Van Y2<br>phone: 0987654322 | Cập nhật thành công; status 200 | High |
| TC-ADM-DRV-006 | Quản lý tài xế (operator) | Vô hiệu hóa tài khoản tài xế | Tài xế đang active | 1. Gửi request DELETE /operator/drivers/{driverId} | driverId: (id hợp lệ) | Vô hiệu hóa thành công; status 204 | High |
| TC-ADM-DRV-007 | Quản lý tài xế (operator) | Xem chi tiết tài xế với driverId không tồn tại | driverId không có trong hệ thống | 1. Gửi request GET /operator/drivers/{driverId} với id không tồn tại | driverId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-DRV-008 | Quản lý tài xế (operator) | Tạo tài khoản tài xế với licenseNumber đã tồn tại | licenseNumber đã được đăng ký bởi tài xế khác | 1. Gửi request POST /operator/drivers với licenseNumber trùng | licenseNumber: B2-777777 (đã tồn tại) | Từ chối; status 400; lỗi licenseNumber đã tồn tại | High |
| TC-ADM-DRV-009 | Quản lý tài xế (operator) | Cập nhật tài xế với driverId không tồn tại | driverId không có trong hệ thống | 1. Gửi request PUT /operator/drivers/{driverId} với id không tồn tại | driverId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-DRV-010 | Quản lý tài xế (operator) | Vô hiệu hóa tài xế với driverId không tồn tại | driverId không có trong hệ thống | 1. Gửi request DELETE /operator/drivers/{driverId} với id không tồn tại | driverId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-DRV-011 | Quản lý tài xế (operator) | Lọc theo status=suspended nhưng không có tài xế nào | Không có tài xế ở trạng thái suspended | 1. Gửi request GET /operator/drivers?status=suspended | status: suspended | Trả về status 200; danh sách rỗng [] | Low |
| TC-ADM-DRV-012 | Quản lý tài xế (operator) | Tạo tài khoản tài xế thiếu licenseNumber | Nhân viên vận hành đã đăng nhập | 1. Gửi request POST /operator/drivers, bỏ trống licenseNumber | licenseNumber: (rỗng) | Từ chối; status 400; lỗi thiếu licenseNumber (required) | High |
| TC-ADM-DRV-013 | Quản lý tài xế (operator) | Cập nhật tài xế với body rỗng | Tài xế tồn tại | 1. Gửi request PUT /operator/drivers/{driverId} với body {} | (body rỗng) | Cập nhật thành công; status 200; dữ liệu giữ nguyên | Low |
| TC-ADM-DRV-014 | Quản lý tài xế (operator) | status filter gửi giá trị ngoài enum cho phép | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/drivers?status=banned | status: banned | Từ chối; status 400; lỗi giá trị status không hợp lệ | Medium |
| TC-ADM-DRV-015 | Quản lý tài xế (operator) | driverId sai định dạng UUID | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET/PUT/DELETE /operator/drivers/{driverId} với id không đúng định dạng | driverId: not-a-uuid | Từ chối; status 400 | Medium |

## Scenario 3: Quản lý phương tiện (operator) (FR15)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-ADM-VEHICLE-001 | Quản lý phương tiện (operator) | Xem danh sách phương tiện | Có ít nhất 1 phương tiện trong hệ thống | 1. Gửi request GET /operator/vehicles | (token operator) | Trả về status 200; danh sách phương tiện | High |
| TC-ADM-VEHICLE-002 | Quản lý phương tiện (operator) | Xem chi tiết một phương tiện | Phương tiện tồn tại | 1. Gửi request GET /operator/vehicles/{vehicleId} | vehicleId: (id hợp lệ) | Trả về status 200; đầy đủ thông tin phương tiện | High |
| TC-ADM-VEHICLE-003 | Quản lý phương tiện (operator) | Cập nhật thông tin phương tiện | Phương tiện tồn tại | 1. Gửi request PUT /operator/vehicles/{vehicleId} | licensePlate: 51H-999.99<br>vehicleType: 4-seat | Cập nhật thành công; status 200 | High |
| TC-ADM-VEHICLE-004 | Quản lý phương tiện (operator) | Gỡ bỏ phương tiện khỏi hệ thống | Phương tiện tồn tại, không đang dùng cho chuyến nào | 1. Gửi request DELETE /operator/vehicles/{vehicleId} | vehicleId: (id hợp lệ) | Gỡ bỏ thành công; status 204 | Medium |
| TC-ADM-VEHICLE-005 | Quản lý phương tiện (operator) | Xem chi tiết phương tiện với vehicleId không tồn tại | vehicleId không có trong hệ thống | 1. Gửi request GET /operator/vehicles/{vehicleId} với id không tồn tại | vehicleId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-VEHICLE-006 | Quản lý phương tiện (operator) | Cập nhật phương tiện với vehicleId không tồn tại | vehicleId không có trong hệ thống | 1. Gửi request PUT /operator/vehicles/{vehicleId} với id không tồn tại | vehicleId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-VEHICLE-007 | Quản lý phương tiện (operator) | Gỡ bỏ phương tiện với vehicleId không tồn tại | vehicleId không có trong hệ thống | 1. Gửi request DELETE /operator/vehicles/{vehicleId} với id không tồn tại | vehicleId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-VEHICLE-008 | Quản lý phương tiện (operator) | Danh sách phương tiện rỗng | Hệ thống chưa có phương tiện nào | 1. Gửi request GET /operator/vehicles | (chưa có dữ liệu) | Trả về status 200; danh sách rỗng [] | Low |
| TC-ADM-VEHICLE-009 | Quản lý phương tiện (operator) | Cập nhật phương tiện thiếu licensePlate | Phương tiện tồn tại | 1. Gửi request PUT /operator/vehicles/{vehicleId}, bỏ trống licensePlate | licensePlate: (rỗng) | Từ chối; status 400; lỗi thiếu licensePlate (required) | High |
| TC-ADM-VEHICLE-010 | Quản lý phương tiện (operator) | Cập nhật phương tiện thiếu vehicleType | Phương tiện tồn tại | 1. Gửi request PUT /operator/vehicles/{vehicleId}, bỏ trống vehicleType | vehicleType: (rỗng) | Từ chối; status 400; lỗi thiếu vehicleType (required) | High |
| TC-ADM-VEHICLE-011 | Quản lý phương tiện (operator) | vehicleId sai định dạng UUID | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET/PUT/DELETE /operator/vehicles/{vehicleId} với id không đúng định dạng | vehicleId: not-a-uuid | Từ chối; status 400 | Medium |
