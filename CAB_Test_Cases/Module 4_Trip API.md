# Module 4 — Trip API — Test Cases (Draft)

> Nguồn tham chiếu: `4_trip_api.yaml` — `POST /trips`, `GET/DELETE /trips/{tripId}`,
> `POST /internal/matching/find-driver`, `POST /internal/matching/next-driver`,
> `POST /trips/{tripId}/response`, `PUT /trips/{tripId}/status` (FR03–FR08, Business Rule RL01/RL02).

## Scenario 1: Tạo yêu cầu đặt xe (FR03)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-BOOKING-001 | Tạo yêu cầu đặt xe | Tạo yêu cầu với đầy đủ thông tin hợp lệ | Khách hàng đã đăng nhập, không có chuyến nào đang hoạt động | 1. Gửi request POST /trips<br>2. Nhập pickupLocation, destination, vehicleType hợp lệ | pickupLocation: (10.776,106.700)<br>destination: (10.800,106.650)<br>vehicleType: 4-seat | Tạo yêu cầu thành công; status 201; trip status = requested/finding_driver | High |
| TC-BOOKING-002 | Tạo yêu cầu đặt xe | Tạo yêu cầu với loại xe khác (7-seat) | Khách hàng đã đăng nhập | 1. Gửi request POST /trips với vehicleType khác | vehicleType: 7-seat | Tạo yêu cầu thành công; status 201 | Medium |
| TC-BOOKING-003 | Tạo yêu cầu đặt xe | Tạo yêu cầu khi khách hàng đang có 1 chuyến chưa hoàn thành | Khách hàng đang có 1 chuyến ở trạng thái in_progress | 1. Gửi request POST /trips khi đang có chuyến hoạt động | pickupLocation, destination, vehicleType hợp lệ | Từ chối tạo chuyến mới; status 409; thông báo đang có chuyến hoạt động | High |
| TC-BOOKING-004 | Tạo yêu cầu đặt xe | Tạo yêu cầu với vehicleType không được hệ thống hỗ trợ | Khách hàng đã đăng nhập | 1. Gửi request POST /trips với vehicleType không tồn tại | vehicleType: limousine-99 | Tạo yêu cầu thất bại; status 400; lỗi loại xe không hợp lệ | Medium |
| TC-BOOKING-005 | Tạo yêu cầu đặt xe | Tạo yêu cầu khi token hết hạn/không hợp lệ | Token đã hết hạn | 1. Gửi request POST /trips với token hết hạn | (token hết hạn) | Từ chối request; status 401 | High |
| TC-BOOKING-006 | Tạo yêu cầu đặt xe | latitude ở biên giá trị hợp lệ tối đa (90) | Khách hàng đã đăng nhập | 1. Gửi request với pickupLocation.latitude = 90 | latitude: 90<br>longitude: 106.700 | Tạo yêu cầu thành công; status 201 | Low |
| TC-BOOKING-007 | Tạo yêu cầu đặt xe | latitude vượt quá giới hạn hợp lệ (91) | Khách hàng đã đăng nhập | 1. Gửi request với pickupLocation.latitude = 91 | latitude: 91<br>longitude: 106.700 | Tạo yêu cầu thất bại; status 400; lỗi tọa độ không hợp lệ | Medium |
| TC-BOOKING-008 | Tạo yêu cầu đặt xe | longitude ở biên giá trị hợp lệ tối đa (180) | Khách hàng đã đăng nhập | 1. Gửi request với destination.longitude = 180 | latitude: 10.800<br>longitude: 180 | Tạo yêu cầu thành công; status 201 | Low |
| TC-BOOKING-009 | Tạo yêu cầu đặt xe | longitude vượt quá giới hạn hợp lệ (-181) | Khách hàng đã đăng nhập | 1. Gửi request với destination.longitude = -181 | latitude: 10.800<br>longitude: -181 | Tạo yêu cầu thất bại; status 400; lỗi tọa độ không hợp lệ | Medium |
| TC-BOOKING-010 | Tạo yêu cầu đặt xe | Điểm đón và điểm đến trùng nhau | Khách hàng đã đăng nhập | 1. Gửi request với pickupLocation = destination | pickupLocation: (10.776,106.700)<br>destination: (10.776,106.700) | Từ chối tạo yêu cầu; status 400; thông báo điểm đón/đến trùng nhau | Medium |
| TC-BOOKING-011 | Tạo yêu cầu đặt xe | Để trống pickupLocation | Khách hàng đã đăng nhập | 1. Gửi request POST /trips, bỏ trống pickupLocation | pickupLocation: (rỗng) | Tạo yêu cầu thất bại; status 400; lỗi thiếu pickupLocation (required) | High |
| TC-BOOKING-012 | Tạo yêu cầu đặt xe | Để trống destination | Khách hàng đã đăng nhập | 1. Gửi request POST /trips, bỏ trống destination | destination: (rỗng) | Tạo yêu cầu thất bại; status 400; lỗi thiếu destination (required) | High |
| TC-BOOKING-013 | Tạo yêu cầu đặt xe | Để trống vehicleType | Khách hàng đã đăng nhập | 1. Gửi request POST /trips, bỏ trống vehicleType | vehicleType: (rỗng) | Tạo yêu cầu thất bại; status 400; lỗi thiếu vehicleType (required) | High |
| TC-BOOKING-014 | Tạo yêu cầu đặt xe | latitude gửi dạng chuỗi thay vì số | Khách hàng đã đăng nhập | 1. Gửi request với latitude là chuỗi | latitude: "abc" | Tạo yêu cầu thất bại; status 400; lỗi sai kiểu dữ liệu | Medium |
| TC-BOOKING-015 | Tạo yêu cầu đặt xe | vehicleType gửi dạng số thay vì chuỗi | Khách hàng đã đăng nhập | 1. Gửi request với vehicleType là số | vehicleType: 4 | Tạo yêu cầu thất bại; status 400; lỗi sai kiểu dữ liệu | Medium |

## Scenario 2: Hủy yêu cầu đặt xe (FR03)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-TRIP-CANCEL-001 | Hủy yêu cầu đặt xe | Hủy chuyến khi đang ở trạng thái requested | Chuyến vừa được tạo, chưa tìm tài xế | 1. Gửi request DELETE /trips/{tripId} | tripId: (chuyến trạng thái requested) | Hủy thành công; status 204; trip status = cancelled | High |
| TC-TRIP-CANCEL-002 | Hủy yêu cầu đặt xe | Hủy chuyến khi đang ở trạng thái finding_driver | Chuyến đang trong quá trình tìm tài xế | 1. Gửi request DELETE /trips/{tripId} | tripId: (chuyến trạng thái finding_driver) | Hủy thành công; status 204 | High |
| TC-TRIP-CANCEL-003 | Hủy yêu cầu đặt xe | Hủy chuyến khi đã có tài xế nhận (driver_assigned), chưa đón khách | Chuyến đã có tài xế nhận, chưa đến điểm đón | 1. Gửi request DELETE /trips/{tripId} | tripId: (chuyến trạng thái driver_assigned) | Hủy thành công; status 204; tài xế được thông báo chuyến đã hủy | High |
| TC-TRIP-CANCEL-004 | Hủy yêu cầu đặt xe | Hủy chuyến với tripId không tồn tại | tripId không có trong hệ thống | 1. Gửi request DELETE /trips/{tripId} với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | High |
| TC-TRIP-CANCEL-005 | Hủy yêu cầu đặt xe | Hủy chuyến khi đã đón khách (picked_up) | Chuyến đang ở trạng thái picked_up | 1. Gửi request DELETE /trips/{tripId} | tripId: (chuyến trạng thái picked_up) | Từ chối hủy; status 409; thông báo chuyến không thể hủy ở trạng thái này | High |
| TC-TRIP-CANCEL-006 | Hủy yêu cầu đặt xe | Hủy chuyến đã hoàn thành (completed) | Chuyến đã completed | 1. Gửi request DELETE /trips/{tripId} | tripId: (chuyến trạng thái completed) | Từ chối hủy; status 409 | High |
| TC-TRIP-CANCEL-007 | Hủy yêu cầu đặt xe | Khách hàng cố hủy chuyến của khách hàng khác | Đăng nhập bằng khách hàng A, thao tác trên tripId của khách hàng B | 1. Dùng token khách hàng A\n2. Gửi request DELETE /trips/{tripId} của khách hàng B | tripId: (chuyến của khách hàng B) | Từ chối truy cập; status 403 | High |
| TC-TRIP-CANCEL-008 | Hủy yêu cầu đặt xe | Gửi yêu cầu hủy đúng lúc tài xế vừa cập nhật sang picked_up (race condition) | Chuyến đang chuyển trạng thái đồng thời với yêu cầu hủy | 1. Gửi PUT status=picked_up và DELETE /trips/{tripId} gần như đồng thời | tripId: (chuyến đang chuyển trạng thái) | Hệ thống xử lý nhất quán: chỉ 1 trong 2 thao tác thành công, không để dữ liệu chuyến ở trạng thái mâu thuẫn | Medium |
| TC-TRIP-CANCEL-009 | Hủy yêu cầu đặt xe | Gửi request hủy không có token xác thực | Không đăng nhập | 1. Gửi request DELETE /trips/{tripId} không kèm token | (không có Authorization header) | Từ chối truy cập; status 401 | High |
| TC-TRIP-CANCEL-010 | Hủy yêu cầu đặt xe | tripId sai định dạng UUID | Khách hàng đã đăng nhập | 1. Gửi request DELETE /trips/{tripId} với id không đúng định dạng | tripId: abc-not-a-uuid | Từ chối request; status 400 | Medium |

## Scenario 3: Tìm & ưu tiên tài xế phù hợp (FR04, nội bộ)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-MATCH-FIND-001 | Tìm & ưu tiên tài xế phù hợp | Tìm tài xế khi có tài xế available gần điểm đón | Có ít nhất 1 tài xế available, đúng vehicleType, trong bán kính tìm kiếm | 1. Gọi POST /internal/matching/find-driver | tripId, pickupLocation, vehicleType: 4-seat | Trả về status 200; danh sách tài xế phù hợp, có priorityScore | High |
| TC-MATCH-FIND-002 | Tìm & ưu tiên tài xế phù hợp | Danh sách trả về được sắp xếp đúng theo độ ưu tiên | Có nhiều tài xế available với khoảng cách khác nhau | 1. Gọi POST /internal/matching/find-driver với nhiều tài xế ứng viên | (nhiều tài xế ở khoảng cách khác nhau) | Danh sách trả về sắp xếp priorityScore giảm dần (tài xế gần nhất/ưu tiên nhất đứng đầu) | High |
| TC-MATCH-FIND-003 | Tìm & ưu tiên tài xế phù hợp | Không có tài xế nào available trong khu vực | Không có tài xế available trong bán kính tìm kiếm | 1. Gọi POST /internal/matching/find-driver khi không có tài xế | pickupLocation: (khu vực không có tài xế) | Trả về status 404; kích hoạt luồng thông báo "không tìm được tài xế" (FR06/FR12) | High |
| TC-MATCH-FIND-004 | Tìm & ưu tiên tài xế phù hợp | Có tài xế available nhưng không đúng vehicleType yêu cầu | Tài xế available chỉ có loại xe khác với yêu cầu | 1. Gọi POST /internal/matching/find-driver với vehicleType không khớp tài xế hiện có | vehicleType: 7-seat (chỉ có tài xế 4-seat) | Trả về status 404; không có tài xế phù hợp loại xe | High |
| TC-MATCH-FIND-005 | Tìm & ưu tiên tài xế phù hợp | Chỉ có đúng 1 tài xế available đủ điều kiện | Chỉ có 1 tài xế available phù hợp | 1. Gọi POST /internal/matching/find-driver | (1 tài xế đủ điều kiện) | Trả về status 200; danh sách có đúng 1 phần tử | Medium |
| TC-MATCH-FIND-006 | Tìm & ưu tiên tài xế phù hợp | Tài xế ở đúng ranh giới bán kính tìm kiếm tối đa (giả định 5km) | Tài xế cách điểm đón đúng 5km | 1. Gọi POST /internal/matching/find-driver | (tài xế cách 5.0km) | Trả về status 200; tài xế được tính vào danh sách kết quả | Medium |
| TC-MATCH-FIND-007 | Tìm & ưu tiên tài xế phù hợp | Tài xế ngoài ranh giới bán kính tìm kiếm tối đa | Tài xế cách điểm đón hơn 5km | 1. Gọi POST /internal/matching/find-driver | (tài xế cách 5.1km) | Tài xế không được tính vào danh sách kết quả | Medium |
| TC-MATCH-FIND-008 | Tìm & ưu tiên tài xế phù hợp | Gửi request thiếu tripId | Service đang hoạt động | 1. Gọi POST /internal/matching/find-driver, bỏ trống tripId | tripId: (rỗng) | Trả về status 400; lỗi thiếu tripId (required) | Medium |
| TC-MATCH-FIND-009 | Tìm & ưu tiên tài xế phù hợp | Gửi request thiếu pickupLocation | Service đang hoạt động | 1. Gọi POST /internal/matching/find-driver, bỏ trống pickupLocation | pickupLocation: (rỗng) | Trả về status 400; lỗi thiếu pickupLocation (required) | Medium |
| TC-MATCH-FIND-010 | Tìm & ưu tiên tài xế phù hợp | vehicleType gửi giá trị không thuộc danh sách hỗ trợ | Service đang hoạt động | 1. Gọi POST /internal/matching/find-driver với vehicleType không hợp lệ | vehicleType: spaceship | Trả về status 400 | Low |
| TC-MATCH-FIND-011 | Tìm & ưu tiên tài xế phù hợp | pickupLocation sai kiểu dữ liệu | Service đang hoạt động | 1. Gọi POST /internal/matching/find-driver với latitude là chuỗi | latitude: "abc" | Trả về status 400; lỗi sai kiểu dữ liệu | Medium |

## Scenario 4: Tài xế chấp nhận/từ chối chuyến (FR05)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-DRV-RESPONSE-001 | Tài xế chấp nhận/từ chối chuyến | Tài xế chấp nhận chuyến trong thời gian quy định | Tài xế được hệ thống đề xuất, yêu cầu còn hiệu lực | 1. Gửi request POST /trips/{tripId}/response | driverId: (id tài xế được đề xuất)<br>decision: accepted | Ghi nhận thành công; status 200; trip status chuyển sang driver_assigned | High |
| TC-DRV-RESPONSE-002 | Tài xế chấp nhận/từ chối chuyến | Tài xế từ chối chuyến | Tài xế được hệ thống đề xuất | 1. Gửi request POST /trips/{tripId}/response | driverId: (id tài xế được đề xuất)<br>decision: rejected | Ghi nhận thành công; status 200; hệ thống kích hoạt tìm tài xế thay thế (FR06) | High |
| TC-DRV-RESPONSE-003 | Tài xế chấp nhận/từ chối chuyến | Phản hồi sau khi yêu cầu đã hết hạn | Thời gian phản hồi quy định đã trôi qua | 1. Gửi request POST /trips/{tripId}/response sau thời gian hết hạn | driverId, decision: accepted | Từ chối; status 409; thông báo yêu cầu đã hết hạn | High |
| TC-DRV-RESPONSE-004 | Tài xế chấp nhận/từ chối chuyến | Phản hồi khi chuyến đã được tài xế khác xử lý | Chuyến đã được gán cho tài xế khác trước đó | 1. Gửi request POST /trips/{tripId}/response | driverId, decision: accepted | Từ chối; status 409; thông báo chuyến đã được xử lý | High |
| TC-DRV-RESPONSE-005 | Tài xế chấp nhận/từ chối chuyến | Tài xế không được phân công cố gửi phản hồi | driverId gửi lên không phải tài xế được hệ thống đề xuất cho chuyến này | 1. Gửi request POST /trips/{tripId}/response với driverId khác | driverId: (tài xế không liên quan) | Từ chối; status 403 | High |
| TC-DRV-RESPONSE-006 | Tài xế chấp nhận/từ chối chuyến | Phản hồi với tripId không tồn tại | tripId không có trong hệ thống | 1. Gửi request POST /trips/{tripId}/response với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-DRV-RESPONSE-007 | Tài xế chấp nhận/từ chối chuyến | Tài xế phản hồi đúng thời điểm hết hạn (biên thời gian timeout) | Yêu cầu vừa chạm mốc hết hạn | 1. Gửi request POST /trips/{tripId}/response đúng lúc hết hạn | driverId, decision: accepted | Hệ thống xử lý nhất quán theo 1 kết quả duy nhất (không lỗi hệ thống, không double-processing) | Medium |
| TC-DRV-RESPONSE-008 | Tài xế chấp nhận/từ chối chuyến | Tài xế phản hồi ngay lập tức sau khi nhận thông báo | Tài xế vừa nhận được thông báo chuyến mới | 1. Gửi request POST /trips/{tripId}/response ngay sau khi có thông báo | driverId, decision: accepted | Ghi nhận thành công; status 200 | Low |
| TC-DRV-RESPONSE-009 | Tài xế chấp nhận/từ chối chuyến | Gửi request thiếu driverId | Yêu cầu chuyến còn hiệu lực | 1. Gửi request POST /trips/{tripId}/response, bỏ trống driverId | driverId: (rỗng)<br>decision: accepted | Trả về status 400; lỗi thiếu driverId (required) | High |
| TC-DRV-RESPONSE-010 | Tài xế chấp nhận/từ chối chuyến | Gửi request thiếu decision | Yêu cầu chuyến còn hiệu lực | 1. Gửi request POST /trips/{tripId}/response, bỏ trống decision | driverId<br>decision: (rỗng) | Trả về status 400; lỗi thiếu decision (required) | High |
| TC-DRV-RESPONSE-011 | Tài xế chấp nhận/từ chối chuyến | decision gửi giá trị ngoài enum cho phép | Yêu cầu chuyến còn hiệu lực | 1. Gửi request POST /trips/{tripId}/response với decision không hợp lệ | decision: maybe | Trả về status 400; lỗi giá trị decision không hợp lệ | Medium |
| TC-DRV-RESPONSE-012 | Tài xế chấp nhận/từ chối chuyến | driverId sai định dạng UUID | Yêu cầu chuyến còn hiệu lực | 1. Gửi request POST /trips/{tripId}/response với driverId không đúng định dạng | driverId: not-a-uuid | Trả về status 400 | Medium |

## Scenario 5: Tìm tài xế thay thế (FR06, nội bộ)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-MATCH-RETRY-001 | Tìm tài xế thay thế | Tìm được tài xế thay thế phù hợp, không trùng danh sách loại trừ | Còn tài xế available ngoài excludedDriverIds | 1. Gọi POST /internal/matching/next-driver | tripId<br>excludedDriverIds: [driverA] | Trả về status 200; driverId trả về khác driverA | High |
| TC-MATCH-RETRY-002 | Tìm tài xế thay thế | excludedDriverIds có nhiều id, vẫn tìm đúng tài xế còn lại phù hợp | excludedDriverIds gồm 3 tài xế đã bị loại | 1. Gọi POST /internal/matching/next-driver | excludedDriverIds: [driverA, driverB, driverC] | Trả về status 200; driverId trả về không thuộc danh sách loại trừ | High |
| TC-MATCH-RETRY-003 | Tìm tài xế thay thế | Không còn tài xế nào phù hợp ngoài danh sách loại trừ | Toàn bộ tài xế available đã nằm trong excludedDriverIds | 1. Gọi POST /internal/matching/next-driver | excludedDriverIds: (toàn bộ tài xế available) | Trả về status 404; kích hoạt thông báo "không tìm được tài xế" cho khách hàng (FR12) | High |
| TC-MATCH-RETRY-004 | Tìm tài xế thay thế | tripId không tồn tại | tripId không có trong hệ thống | 1. Gọi POST /internal/matching/next-driver với tripId không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-MATCH-RETRY-005 | Tìm tài xế thay thế | excludedDriverIds chứa toàn bộ tài xế available (biên 0 tài xế còn lại) | Không còn tài xế nào ngoài danh sách loại trừ | 1. Gọi POST /internal/matching/next-driver | excludedDriverIds: (tất cả) | Trả về status 404 | Medium |
| TC-MATCH-RETRY-006 | Tìm tài xế thay thế | excludedDriverIds chỉ có 1 phần tử (vừa loại 1 tài xế) | Còn nhiều tài xế khác available | 1. Gọi POST /internal/matching/next-driver | excludedDriverIds: [driverA] | Trả về status 200; tìm được tài xế khác | Low |
| TC-MATCH-RETRY-007 | Tìm tài xế thay thế | Gửi request thiếu tripId | Service đang hoạt động | 1. Gọi POST /internal/matching/next-driver, bỏ trống tripId | tripId: (rỗng) | Trả về status 400; lỗi thiếu tripId (required) | Medium |
| TC-MATCH-RETRY-008 | Tìm tài xế thay thế | excludedDriverIds là mảng rỗng (hợp lệ - chưa loại tài xế nào) | Service đang hoạt động | 1. Gọi POST /internal/matching/next-driver với excludedDriverIds = [] | excludedDriverIds: [] | Trả về status 200; coi như tìm tài xế lần đầu | Low |
| TC-MATCH-RETRY-009 | Tìm tài xế thay thế | excludedDriverIds chứa giá trị không đúng định dạng UUID | Service đang hoạt động | 1. Gọi POST /internal/matching/next-driver với id không hợp lệ trong mảng | excludedDriverIds: ["not-a-uuid"] | Trả về status 400 | Medium |

## Scenario 6: Cập nhật trạng thái thực hiện chuyến (FR07)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-TRIP-STATUS-001 | Cập nhật trạng thái thực hiện chuyến | Cập nhật driver_assigned → arrived_at_pickup | Chuyến đang ở trạng thái driver_assigned | 1. Gửi request PUT /trips/{tripId}/status | status: arrived_at_pickup | Cập nhật thành công; status 200 | High |
| TC-TRIP-STATUS-002 | Cập nhật trạng thái thực hiện chuyến | Cập nhật arrived_at_pickup → picked_up | Chuyến đang ở trạng thái arrived_at_pickup | 1. Gửi request PUT /trips/{tripId}/status | status: picked_up | Cập nhật thành công; status 200 | High |
| TC-TRIP-STATUS-003 | Cập nhật trạng thái thực hiện chuyến | Cập nhật picked_up → in_progress | Chuyến đang ở trạng thái picked_up | 1. Gửi request PUT /trips/{tripId}/status | status: in_progress | Cập nhật thành công; status 200 | High |
| TC-TRIP-STATUS-004 | Cập nhật trạng thái thực hiện chuyến | Cập nhật in_progress → completed | Chuyến đang ở trạng thái in_progress | 1. Gửi request PUT /trips/{tripId}/status | status: completed | Cập nhật thành công; status 200; kích hoạt tính cước (FR09) | High |
| TC-TRIP-STATUS-005 | Cập nhật trạng thái thực hiện chuyến | Cập nhật sai thứ tự - nhảy cóc trạng thái | Chuyến đang ở trạng thái driver_assigned | 1. Gửi request PUT /trips/{tripId}/status nhảy thẳng sang completed | status: completed | Từ chối; status 400; lỗi trạng thái không hợp lệ theo thứ tự | High |
| TC-TRIP-STATUS-006 | Cập nhật trạng thái thực hiện chuyến | Cập nhật lùi trạng thái | Chuyến đang ở trạng thái in_progress | 1. Gửi request PUT /trips/{tripId}/status lùi về arrived_at_pickup | status: arrived_at_pickup | Từ chối; status 400; lỗi không được lùi trạng thái | High |
| TC-TRIP-STATUS-007 | Cập nhật trạng thái thực hiện chuyến | Cập nhật trạng thái cho chuyến đã cancelled | Chuyến đã ở trạng thái cancelled | 1. Gửi request PUT /trips/{tripId}/status | status: arrived_at_pickup | Từ chối; status 400; chuyến đã hủy không thể cập nhật | High |
| TC-TRIP-STATUS-008 | Cập nhật trạng thái thực hiện chuyến | Tài xế không được phân công chuyến cố cập nhật trạng thái | Tài xế đăng nhập không phải tài xế được gán cho chuyến này | 1. Gửi request PUT /trips/{tripId}/status với tài xế không liên quan | status: picked_up | Từ chối; status 403 | High |
| TC-TRIP-STATUS-009 | Cập nhật trạng thái thực hiện chuyến | Cập nhật nhảy vượt đúng 1 bước so với bước hợp lệ tiếp theo | Chuyến đang ở trạng thái arrived_at_pickup | 1. Gửi request PUT /trips/{tripId}/status bỏ qua picked_up, chuyển thẳng in_progress | status: in_progress | Từ chối; status 400; lỗi bỏ qua bước bắt buộc | Medium |
| TC-TRIP-STATUS-010 | Cập nhật trạng thái thực hiện chuyến | Gửi request thiếu trường status | Chuyến đang ở trạng thái hợp lệ để cập nhật | 1. Gửi request PUT /trips/{tripId}/status, bỏ trống status | status: (rỗng) | Từ chối; status 400; lỗi thiếu status (required) | High |
| TC-TRIP-STATUS-011 | Cập nhật trạng thái thực hiện chuyến | Gửi status với giá trị ngoài enum quy định | Chuyến đang ở trạng thái hợp lệ | 1. Gửi request PUT /trips/{tripId}/status với giá trị không hợp lệ | status: on_the_way | Từ chối; status 400; lỗi giá trị enum không hợp lệ | Medium |
| TC-TRIP-STATUS-012 | Cập nhật trạng thái thực hiện chuyến | tripId sai định dạng UUID | Tài xế đã đăng nhập | 1. Gửi request PUT /trips/{tripId}/status với id không đúng định dạng | tripId: not-a-uuid | Từ chối; status 400 | Medium |

## Scenario 7: Theo dõi chuyến đi (FR08)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-TRIP-TRACK-001 | Theo dõi chuyến đi | Xem trạng thái chuyến khi đang tìm tài xế | Chuyến đang ở trạng thái finding_driver | 1. Gửi request GET /trips/{tripId} | tripId: (chuyến trạng thái finding_driver) | Trả về status 200; status = finding_driver, driverId = null | High |
| TC-TRIP-TRACK-002 | Theo dõi chuyến đi | Xem trạng thái chuyến khi đã có tài xế nhận | Chuyến đang ở trạng thái driver_assigned | 1. Gửi request GET /trips/{tripId} | tripId: (chuyến trạng thái driver_assigned) | Trả về status 200; kèm driverId, estimatedArrivalTime | High |
| TC-TRIP-TRACK-003 | Theo dõi chuyến đi | Xem trạng thái chuyến khi đã hoàn thành | Chuyến đã completed | 1. Gửi request GET /trips/{tripId} | tripId: (chuyến trạng thái completed) | Trả về status 200; kèm fare, completedAt | High |
| TC-TRIP-TRACK-004 | Theo dõi chuyến đi | Xem chuyến với tripId không tồn tại | tripId không có trong hệ thống | 1. Gửi request GET /trips/{tripId} với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | High |
| TC-TRIP-TRACK-005 | Theo dõi chuyến đi | Khách hàng cố xem chuyến của khách hàng khác | Đăng nhập bằng khách hàng A, xem tripId của khách hàng B | 1. Dùng token khách hàng A\n2. Gửi request GET /trips/{tripId} của khách hàng B | tripId: (chuyến của khách hàng B) | Từ chối truy cập; status 403 | High |
| TC-TRIP-TRACK-006 | Theo dõi chuyến đi | Xem chuyến ngay sau khi tạo, chưa có tài xế | Chuyến vừa tạo, chưa qua bước matching | 1. Gửi request GET /trips/{tripId} ngay sau khi tạo | tripId: (chuyến vừa tạo) | Trả về status 200; driverId = null, estimatedArrivalTime = null | Medium |
| TC-TRIP-TRACK-007 | Theo dõi chuyến đi | Gửi request không có token xác thực | Không đăng nhập | 1. Gửi request GET /trips/{tripId} không kèm token | (không có Authorization header) | Từ chối truy cập; status 401 | High |
| TC-TRIP-TRACK-008 | Theo dõi chuyến đi | tripId sai định dạng UUID | Khách hàng đã đăng nhập | 1. Gửi request GET /trips/{tripId} với id không đúng định dạng | tripId: not-a-uuid | Từ chối request; status 400 | Medium |
