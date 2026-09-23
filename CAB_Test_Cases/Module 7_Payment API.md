# Module 7 — Payment API — Test Cases (Draft)

> Nguồn tham chiếu: `7_payment_api.yaml` — `POST /internal/trips/{tripId}/calculate-fare`,
> `GET/POST /trips/{tripId}/payment`, `POST /trips/{tripId}/payment/retry`,
> `POST /webhooks/payments/callback` (FR09, FR10, FR11, Business Rule RL04/RL05/RL07, NFR08).

## Scenario 1: Tính cước chuyến đi (FR09, nội bộ)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-FARE-001 | Tính cước chuyến đi | Tính cước thành công ngay sau khi chuyến hoàn thành | Chuyến vừa chuyển trạng thái completed | 1. Gọi POST /internal/trips/{tripId}/calculate-fare | tripId: (chuyến vừa completed) | Trả về status 200; amount, currency, breakdown hợp lý | High |
| TC-FARE-002 | Tính cước chuyến đi | Tính cước cho chuyến chưa hoàn thành | Chuyến đang ở trạng thái in_progress | 1. Gọi POST /internal/trips/{tripId}/calculate-fare | tripId: (chuyến chưa completed) | Từ chối; status 400/409; lỗi chỉ tính cước sau khi chuyến hoàn thành (Business Rule RL05) | High |
| TC-FARE-003 | Tính cước chuyến đi | Tính cước cho tripId không tồn tại | tripId không có trong hệ thống | 1. Gọi POST /internal/trips/{tripId}/calculate-fare với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-FARE-004 | Tính cước chuyến đi | Gọi tính cước lặp lại cho chuyến đã được tính cước trước đó | Chuyến đã có fare được tính | 1. Gọi POST /internal/trips/{tripId}/calculate-fare lần thứ 2 | tripId: (chuyến đã có fare) | Trả về status 200; kết quả idempotent, giống lần tính đầu, không tính lại/nhân đôi | Medium |
| TC-FARE-005 | Tính cước chuyến đi | Tính cước cho chuyến có quãng đường/thời gian rất ngắn (gần 0) | Chuyến hoàn thành gần như ngay lập tức | 1. Gọi POST /internal/trips/{tripId}/calculate-fare | tripId: (chuyến quãng đường ~0km) | Trả về status 200; áp dụng mức cước tối thiểu, amount > 0 | Medium |
| TC-FARE-006 | Tính cước chuyến đi | Tính cước cho chuyến có quãng đường/thời gian rất lớn | Chuyến đi quãng đường dài bất thường | 1. Gọi POST /internal/trips/{tripId}/calculate-fare | tripId: (chuyến quãng đường >200km) | Trả về status 200; amount tính đúng, không lỗi tràn số | Low |
| TC-FARE-007 | Tính cước chuyến đi | Gọi API không có tripId trong path | Client gọi sai route | 1. Gọi POST /internal/trips//calculate-fare (thiếu id) | (tripId rỗng) | Trả về status 404 (route không hợp lệ) | Low |
| TC-FARE-008 | Tính cước chuyến đi | tripId sai định dạng UUID | Service đang hoạt động | 1. Gọi POST /internal/trips/{tripId}/calculate-fare với id không đúng định dạng | tripId: not-a-uuid | Trả về status 400 | Medium |

## Scenario 2: Thanh toán tiền mặt (FR10)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-PAY-CASH-001 | Thanh toán tiền mặt | Thanh toán tiền mặt cho chuyến đã hoàn thành và đã tính cước | Chuyến completed, đã có fare | 1. Gửi request POST /trips/{tripId}/payment | method: cash | Ghi nhận thành công; status 202; payment status = success | High |
| TC-PAY-CASH-002 | Thanh toán tiền mặt | Thanh toán tiền mặt cho chuyến chưa hoàn thành | Chuyến đang ở trạng thái in_progress | 1. Gửi request POST /trips/{tripId}/payment | method: cash | Từ chối; status 400; lỗi chuyến chưa hoàn thành/chưa có cước | High |
| TC-PAY-CASH-003 | Thanh toán tiền mặt | Thanh toán cho chuyến đã thanh toán thành công trước đó | Chuyến đã có payment status = success | 1. Gửi request POST /trips/{tripId}/payment lần 2 | method: cash | Từ chối; status 400; lỗi chuyến đã được thanh toán | High |
| TC-PAY-CASH-004 | Thanh toán tiền mặt | Thanh toán cho tripId không tồn tại | tripId không có trong hệ thống | 1. Gửi request POST /trips/{tripId}/payment với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000<br>method: cash | Trả về status 404 | Medium |
| TC-PAY-CASH-005 | Thanh toán tiền mặt | Khách hàng cố thanh toán hộ cho chuyến của khách hàng khác | Đăng nhập bằng khách hàng A, thao tác trên tripId của khách hàng B | 1. Dùng token khách hàng A\n2. Gửi request POST /trips/{tripId}/payment với tripId của khách hàng B | method: cash | Từ chối truy cập; status 403 | High |
| TC-PAY-CASH-006 | Thanh toán tiền mặt | Gửi yêu cầu thanh toán ngay sau khi cước vừa được tính xong | Fare vừa được tính xong (race condition biên thời gian) | 1. Gọi calculate-fare\n2. Gửi ngay POST /trips/{tripId}/payment | method: cash | Ghi nhận thành công; status 202; không xảy ra lỗi do race condition | Medium |
| TC-PAY-CASH-007 | Thanh toán tiền mặt | Gửi request thiếu method (bắt buộc) | Chuyến đã hoàn thành, đã có fare | 1. Gửi request POST /trips/{tripId}/payment, bỏ trống method | method: (rỗng) | Từ chối; status 400; lỗi thiếu method (required) | High |
| TC-PAY-CASH-008 | Thanh toán tiền mặt | method gửi giá trị ngoài enum cho phép | Chuyến đã hoàn thành | 1. Gửi request POST /trips/{tripId}/payment với method không hợp lệ | method: bitcoin | Từ chối; status 400; lỗi giá trị method không hợp lệ | Medium |
| TC-PAY-CASH-009 | Thanh toán tiền mặt | tripId sai định dạng UUID | Khách hàng đã đăng nhập | 1. Gửi request POST /trips/{tripId}/payment với id không đúng định dạng | tripId: not-a-uuid | Từ chối; status 400 | Medium |

## Scenario 3: Thanh toán điện tử (FR10, NFR08)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-PAY-EWALLET-001 | Thanh toán điện tử | Thanh toán qua e_wallet với paymentToken hợp lệ | Chuyến completed, đã có fare, có token hợp lệ từ NCC thanh toán | 1. Gửi request POST /trips/{tripId}/payment | method: e_wallet<br>paymentToken: tok_valid_abc123 | Ghi nhận thành công; status 202; payment status = pending (chờ webhook xác nhận) | High |
| TC-PAY-EWALLET-002 | Thanh toán điện tử | Thanh toán qua credit_card với paymentToken hợp lệ | Chuyến completed, đã có fare | 1. Gửi request POST /trips/{tripId}/payment | method: credit_card<br>paymentToken: tok_valid_xyz789 | Ghi nhận thành công; status 202; payment status = pending | High |
| TC-PAY-EWALLET-003 | Thanh toán điện tử | Thanh toán với paymentToken không hợp lệ/hết hạn | Token đã hết hạn hoặc không tồn tại phía NCC thanh toán | 1. Gửi request POST /trips/{tripId}/payment với token hết hạn | method: e_wallet<br>paymentToken: tok_expired_999 | Từ chối; status 400; lỗi token thanh toán không hợp lệ | High |
| TC-PAY-EWALLET-004 | Thanh toán điện tử | Thanh toán cho chuyến chưa hoàn thành | Chuyến đang ở trạng thái in_progress | 1. Gửi request POST /trips/{tripId}/payment | method: e_wallet<br>paymentToken: tok_valid_abc123 | Từ chối; status 400; lỗi chuyến chưa hoàn thành | High |
| TC-PAY-EWALLET-005 | Thanh toán điện tử | Thanh toán cho tripId không tồn tại | tripId không có trong hệ thống | 1. Gửi request POST /trips/{tripId}/payment với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-PAY-EWALLET-006 | Thanh toán điện tử | Thanh toán khi chuyến đã thanh toán thành công trước đó | Chuyến đã có payment status = success | 1. Gửi request POST /trips/{tripId}/payment lần 2 | method: e_wallet<br>paymentToken: tok_valid_abc123 | Từ chối; status 400; lỗi chuyến đã được thanh toán | High |
| TC-PAY-EWALLET-007 | Thanh toán điện tử | paymentToken ở độ dài tối đa cho phép | Chuyến đã hoàn thành | 1. Gửi request POST /trips/{tripId}/payment với paymentToken dài tối đa quy định | paymentToken: (đúng độ dài tối đa) | Ghi nhận thành công; status 202 | Low |
| TC-PAY-EWALLET-008 | Thanh toán điện tử | paymentToken vượt quá độ dài tối đa cho phép | Chuyến đã hoàn thành | 1. Gửi request POST /trips/{tripId}/payment với paymentToken vượt độ dài | paymentToken: (vượt độ dài tối đa) | Từ chối; status 400; lỗi vượt quá độ dài cho phép | Medium |
| TC-PAY-EWALLET-009 | Thanh toán điện tử | Gửi thanh toán điện tử thiếu paymentToken | Chuyến đã hoàn thành | 1. Gửi request POST /trips/{tripId}/payment, bỏ trống paymentToken | method: e_wallet<br>paymentToken: (rỗng) | Từ chối; status 400; lỗi thiếu paymentToken khi thanh toán điện tử | High |
| TC-PAY-EWALLET-010 | Thanh toán điện tử | Để trống method | Chuyến đã hoàn thành | 1. Gửi request POST /trips/{tripId}/payment, bỏ trống method | method: (rỗng) | Từ chối; status 400; lỗi thiếu method (required) | High |
| TC-PAY-EWALLET-011 | Thanh toán điện tử | method gửi giá trị ngoài enum cho phép | Chuyến đã hoàn thành | 1. Gửi request POST /trips/{tripId}/payment với method không hợp lệ | method: paypal | Từ chối; status 400; lỗi giá trị method không hợp lệ | Medium |
| TC-PAY-EWALLET-012 | Thanh toán điện tử | paymentToken chứa dữ liệu nghi là số thẻ thật (vi phạm NFR08) | Chuyến đã hoàn thành | 1. Gửi request POST /trips/{tripId}/payment với paymentToken chứa chuỗi 16 số liên tiếp | paymentToken: 4111111111111111 | Từ chối; status 400; hệ thống phát hiện & chặn dữ liệu thẻ nhạy cảm gửi trực tiếp (NFR08) | High |

## Scenario 4: Xử lý thanh toán thất bại & thử lại (FR11, Business Rule RL07)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-PAY-RETRY-001 | Xử lý thanh toán thất bại & thử lại | Thử lại thanh toán sau khi giao dịch trước đó thất bại | Payment status = failed | 1. Gửi request POST /trips/{tripId}/payment/retry | tripId: (chuyến có payment failed) | Ghi nhận thành công; status 202; tạo giao dịch mới ở trạng thái pending | High |
| TC-PAY-RETRY-002 | Xử lý thanh toán thất bại & thử lại | Thử lại thanh toán cho chuyến đã thanh toán thành công trước đó | Payment status = success | 1. Gửi request POST /trips/{tripId}/payment/retry | tripId: (chuyến đã thanh toán thành công) | Từ chối; status 409; thông báo chuyến đã được thanh toán thành công trước đó | High |
| TC-PAY-RETRY-003 | Xử lý thanh toán thất bại & thử lại | Thử lại thanh toán cho tripId không tồn tại | tripId không có trong hệ thống | 1. Gửi request POST /trips/{tripId}/payment/retry với id không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-PAY-RETRY-004 | Xử lý thanh toán thất bại & thử lại | Khách hàng cố thử lại thanh toán cho chuyến của khách hàng khác | Đăng nhập bằng khách hàng A, thao tác trên tripId của khách hàng B | 1. Dùng token khách hàng A\n2. Gửi request POST /trips/{tripId}/payment/retry với tripId của khách hàng B | tripId: (chuyến của khách hàng B) | Từ chối truy cập; status 403 | High |
| TC-PAY-RETRY-005 | Xử lý thanh toán thất bại & thử lại | Thử lại thanh toán đúng số lần tối đa cho phép (giả định 3 lần) | Đã thử lại thất bại 2 lần trước đó | 1. Gửi request POST /trips/{tripId}/payment/retry lần thứ 3 | tripId: (chuyến đã retry 2 lần) | Ghi nhận thành công; status 202; vẫn còn trong giới hạn cho phép | Medium |
| TC-PAY-RETRY-006 | Xử lý thanh toán thất bại & thử lại | Thử lại thanh toán vượt quá số lần tối đa cho phép (lần thứ 4) | Đã thử lại thất bại 3 lần trước đó | 1. Gửi request POST /trips/{tripId}/payment/retry lần thứ 4 | tripId: (chuyến đã retry 3 lần) | Từ chối; thông báo đã vượt số lần thử lại tối đa cho phép | Medium |
| TC-PAY-RETRY-007 | Xử lý thanh toán thất bại & thử lại | Thử lại thanh toán khi chuyến chưa từng có giao dịch thanh toán nào | Chuyến chưa gọi payment lần nào | 1. Gửi request POST /trips/{tripId}/payment/retry | tripId: (chuyến chưa thanh toán lần nào) | Từ chối; lỗi chưa có giao dịch để thử lại | Medium |
| TC-PAY-RETRY-008 | Xử lý thanh toán thất bại & thử lại | tripId sai định dạng UUID | Khách hàng đã đăng nhập | 1. Gửi request POST /trips/{tripId}/payment/retry với id không đúng định dạng | tripId: not-a-uuid | Từ chối; status 400 | Medium |

## Scenario 5: Webhook nhận kết quả giao dịch từ nhà cung cấp thanh toán (FR11)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-PAY-WEBHOOK-001 | Webhook nhận kết quả giao dịch | Webhook báo giao dịch thành công với chữ ký hợp lệ | Có giao dịch đang ở trạng thái pending; chữ ký X-Payment-Signature hợp lệ | 1. Gửi request POST /webhooks/payments/callback | transactionReference: txn_001<br>tripId: (trip có payment pending)<br>status: success<br>amount: 85000 | Tiếp nhận thành công; status 200; payment status cập nhật thành success; kích hoạt thông báo khách hàng | High |
| TC-PAY-WEBHOOK-002 | Webhook nhận kết quả giao dịch | Webhook báo giao dịch thất bại với chữ ký hợp lệ | Có giao dịch đang ở trạng thái pending | 1. Gửi request POST /webhooks/payments/callback | transactionReference: txn_002<br>tripId: (trip có payment pending)<br>status: failed<br>failureReason: insufficient_funds | Tiếp nhận thành công; status 200; payment status cập nhật thành failed; kích hoạt thông báo khách hàng (RL07) | High |
| TC-PAY-WEBHOOK-003 | Webhook nhận kết quả giao dịch | Webhook với chữ ký không hợp lệ | Header X-Payment-Signature sai/giả mạo | 1. Gửi request POST /webhooks/payments/callback với chữ ký sai | X-Payment-Signature: invalid_sig | Từ chối; status 401; không cập nhật payment | High |
| TC-PAY-WEBHOOK-004 | Webhook nhận kết quả giao dịch | Webhook với tripId không tồn tại trong hệ thống | tripId không có trong hệ thống | 1. Gửi request POST /webhooks/payments/callback với tripId không tồn tại | tripId: 00000000-0000-0000-0000-000000000000 | Từ chối; status 400; không tìm thấy giao dịch tương ứng | Medium |
| TC-PAY-WEBHOOK-005 | Webhook nhận kết quả giao dịch | Webhook gửi trùng lặp cùng transactionReference đã xử lý | transactionReference đã được xử lý ở lần gọi trước | 1. Gửi lại request POST /webhooks/payments/callback với transactionReference đã xử lý | transactionReference: txn_001 (đã xử lý) | Tiếp nhận; status 200; xử lý idempotent, không cập nhật/thông báo trùng lặp | High |
| TC-PAY-WEBHOOK-006 | Webhook nhận kết quả giao dịch | Webhook báo giao dịch thành công với amount = 0 | Giao dịch áp dụng khuyến mãi 100% | 1. Gửi request POST /webhooks/payments/callback với amount = 0 | status: success<br>amount: 0 | Tiếp nhận thành công; status 200; hệ thống xử lý bình thường, không báo lỗi | Low |
| TC-PAY-WEBHOOK-007 | Webhook nhận kết quả giao dịch | Gửi webhook thiếu transactionReference | Chữ ký hợp lệ | 1. Gửi request POST /webhooks/payments/callback, bỏ trống transactionReference | transactionReference: (rỗng) | Từ chối; status 400; lỗi thiếu transactionReference (required) | High |
| TC-PAY-WEBHOOK-008 | Webhook nhận kết quả giao dịch | Gửi webhook thiếu tripId | Chữ ký hợp lệ | 1. Gửi request POST /webhooks/payments/callback, bỏ trống tripId | tripId: (rỗng) | Từ chối; status 400; lỗi thiếu tripId (required) | High |
| TC-PAY-WEBHOOK-009 | Webhook nhận kết quả giao dịch | Gửi webhook thiếu status | Chữ ký hợp lệ | 1. Gửi request POST /webhooks/payments/callback, bỏ trống status | status: (rỗng) | Từ chối; status 400; lỗi thiếu status (required) | High |
| TC-PAY-WEBHOOK-010 | Webhook nhận kết quả giao dịch | status gửi giá trị ngoài enum cho phép | Chữ ký hợp lệ | 1. Gửi request POST /webhooks/payments/callback với status không hợp lệ | status: pending | Từ chối; status 400; lỗi giá trị status không hợp lệ | Medium |
| TC-PAY-WEBHOOK-011 | Webhook nhận kết quả giao dịch | tripId sai định dạng UUID | Chữ ký hợp lệ | 1. Gửi request POST /webhooks/payments/callback với tripId không đúng định dạng | tripId: not-a-uuid | Từ chối; status 400 | Medium |
