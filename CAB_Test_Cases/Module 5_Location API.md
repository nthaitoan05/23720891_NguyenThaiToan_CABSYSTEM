# Module 5 — Location API — Test Cases (Draft)

> Nguồn tham chiếu: `5_location_api.yaml` — `PUT /drivers/{driverId}/location` (FR07, NFR02).
> Chỉ 1 endpoint duy nhất, tách riêng khỏi Trip API do tần suất ghi rất cao (mỗi vài giây).

## Scenario: Cập nhật vị trí tài xế (FR07)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-LOCATION-001 | Cập nhật vị trí tài xế | Cập nhật vị trí hợp lệ khi tài xế đang rảnh (không có chuyến) | Tài xế đã đăng nhập, không có chuyến đang thực hiện | 1. Gửi request PUT /drivers/{driverId}/location | latitude: 10.776<br>longitude: 106.700 | Cập nhật thành công; status 204 | High |
| TC-LOCATION-002 | Cập nhật vị trí tài xế | Cập nhật vị trí hợp lệ khi tài xế đang thực hiện chuyến (in_progress) | Tài xế có 1 chuyến ở trạng thái in_progress | 1. Gửi request PUT /drivers/{driverId}/location trong lúc đang chạy chuyến | latitude: 10.780<br>longitude: 106.710 | Cập nhật thành công; status 204; vị trí mới được dùng để tính lại ETA cho chuyến đang chạy | High |
| TC-LOCATION-003 | Cập nhật vị trí tài xế | Cập nhật vị trí với driverId không tồn tại | driverId không có trong hệ thống | 1. Gửi request PUT /drivers/{driverId}/location với id không tồn tại | driverId: 00000000-0000-0000-0000-000000000000<br>latitude: 10.776<br>longitude: 106.700 | Trả về status 404 | Medium |
| TC-LOCATION-004 | Cập nhật vị trí tài xế | Tài xế cố cập nhật vị trí cho driverId khác (không phải chính mình) | Đăng nhập bằng tài xế A, thao tác trên id của tài xế B | 1. Dùng token tài xế A\n2. Gửi request PUT /drivers/{driverId}/location với id tài xế B | driverId: (id tài xế B)<br>latitude, longitude hợp lệ | Từ chối truy cập; status 403 | High |
| TC-LOCATION-005 | Cập nhật vị trí tài xế | Gọi API không có token xác thực | Không đăng nhập | 1. Gửi request PUT /drivers/{driverId}/location không kèm token | (không có Authorization header) | Từ chối truy cập; status 401 | High |
| TC-LOCATION-006 | Cập nhật vị trí tài xế | latitude ở biên giá trị hợp lệ tối đa (90) | Tài xế đã đăng nhập | 1. Gửi request PUT với latitude = 90 | latitude: 90<br>longitude: 106.700 | Cập nhật thành công; status 204 | Low |
| TC-LOCATION-007 | Cập nhật vị trí tài xế | latitude ở biên giá trị hợp lệ tối thiểu (-90) | Tài xế đã đăng nhập | 1. Gửi request PUT với latitude = -90 | latitude: -90<br>longitude: 106.700 | Cập nhật thành công; status 204 | Low |
| TC-LOCATION-008 | Cập nhật vị trí tài xế | latitude vượt quá giới hạn hợp lệ (91) | Tài xế đã đăng nhập | 1. Gửi request PUT với latitude = 91 | latitude: 91<br>longitude: 106.700 | Cập nhật thất bại; status 400; lỗi tọa độ latitude không hợp lệ | Medium |
| TC-LOCATION-009 | Cập nhật vị trí tài xế | longitude ở biên giá trị hợp lệ (180) | Tài xế đã đăng nhập | 1. Gửi request PUT với longitude = 180 | latitude: 10.776<br>longitude: 180 | Cập nhật thành công; status 204 | Low |
| TC-LOCATION-010 | Cập nhật vị trí tài xế | longitude vượt quá giới hạn hợp lệ (-181) | Tài xế đã đăng nhập | 1. Gửi request PUT với longitude = -181 | latitude: 10.776<br>longitude: -181 | Cập nhật thất bại; status 400; lỗi tọa độ longitude không hợp lệ | Medium |
| TC-LOCATION-011 | Cập nhật vị trí tài xế | Để trống latitude | Tài xế đã đăng nhập | 1. Gửi request PUT, bỏ trống latitude | latitude: (rỗng)<br>longitude: 106.700 | Cập nhật thất bại; status 400; lỗi thiếu latitude (required) | High |
| TC-LOCATION-012 | Cập nhật vị trí tài xế | Để trống longitude | Tài xế đã đăng nhập | 1. Gửi request PUT, bỏ trống longitude | latitude: 10.776<br>longitude: (rỗng) | Cập nhật thất bại; status 400; lỗi thiếu longitude (required) | High |
| TC-LOCATION-013 | Cập nhật vị trí tài xế | Gửi request với body rỗng | Tài xế đã đăng nhập | 1. Gửi request PUT /drivers/{driverId}/location với body {} | (body rỗng) | Cập nhật thất bại; status 400; lỗi thiếu latitude và longitude | High |
| TC-LOCATION-014 | Cập nhật vị trí tài xế | latitude gửi dạng chuỗi thay vì số | Tài xế đã đăng nhập | 1. Gửi request PUT với latitude là chuỗi | latitude: "abc"<br>longitude: 106.700 | Cập nhật thất bại; status 400; lỗi sai kiểu dữ liệu | Medium |
| TC-LOCATION-015 | Cập nhật vị trí tài xế | longitude gửi giá trị không phải số hợp lệ (NaN) | Tài xế đã đăng nhập | 1. Gửi request PUT với longitude = NaN | latitude: 10.776<br>longitude: NaN | Cập nhật thất bại; status 400; lỗi sai kiểu dữ liệu | Medium |
