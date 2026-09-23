# Module 11 — Admin Operations API — Test Cases (Draft)

> Nguồn tham chiếu: `11_admin_operations_api.yaml` — `GET /operator/trips`, `GET /operator/trips/ongoing`,
> `POST /operator/trips/{tripId}/resolve`, `GET /operator/transactions`, `GET /operator/reports`
> (FR16, FR17, FR19, Exception EX05).

## Scenario 1: Theo dõi & xử lý chuyến đi bất thường (FR16)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-ADM-TRIP-001 | Theo dõi & xử lý chuyến đi bất thường | Xem danh sách toàn bộ chuyến đi (không lọc) | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/trips | (không filter) | Trả về status 200; danh sách toàn bộ chuyến đi | High |
| TC-ADM-TRIP-002 | Theo dõi & xử lý chuyến đi bất thường | Xem danh sách chuyến lọc theo status=cancelled | Có chuyến ở trạng thái cancelled | 1. Gửi request GET /operator/trips?status=cancelled | status: cancelled | Trả về status 200; chỉ gồm chuyến đã hủy | Medium |
| TC-ADM-TRIP-003 | Theo dõi & xử lý chuyến đi bất thường | Xem danh sách chuyến lọc theo khoảng thời gian | Có chuyến trong khoảng from-to | 1. Gửi request GET /operator/trips?from=2026-01-01T00:00:00Z&to=2026-01-31T23:59:59Z | from, to hợp lệ | Trả về status 200; chỉ gồm chuyến trong khoảng thời gian | Medium |
| TC-ADM-TRIP-004 | Theo dõi & xử lý chuyến đi bất thường | Xem danh sách chuyến đang diễn ra theo thời gian thực | Có chuyến đang ở trạng thái in_progress/driver_assigned | 1. Gửi request GET /operator/trips/ongoing | (không tham số) | Trả về status 200; danh sách chuyến đang diễn ra kèm trạng thái tài xế | High |
| TC-ADM-TRIP-005 | Theo dõi & xử lý chuyến đi bất thường | Xử lý chuyến gặp sự cố - phân công lại tài xế | Chuyến đang gặp sự cố (tài xế không phản hồi) | 1. Gửi request POST /operator/trips/{tripId}/resolve | action: reassign_driver<br>note: Tài xế mất liên lạc, phân công lại | Xử lý thành công; status 200 | High |
| TC-ADM-TRIP-006 | Theo dõi & xử lý chuyến đi bất thường | Xử lý chuyến gặp sự cố - hủy chuyến | Chuyến gặp sự cố cần hủy | 1. Gửi request POST /operator/trips/{tripId}/resolve | action: cancel_trip<br>note: Không tìm được tài xế thay thế | Xử lý thành công; status 200; trip status = cancelled | High |
| TC-ADM-TRIP-007 | Theo dõi & xử lý chuyến đi bất thường | Xử lý chuyến gặp sự cố - hoàn tiền | Chuyến đã thanh toán nhưng gặp sự cố | 1. Gửi request POST /operator/trips/{tripId}/resolve | action: refund<br>note: Hoàn tiền do lỗi hệ thống | Xử lý thành công; status 200 | High |
| TC-ADM-TRIP-008 | Theo dõi & xử lý chuyến đi bất thường | Xử lý chuyến với tripId không tồn tại | tripId không có trong hệ thống | 1. Gửi request POST /operator/trips/{tripId}/resolve với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-TRIP-009 | Theo dõi & xử lý chuyến đi bất thường | Xử lý chuyến với action ngoài enum cho phép | Chuyến tồn tại | 1. Gửi request POST /operator/trips/{tripId}/resolve với action không hợp lệ | action: delete_trip | Từ chối; status 400; lỗi giá trị action không hợp lệ | Medium |
| TC-ADM-TRIP-010 | Theo dõi & xử lý chuyến đi bất thường | Nhân viên operator_basic cố thực hiện hoàn tiền (refund) | Đăng nhập bằng vai trò operator_basic | 1. Gửi request POST /operator/trips/{tripId}/resolve với action=refund bằng operator_basic | action: refund<br>note: test | Từ chối; status 403; thao tác tài chính yêu cầu quyền cao hơn | High |
| TC-ADM-TRIP-011 | Theo dõi & xử lý chuyến đi bất thường | Tham số from > to (khoảng thời gian không hợp lệ) | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/trips?from=2026-02-01&to=2026-01-01 | from > to | Từ chối; status 400; lỗi khoảng thời gian không hợp lệ | Medium |
| TC-ADM-TRIP-012 | Theo dõi & xử lý chuyến đi bất thường | Tham số from = to (biên đúng 1 thời điểm) | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/trips?from=2026-01-15T00:00:00Z&to=2026-01-15T00:00:00Z | from = to | Trả về status 200; danh sách chuyến đúng thời điểm đó (có thể rỗng) | Low |
| TC-ADM-TRIP-013 | Theo dõi & xử lý chuyến đi bất thường | Xử lý chuyến thiếu note (bắt buộc) | Chuyến tồn tại | 1. Gửi request POST /operator/trips/{tripId}/resolve, bỏ trống note | action: cancel_trip<br>note: (rỗng) | Từ chối; status 400; lỗi thiếu note (required) | High |
| TC-ADM-TRIP-014 | Theo dõi & xử lý chuyến đi bất thường | Xử lý chuyến thiếu action (bắt buộc) | Chuyến tồn tại | 1. Gửi request POST /operator/trips/{tripId}/resolve, bỏ trống action | action: (rỗng)<br>note: test | Từ chối; status 400; lỗi thiếu action (required) | High |
| TC-ADM-TRIP-015 | Theo dõi & xử lý chuyến đi bất thường | status filter gửi giá trị ngoài enum TripStatus | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/trips?status=xyz | status: xyz | Từ chối; status 400; lỗi giá trị status không hợp lệ | Medium |
| TC-ADM-TRIP-016 | Theo dõi & xử lý chuyến đi bất thường | from/to sai định dạng ngày giờ | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/trips?from=31-01-2026 | from: 31-01-2026 | Từ chối; status 400; lỗi sai định dạng date-time | Medium |
| TC-ADM-TRIP-017 | Theo dõi & xử lý chuyến đi bất thường | tripId sai định dạng UUID | Nhân viên vận hành đã đăng nhập | 1. Gửi request POST /operator/trips/{tripId}/resolve với id không đúng định dạng | tripId: not-a-uuid | Từ chối; status 400 | Medium |

## Scenario 2: Tra cứu lịch sử giao dịch (FR17)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-ADM-TXN-001 | Tra cứu lịch sử giao dịch | Tra cứu giao dịch theo tripId | Giao dịch tồn tại cho tripId này | 1. Gửi request GET /operator/transactions?tripId={tripId} | tripId: (id hợp lệ) | Trả về status 200; danh sách giao dịch của chuyến đó | High |
| TC-ADM-TXN-002 | Tra cứu lịch sử giao dịch | Tra cứu giao dịch theo customerId | Khách hàng có nhiều giao dịch | 1. Gửi request GET /operator/transactions?customerId={customerId} | customerId: (id hợp lệ) | Trả về status 200; danh sách giao dịch của khách hàng đó | High |
| TC-ADM-TXN-003 | Tra cứu lịch sử giao dịch | Tra cứu giao dịch lọc theo status=success | Có giao dịch thành công trong hệ thống | 1. Gửi request GET /operator/transactions?status=success | status: success | Trả về status 200; chỉ gồm giao dịch thành công | Medium |
| TC-ADM-TXN-004 | Tra cứu lịch sử giao dịch | Tra cứu toàn bộ giao dịch (không lọc) | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/transactions | (không filter) | Trả về status 200; toàn bộ giao dịch trong hệ thống | Medium |
| TC-ADM-TXN-005 | Tra cứu lịch sử giao dịch | Tra cứu với tripId không tồn tại trong hệ thống | tripId không có giao dịch nào | 1. Gửi request GET /operator/transactions?tripId={tripId} với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 200; danh sách rỗng [] | Medium |
| TC-ADM-TXN-006 | Tra cứu lịch sử giao dịch | Tra cứu với customerId không tồn tại | customerId không có trong hệ thống | 1. Gửi request GET /operator/transactions?customerId={customerId} với id không tồn tại | customerId: 00000000-0000-0000-0000-000000000000 | Trả về status 200; danh sách rỗng [] | Medium |
| TC-ADM-TXN-007 | Tra cứu lịch sử giao dịch | Kết hợp đồng thời cả 3 tham số lọc | tripId, customerId, status đều hợp lệ và khớp nhau | 1. Gửi request GET /operator/transactions?tripId=...&customerId=...&status=success | tripId, customerId, status: success | Trả về status 200; kết quả thỏa mãn đồng thời cả 3 điều kiện | Low |
| TC-ADM-TXN-008 | Tra cứu lịch sử giao dịch | Không truyền filter nào | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/transactions không kèm query param | (không truyền gì) | Trả về status 200; trả về toàn bộ giao dịch | Low |
| TC-ADM-TXN-009 | Tra cứu lịch sử giao dịch | status filter gửi giá trị ngoài enum cho phép | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/transactions?status=refunded | status: refunded | Từ chối; status 400; lỗi giá trị status không hợp lệ | Medium |
| TC-ADM-TXN-010 | Tra cứu lịch sử giao dịch | tripId/customerId sai định dạng UUID | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/transactions?tripId=not-a-uuid | tripId: not-a-uuid | Từ chối; status 400 | Medium |

## Scenario 3: Xem báo cáo hoạt động (FR19)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-ADM-REPORT-001 | Xem báo cáo hoạt động | Xem báo cáo với khoảng thời gian hợp lệ | Có dữ liệu chuyến đi/doanh thu trong khoảng thời gian | 1. Gửi request GET /operator/reports?from=2026-01-01&to=2026-01-31 | from: 2026-01-01<br>to: 2026-01-31 | Trả về status 200; totalTrips, totalRevenue, completionRate, cancellationRate, topDrivers | High |
| TC-ADM-REPORT-002 | Xem báo cáo hoạt động | Xem báo cáo trong khoảng thời gian 1 ngày (from = to) | Có dữ liệu trong ngày đó | 1. Gửi request GET /operator/reports?from=2026-01-15&to=2026-01-15 | from = to = 2026-01-15 | Trả về status 200; số liệu đúng trong phạm vi 1 ngày | Medium |
| TC-ADM-REPORT-003 | Xem báo cáo hoạt động | Xem báo cáo với from > to | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/reports?from=2026-02-01&to=2026-01-01 | from > to | Từ chối; status 400; lỗi khoảng thời gian không hợp lệ | High |
| TC-ADM-REPORT-004 | Xem báo cáo hoạt động | Xem báo cáo cho khoảng thời gian chưa có dữ liệu | Không có chuyến nào trong khoảng thời gian được chọn | 1. Gửi request GET /operator/reports?from=2020-01-01&to=2020-01-31 | (khoảng thời gian không có dữ liệu) | Trả về status 200; totalTrips=0, totalRevenue=0, completionRate=0 | Medium |
| TC-ADM-REPORT-005 | Xem báo cáo hoạt động | Xem báo cáo với khoảng thời gian rất dài (1 năm) | Dữ liệu tích lũy cả năm | 1. Gửi request GET /operator/reports?from=2025-01-01&to=2025-12-31 | (khoảng 1 năm) | Trả về status 200; hệ thống xử lý được, không timeout | Low |
| TC-ADM-REPORT-006 | Xem báo cáo hoạt động | Thiếu tham số from (bắt buộc) | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/reports?to=2026-01-31, bỏ trống from | from: (rỗng) | Từ chối; status 400; lỗi thiếu from (required) | High |
| TC-ADM-REPORT-007 | Xem báo cáo hoạt động | Thiếu tham số to (bắt buộc) | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/reports?from=2026-01-01, bỏ trống to | to: (rỗng) | Từ chối; status 400; lỗi thiếu to (required) | High |
| TC-ADM-REPORT-008 | Xem báo cáo hoạt động | from/to sai định dạng ngày | Nhân viên vận hành đã đăng nhập | 1. Gửi request GET /operator/reports?from=01-01-2026&to=31-01-2026 | from, to: sai định dạng | Từ chối; status 400; lỗi sai định dạng date | Medium |
