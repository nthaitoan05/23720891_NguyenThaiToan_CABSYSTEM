# Module 8 — Rating & History API — Test Cases (Draft)

> Nguồn tham chiếu: `8_rating_history_api.yaml` — `GET /customers/{customerId}/trips`,
> `GET /drivers/{driverId}/trips`, `GET /trips/{tripId}/fare`,
> `GET/POST/PUT/DELETE /trips/{tripId}/rating` (FR13, FR14, Business Rule RL06).

## Scenario 1: Xem lịch sử chuyến & số tiền phải trả (FR13)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-HISTORY-001 | Xem lịch sử chuyến & số tiền phải trả | Xem lịch sử chuyến của khách hàng (không lọc) | Khách hàng đã có ít nhất 1 chuyến | 1. Gửi request GET /customers/{customerId}/trips | customerId: (id khách hàng) | Trả về status 200; danh sách toàn bộ chuyến của khách hàng | High |
| TC-HISTORY-002 | Xem lịch sử chuyến & số tiền phải trả | Xem lịch sử chuyến của tài xế | Tài xế đã có ít nhất 1 chuyến | 1. Gửi request GET /drivers/{driverId}/trips | driverId: (id tài xế) | Trả về status 200; danh sách toàn bộ chuyến của tài xế | High |
| TC-HISTORY-003 | Xem lịch sử chuyến & số tiền phải trả | Lọc lịch sử theo trạng thái completed | Khách hàng có nhiều chuyến ở các trạng thái khác nhau | 1. Gửi request GET /customers/{customerId}/trips?status=completed | status: completed | Trả về status 200; chỉ gồm các chuyến có status = completed | Medium |
| TC-HISTORY-004 | Xem lịch sử chuyến & số tiền phải trả | Xem lịch sử có phân trang | Khách hàng có hơn 20 chuyến | 1. Gửi request GET /customers/{customerId}/trips?page=2&pageSize=10 | page: 2<br>pageSize: 10 | Trả về status 200; đúng 10 kết quả thuộc trang 2 | Medium |
| TC-HISTORY-005 | Xem lịch sử chuyến & số tiền phải trả | Xem số tiền phải trả của chuyến đã tính cước | Chuyến đã completed và đã có fare | 1. Gửi request GET /trips/{tripId}/fare | tripId: (chuyến đã có fare) | Trả về status 200; amount, currency, breakdown | High |
| TC-HISTORY-006 | Xem lịch sử chuyến & số tiền phải trả | Khách hàng A xem lịch sử chuyến của khách hàng B | Đăng nhập bằng khách hàng A, thao tác trên customerId của khách hàng B | 1. Dùng token khách hàng A\n2. Gửi request GET /customers/{customerId}/trips với id khách hàng B | customerId: (id khách hàng B) | Từ chối truy cập; status 403 | High |
| TC-HISTORY-007 | Xem lịch sử chuyến & số tiền phải trả | Tài xế A xem lịch sử chuyến của tài xế B | Đăng nhập bằng tài xế A, thao tác trên driverId của tài xế B | 1. Dùng token tài xế A\n2. Gửi request GET /drivers/{driverId}/trips với id tài xế B | driverId: (id tài xế B) | Từ chối truy cập; status 403 | High |
| TC-HISTORY-008 | Xem lịch sử chuyến & số tiền phải trả | Xem số tiền phải trả cho tripId không tồn tại | tripId không có trong hệ thống | 1. Gửi request GET /trips/{tripId}/fare với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-HISTORY-009 | Xem lịch sử chuyến & số tiền phải trả | Xem số tiền phải trả cho chuyến chưa được tính cước | Chuyến chưa completed, chưa có fare | 1. Gửi request GET /trips/{tripId}/fare | tripId: (chuyến chưa có fare) | Trả về status 404; chưa có dữ liệu cước phí | Medium |
| TC-HISTORY-010 | Xem lịch sử chuyến & số tiền phải trả | pageSize ở giá trị tối đa cho phép (giả định 100) | Khách hàng có nhiều chuyến | 1. Gửi request GET /customers/{customerId}/trips?pageSize=100 | pageSize: 100 | Trả về status 200 | Low |
| TC-HISTORY-011 | Xem lịch sử chuyến & số tiền phải trả | pageSize vượt quá giá trị tối đa cho phép | Khách hàng có nhiều chuyến | 1. Gửi request GET /customers/{customerId}/trips?pageSize=1000 | pageSize: 1000 | Từ chối; status 400; lỗi pageSize vượt giới hạn cho phép | Medium |
| TC-HISTORY-012 | Xem lịch sử chuyến & số tiền phải trả | Khách hàng chưa có chuyến nào | Tài khoản mới, chưa từng đặt chuyến | 1. Gửi request GET /customers/{customerId}/trips | customerId: (khách hàng mới) | Trả về status 200; danh sách rỗng [] | Medium |
| TC-HISTORY-013 | Xem lịch sử chuyến & số tiền phải trả | Không truyền tham số status (mặc định lấy tất cả) | Khách hàng có nhiều chuyến ở nhiều trạng thái | 1. Gửi request GET /customers/{customerId}/trips không kèm status | (không truyền status) | Trả về status 200; trả về chuyến ở mọi trạng thái | Low |
| TC-HISTORY-014 | Xem lịch sử chuyến & số tiền phải trả | Không truyền page/pageSize (dùng giá trị mặc định) | Khách hàng có nhiều chuyến | 1. Gửi request GET /customers/{customerId}/trips không kèm page/pageSize | (không truyền page/pageSize) | Trả về status 200; áp dụng page=1, pageSize=20 mặc định | Low |
| TC-HISTORY-015 | Xem lịch sử chuyến & số tiền phải trả | status filter gửi giá trị ngoài enum TripStatus | Khách hàng đã đăng nhập | 1. Gửi request GET /customers/{customerId}/trips?status=xyz | status: xyz | Từ chối; status 400; lỗi giá trị status không hợp lệ | Medium |
| TC-HISTORY-016 | Xem lịch sử chuyến & số tiền phải trả | page/pageSize gửi giá trị không phải số | Khách hàng đã đăng nhập | 1. Gửi request GET /customers/{customerId}/trips?page=abc | page: abc | Từ chối; status 400; lỗi sai kiểu dữ liệu | Medium |
| TC-HISTORY-017 | Xem lịch sử chuyến & số tiền phải trả | tripId sai định dạng UUID khi xem fare | Khách hàng đã đăng nhập | 1. Gửi request GET /trips/{tripId}/fare với id không đúng định dạng | tripId: not-a-uuid | Từ chối; status 400 | Medium |

## Scenario 2: Đánh giá tài xế (FR14, Business Rule RL06)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-RATING-001 | Đánh giá tài xế | Gửi đánh giá lần đầu cho chuyến đã hoàn thành (kèm comment) | Chuyến ở trạng thái completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating | score: 5<br>comment: Tài xế thân thiện, đúng giờ | Ghi nhận thành công; status 201 | High |
| TC-RATING-002 | Đánh giá tài xế | Gửi đánh giá không kèm comment (comment không bắt buộc) | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating | score: 4 | Ghi nhận thành công; status 201 | Medium |
| TC-RATING-003 | Đánh giá tài xế | Xem đánh giá đã gửi | Chuyến đã có đánh giá | 1. Gửi request GET /trips/{tripId}/rating | tripId: (chuyến đã có rating) | Trả về status 200; đúng score, comment đã gửi | High |
| TC-RATING-004 | Đánh giá tài xế | Chỉnh sửa đánh giá đã gửi | Chuyến đã có đánh giá trước đó | 1. Gửi request PUT /trips/{tripId}/rating | score: 3<br>comment: Cập nhật lại đánh giá | Cập nhật thành công; status 200; dữ liệu khớp với dữ liệu vừa gửi | Medium |
| TC-RATING-005 | Đánh giá tài xế | Xóa đánh giá đã gửi | Chuyến đã có đánh giá | 1. Gửi request DELETE /trips/{tripId}/rating | tripId: (chuyến đã có rating) | Xóa thành công; status 204 | Medium |
| TC-RATING-006 | Đánh giá tài xế | Gửi đánh giá cho chuyến chưa hoàn thành | Chuyến đang ở trạng thái in_progress | 1. Gửi request POST /trips/{tripId}/rating | score: 5 | Từ chối; status 409; chuyến chưa hoàn thành, không thể đánh giá (RL06) | High |
| TC-RATING-007 | Đánh giá tài xế | Xem đánh giá cho chuyến chưa có đánh giá nào | Chuyến completed nhưng chưa được đánh giá | 1. Gửi request GET /trips/{tripId}/rating | tripId: (chuyến chưa có rating) | Trả về status 404 | Medium |
| TC-RATING-008 | Đánh giá tài xế | Chỉnh sửa đánh giá cho chuyến chưa có đánh giá nào | Chuyến chưa từng được đánh giá | 1. Gửi request PUT /trips/{tripId}/rating | score: 4 | Trả về status 404 | Medium |
| TC-RATING-009 | Đánh giá tài xế | Xóa đánh giá cho chuyến chưa có đánh giá nào | Chuyến chưa từng được đánh giá | 1. Gửi request DELETE /trips/{tripId}/rating | tripId: (chuyến chưa có rating) | Trả về status 404 | Medium |
| TC-RATING-010 | Đánh giá tài xế | Khách hàng cố đánh giá cho chuyến của khách hàng khác | Đăng nhập bằng khách hàng A, thao tác trên tripId của khách hàng B | 1. Dùng token khách hàng A\n2. Gửi request POST /trips/{tripId}/rating với tripId của khách hàng B | tripId: (chuyến của khách hàng B) | Từ chối truy cập; status 403 | High |
| TC-RATING-011 | Đánh giá tài xế | score ở biên giá trị hợp lệ tối thiểu (1) | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating | score: 1 | Ghi nhận thành công; status 201 | Low |
| TC-RATING-012 | Đánh giá tài xế | score ở biên giá trị hợp lệ tối đa (5) | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating | score: 5 | Ghi nhận thành công; status 201 | Low |
| TC-RATING-013 | Đánh giá tài xế | score dưới giá trị tối thiểu cho phép (0) | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating | score: 0 | Từ chối; status 400; lỗi score phải từ 1 đến 5 | High |
| TC-RATING-014 | Đánh giá tài xế | score vượt quá giá trị tối đa cho phép (6) | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating | score: 6 | Từ chối; status 400; lỗi score phải từ 1 đến 5 | High |
| TC-RATING-015 | Đánh giá tài xế | Gửi đánh giá thiếu score (bắt buộc) | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating, bỏ trống score | score: (rỗng) | Từ chối; status 400; lỗi thiếu score (required) | High |
| TC-RATING-016 | Đánh giá tài xế | Gửi đánh giá với comment rỗng (hợp lệ vì không bắt buộc) | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating với comment rỗng | score: 4<br>comment: "" | Ghi nhận thành công; status 201 | Low |
| TC-RATING-017 | Đánh giá tài xế | score gửi dạng số thập phân thay vì số nguyên | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating | score: 3.5 | Từ chối; status 400; lỗi score phải là số nguyên | Medium |
| TC-RATING-018 | Đánh giá tài xế | score gửi dạng chuỗi thay vì số | Chuyến completed, chưa có đánh giá | 1. Gửi request POST /trips/{tripId}/rating | score: "five" | Từ chối; status 400; lỗi sai kiểu dữ liệu | Medium |
| TC-RATING-019 | Đánh giá tài xế | tripId sai định dạng UUID | Khách hàng đã đăng nhập | 1. Gửi request GET/POST/PUT/DELETE /trips/{tripId}/rating với id không đúng định dạng | tripId: not-a-uuid | Từ chối; status 400 | Medium |
