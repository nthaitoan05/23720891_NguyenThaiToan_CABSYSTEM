# SOFTWARE REQUIREMENTS SPECIFICATION
## Dự án: CAB System — Nền tảng đặt xe (Công ty ABC)

---

# 1. Stakeholders

| Stakeholder | Vai trò |
|---|---|
| **Ban lãnh đạo Công ty ABC** | Định hướng mục tiêu kinh doanh, đưa ra kỳ vọng đối với hệ thống mới và theo dõi các chỉ số hoạt động (số lượng chuyến, doanh thu, tỷ lệ hoàn thành, tỷ lệ hủy, hiệu quả tài xế). |
| **Khách hàng** | Đăng ký tài khoản, đặt xe, theo dõi chuyến đi, xem lịch sử, thanh toán và đánh giá tài xế. |
| **Tài xế** | Đăng ký hoặc được vận hành tạo tài khoản, quản lý hồ sơ và phương tiện, nhận/từ chối chuyến, cập nhật trạng thái chuyến và vị trí. |
| **Nhân viên vận hành** | Quản lý khách hàng, tài xế, phương tiện, chuyến đi; theo dõi chuyến đang diễn ra; xử lý chuyến bị lỗi; tra cứu lịch sử giao dịch. Một số thao tác nhạy cảm chỉ dành cho nhân viên được phân quyền phù hợp. |
| **Nhà cung cấp dịch vụ thanh toán** | Xử lý thanh toán điện tử cho hệ thống; hệ thống CAB không lưu trực tiếp thông tin nhạy cảm của thẻ/tài khoản thanh toán. |
| **Nhà cung cấp dịch vụ thông báo** | Cung cấp kênh gửi thông báo đến khách hàng và tài xế; hệ thống cần dễ dàng bổ sung kênh thông báo mới trong tương lai. |

---

# 2. Stakeholder Matrix

Ma trận dưới đây đánh giá mức độ quan tâm (Interest) và mức độ ảnh hưởng (Power) của từng stakeholder đối với dự án, làm cơ sở xác định cách thức trao đổi và mức độ ưu tiên khi thu thập/xác nhận yêu cầu.

```mermaid
quadrantChart
    title Stakeholder Matrix - CAB System
    x-axis Low Interest --> High Interest
    y-axis Low Power --> High Power

    quadrant-1 Manage Closely
    quadrant-2 Keep Satisfied
    quadrant-3 Monitor
    quadrant-4 Keep Informed

    "Ban lãnh đạo": [0.80, 0.95]
    "Nhân viên vận hành": [0.90, 0.70]
    "Khách hàng": [0.95, 0.50]
    "Tài xế": [0.90, 0.45]
    "NCC thanh toán": [0.40, 0.65]
    "NCC thông báo": [0.35, 0.40]
```

| Stakeholder | Quadrant | Cách tiếp cận |
|---|---|---|
| Ban lãnh đạo | Manage Closely | Trao đổi thường xuyên, xác nhận mục tiêu kinh doanh và các chỉ số báo cáo. |
| Nhân viên vận hành | Manage Closely | Thu thập chi tiết nghiệp vụ vận hành hằng ngày, xác nhận quy trình xử lý ngoại lệ. |
| Khách hàng | Keep Informed | Thu thập kỳ vọng trải nghiệm sử dụng, thông báo tiến độ khi cần. |
| Tài xế | Keep Informed | Thu thập kỳ vọng về quy trình nhận chuyến và cập nhật trạng thái. |
| Nhà cung cấp thanh toán | Keep Satisfied | Xác nhận chuẩn tích hợp và yêu cầu bảo mật giao dịch. |
| Nhà cung cấp thông báo | Monitor | Xác nhận các kênh thông báo hỗ trợ và khả năng mở rộng. |

---

# 3. Business Goals

| ID | Business Goal | Mô tả |
|---|---|---|
| BG1 | Xây dựng nền tảng có khả năng mở rộng | Phục vụ số lượng lớn khách hàng và tài xế, dễ dàng bổ sung tính năng, dịch vụ và thành phần kỹ thuật trong tương lai mà không phải xây lại toàn bộ hệ thống. |
| BG2 | Tự động hóa việc tìm và phân công tài xế | Tự động xác định, ưu tiên tài xế phù hợp và gần khách hàng; tự động tìm tài xế khác khi tài xế được đề xuất không phản hồi hoặc từ chối. |
| BG3 | Nâng cao trải nghiệm khách hàng | Giúp khách hàng đặt xe thuận tiện, theo dõi chuyến đi theo thời gian thực, xem thông tin tài xế/ETA, lịch sử chuyến, số tiền phải trả và đánh giá tài xế. |
| BG4 | Nâng cao hiệu quả quản lý và vận hành | Cung cấp giao diện quản trị để nhân viên vận hành quản lý khách hàng, tài xế, phương tiện, chuyến đi và xử lý các trường hợp bất thường. |
| BG5 | Quản lý thanh toán và doanh thu hiệu quả | Hỗ trợ tính cước, thanh toán tiền mặt và điện tử qua nhà cung cấp bên ngoài, đảm bảo an toàn thông tin thanh toán. |
| BG6 | Đảm bảo hệ thống ổn định, an toàn và liên tục | Hệ thống hoạt động ổn định khi tải cao, các thành phần mở rộng độc lập, lỗi cục bộ không ảnh hưởng toàn hệ thống, dữ liệu được bảo vệ và kiểm soát truy cập. |
| BG7 | Hỗ trợ ra quyết định dựa trên dữ liệu | Cung cấp báo cáo về số lượng chuyến, doanh thu, tỷ lệ hoàn thành, tỷ lệ hủy và hiệu quả hoạt động của tài xế. |

---

# 4. MVP Modules

| STT | Module | Mô tả | Chức năng chính |
|---|---|---|---|
| 1 | **Quản lý tài khoản & xác thực** | Quản lý tài khoản và xác thực người dùng. | Đăng ký, đăng nhập, cập nhật thông tin cá nhân. |
| 2 | **Quản lý tài xế & phương tiện** | Quản lý hồ sơ tài xế, phương tiện và trạng thái hoạt động. | Đăng ký/tạo tài khoản tài xế, cập nhật hồ sơ và phương tiện, cập nhật trạng thái sẵn sàng nhận chuyến. |
| 3 | **Đặt xe** | Cho phép khách hàng tạo yêu cầu đặt xe. | Nhập điểm đón/điểm đến, chọn loại xe, gửi yêu cầu đặt xe. |
| 4 | **Tìm kiếm & phân công tài xế** | Tự động tìm tài xế phù hợp với yêu cầu của khách hàng. | Xác định tài xế theo vị trí, trạng thái sẵn sàng và tiêu chí vận hành; ưu tiên tài xế phù hợp và gần khách hàng; tìm tài xế khác khi bị từ chối hoặc không phản hồi. |
| 5 | **Quản lý chuyến đi** | Quản lý toàn bộ trạng thái chuyến từ lúc đặt đến khi hoàn thành. | Cập nhật trạng thái chuyến, cập nhật vị trí tài xế, theo dõi chuyến, hiển thị thông tin tài xế và thời gian dự kiến đến. |
| 6 | **Tính cước & thanh toán** | Tính số tiền khách hàng phải trả và xử lý thanh toán. | Tính cước, thanh toán tiền mặt, thanh toán điện tử, ghi nhận kết quả giao dịch, xử lý thanh toán thất bại. |
| 7 | **Thông báo** | Gửi thông tin cập nhật đến khách hàng và tài xế. | Thông báo các sự kiện của chuyến đi (tiếp nhận yêu cầu, tài xế nhận chuyến, đến điểm đón, hoàn thành, kết quả thanh toán) và chuyến mới/thay đổi cho tài xế. |
| 8 | **Lịch sử & đánh giá** | Cho phép khách hàng xem lại thông tin chuyến và đánh giá tài xế. | Xem lịch sử chuyến, xem số tiền phải trả, đánh giá tài xế sau khi hoàn thành chuyến. |
| 9 | **Quản lý vận hành** | Hỗ trợ nhân viên vận hành theo dõi và quản lý hoạt động hệ thống. | Quản lý khách hàng, tài xế, phương tiện, chuyến đi; xem chuyến đang diễn ra; kiểm tra trạng thái tài xế; xử lý chuyến bị lỗi; tra cứu lịch sử giao dịch; phân quyền quản trị. |
| 10 | **Báo cáo cơ bản** | Cung cấp dữ liệu phục vụ theo dõi hoạt động kinh doanh. | Báo cáo số lượng chuyến, doanh thu, tỷ lệ hoàn thành, tỷ lệ hủy và hiệu quả hoạt động của tài xế. |

---

# 5. Business Requirements

| ID | Business Requirement | Mô tả |
|---|---|---|
| BR01 | Xây dựng nền tảng đặt xe trực tuyến | Thay thế hệ thống hiện tại, cho phép khách hàng và tài xế thực hiện toàn bộ quy trình đặt và thực hiện chuyến trên cùng một nền tảng. |
| BR02 | Hỗ trợ số lượng lớn người dùng | Phục vụ số lượng lớn khách hàng và tài xế, có khả năng mở rộng khi nhu cầu sử dụng tăng. |
| BR03 | Tự động hóa việc tìm và phân công tài xế | Tự động tìm và ưu tiên tài xế phù hợp dựa trên vị trí, trạng thái sẵn sàng và tiêu chí vận hành; tiếp tục tìm tài xế khác khi tài xế không phản hồi hoặc từ chối chuyến. |
| BR04 | Quản lý toàn bộ quy trình chuyến xe | Theo dõi toàn bộ quy trình từ khi khách hàng tạo yêu cầu đến khi chuyến hoàn thành và được đánh giá. |
| BR05 | Cung cấp khả năng theo dõi chuyến đi | Cho phép khách hàng theo dõi trạng thái chuyến, thông tin tài xế, thời gian dự kiến đến, lịch sử chuyến và số tiền phải trả. |
| BR06 | Hỗ trợ tính cước và thanh toán | Tính số tiền khách hàng phải trả; hỗ trợ thanh toán tiền mặt và điện tử qua nhà cung cấp bên ngoài, không lưu trực tiếp thông tin thanh toán nhạy cảm. |
| BR07 | Quản lý thông báo | Thông báo cho khách hàng và tài xế về các sự kiện quan trọng trong quá trình đặt, thực hiện chuyến và kết quả thanh toán. |
| BR08 | Hỗ trợ quản lý và vận hành tập trung | Cung cấp giao diện quản trị để nhân viên vận hành quản lý khách hàng, tài xế, phương tiện, chuyến đi và xử lý các trường hợp bất thường. |
| BR09 | Cung cấp báo cáo hoạt động | Cung cấp dữ liệu và báo cáo về số lượng chuyến, doanh thu, tỷ lệ hoàn thành, tỷ lệ hủy và hiệu quả hoạt động của tài xế. |
| BR10 | Đảm bảo tính ổn định và khả năng mở rộng | Hoạt động ổn định khi nhu cầu tăng cao; các thành phần mở rộng độc lập; lỗi tại một thành phần (thanh toán/thông báo) không làm dừng toàn bộ hệ thống. |
| BR11 | Đảm bảo an toàn và bảo mật dữ liệu | Xác thực người dùng, kiểm soát quyền truy cập với chức năng quản trị, bảo vệ dữ liệu cá nhân/vị trí/giao dịch, lưu vết các thao tác quan trọng. |
| BR12 | Hỗ trợ phát triển và mở rộng trong tương lai | Kiến trúc linh hoạt để bổ sung loại dịch vụ, phương thức thanh toán, nhà cung cấp thông báo hoặc thay đổi thành phần kỹ thuật mà không phải xây lại toàn bộ hệ thống. |

**Liên kết Business Goals ↔ Business Requirements**

| BG | BR liên quan |
|---|---|
| BG1 | BR01, BR02, BR12 |
| BG2 | BR03 |
| BG3 | BR04, BR05, BR07 |
| BG4 | BR08 |
| BG5 | BR06 |
| BG6 | BR10, BR11 |
| BG7 | BR09 |

---

# 6. Business Process Modeling

## 6.1. Business Process Overview

Quy trình nghiệp vụ cốt lõi của hệ thống CAB — vòng đời một chuyến xe — gồm các bước:

**Tạo yêu cầu đặt xe → Tìm kiếm và phân công tài xế → Xác nhận chuyến → Thực hiện chuyến → Tính cước → Thanh toán → Hoàn tất chuyến → Đánh giá**

Đây là quy trình duy nhất được mô hình hóa chi tiết vì đây là quy trình trung tâm, xuyên suốt các module của hệ thống. Các nhóm chức năng còn lại (quản lý tài khoản, quản lý vận hành, báo cáo) là các chức năng hỗ trợ, không phải quy trình tuần tự nhiều bước.

## 6.2. Business Process Diagram

```mermaid
flowchart TD

    A([Start]) --> B[Khách hàng đăng nhập]
    B --> C[Nhập điểm đón, điểm đến và chọn loại xe]
    C --> D[Gửi yêu cầu đặt xe]

    D --> E[Hệ thống tiếp nhận yêu cầu]
    E --> F[Tìm tài xế phù hợp]

    F --> G{Có tài xế phù hợp?}

    G -- Không --> H[Thông báo không tìm được tài xế]
    H --> Z([End])

    G -- Có --> I[Gửi yêu cầu đến tài xế]
    I --> J{Tài xế chấp nhận?}

    J -- Không phản hồi / Từ chối --> K[Tìm tài xế phù hợp khác]
    K --> F

    J -- Có --> L[Thông báo khách hàng: tài xế đã nhận chuyến]
    L --> M[Tài xế di chuyển đến điểm đón]

    M --> N{Tài xế đã đến điểm đón?}
    N -- Chưa --> M
    N -- Rồi --> O[Cập nhật trạng thái: đã đến điểm đón]

    O --> P[Đón khách]
    P --> Q[Cập nhật trạng thái: đang di chuyển]
    Q --> R[Hoàn thành chuyến]

    R --> S[Tính cước]
    S --> T{Phương thức thanh toán}

    T -- Tiền mặt --> U[Khách hàng thanh toán tiền mặt]
    T -- Điện tử --> V[Thực hiện thanh toán qua nhà cung cấp]

    V --> W{Thanh toán thành công?}
    W -- Không --> X[Thông báo thanh toán thất bại, cho phép xử lý lại]
    X --> V
    W -- Có --> Y[Ghi nhận kết quả thanh toán]

    U --> Y
    Y --> AA[Thông báo hoàn thành chuyến]
    AA --> AB[Khách hàng đánh giá tài xế]
    AB --> AC([End])
```

## 6.3. Các bên tham gia trong Business Process

| Actor / Stakeholder | Vai trò trong quy trình |
|---|---|
| **Khách hàng** | Tạo yêu cầu đặt xe, theo dõi chuyến, thanh toán, đánh giá tài xế. |
| **Hệ thống CAB** | Tiếp nhận yêu cầu, tìm và phân công tài xế, quản lý trạng thái chuyến, tính cước, xử lý thanh toán, gửi thông báo. |
| **Tài xế** | Nhận/từ chối chuyến, di chuyển đến điểm đón, đón khách, cập nhật trạng thái, hoàn thành chuyến. |
| **Nhà cung cấp thanh toán** | Xử lý giao dịch thanh toán điện tử, trả về kết quả giao dịch. |
| **Nhân viên vận hành** | Theo dõi chuyến đang diễn ra, kiểm tra trạng thái tài xế, hỗ trợ xử lý chuyến bị lỗi (nằm ngoài luồng chính, can thiệp khi có sự cố). |

## 6.4. Business Process theo Business Requirement

| Business Requirement | Bước quy trình liên quan |
|---|---|
| BR01 – Nền tảng đặt xe trực tuyến | Toàn bộ quy trình đặt và thực hiện chuyến |
| BR03 – Tự động hóa tìm và phân công tài xế | Tìm tài xế → Kiểm tra phản hồi → Tìm tài xế khác khi cần |
| BR04 – Quản lý toàn bộ quy trình chuyến xe | Từ tạo yêu cầu đến hoàn thành chuyến |
| BR05 – Theo dõi chuyến đi | Cập nhật và thông báo trạng thái chuyến |
| BR06 – Tính cước và thanh toán | Tính cước → Thanh toán → Xử lý kết quả giao dịch |
| BR07 – Quản lý thông báo | Thông báo xuyên suốt các bước của quy trình |
| BR11 – Bảo mật dữ liệu | Đăng nhập/xác thực trước khi khách hàng tạo yêu cầu |

---

# 7. Functional Requirements

| ID | Module | Functional Requirement | Mô tả |
|---|---|---|---|
| FR01 | Quản lý tài khoản & xác thực | Đăng ký tài khoản khách hàng | Hệ thống cho phép khách hàng đăng ký tài khoản. |
| FR02 | Quản lý tài khoản & xác thực | Đăng ký/tạo tài khoản tài xế | Hệ thống cho phép tài xế tự đăng ký hoặc được nhân viên vận hành tạo tài khoản. |
| FR03 | Quản lý tài khoản & xác thực | Đăng nhập | Hệ thống cho phép khách hàng và tài xế đăng nhập trước khi sử dụng các chức năng yêu cầu tài khoản. |
| FR04 | Quản lý tài khoản & xác thực | Cập nhật thông tin cá nhân | Hệ thống cho phép khách hàng cập nhật thông tin cá nhân. |
| FR05 | Quản lý tài xế & phương tiện | Cập nhật hồ sơ, phương tiện và trạng thái hoạt động | Hệ thống cho phép tài xế cập nhật hồ sơ, thông tin phương tiện và trạng thái sẵn sàng nhận chuyến. |
| FR06 | Đặt xe | Nhập thông tin chuyến | Hệ thống cho phép khách hàng nhập điểm đón, điểm đến và chọn loại xe. |
| FR07 | Đặt xe | Tạo yêu cầu đặt xe | Hệ thống cho phép khách hàng gửi yêu cầu đặt xe. |
| FR08 | Đặt xe | Tiếp nhận yêu cầu | Hệ thống tiếp nhận và ghi nhận yêu cầu đặt xe. |
| FR09 | Tìm kiếm & phân công tài xế | Xác định tài xế phù hợp | Hệ thống xác định các tài xế phù hợp dựa trên vị trí, trạng thái sẵn sàng và tiêu chí vận hành. |
| FR10 | Tìm kiếm & phân công tài xế | Ưu tiên tài xế | Hệ thống ưu tiên tài xế phù hợp và gần khách hàng. |
| FR11 | Tìm kiếm & phân công tài xế | Gửi yêu cầu đến tài xế | Hệ thống gửi yêu cầu chuyến đến tài xế phù hợp. |
| FR12 | Tìm kiếm & phân công tài xế | Xử lý phản hồi tài xế | Hệ thống ghi nhận việc tài xế chấp nhận hoặc từ chối chuyến. |
| FR13 | Tìm kiếm & phân công tài xế | Tìm tài xế thay thế | Khi tài xế không phản hồi hoặc từ chối, hệ thống tiếp tục tìm tài xế khác mà không yêu cầu khách hàng tạo lại yêu cầu. |
| FR14 | Tìm kiếm & phân công tài xế | Thông báo không tìm được tài xế | Khi không tìm được tài xế phù hợp, hệ thống thông báo rõ ràng cho khách hàng. |
| FR15 | Quản lý chuyến đi | Cập nhật trạng thái chuyến | Hệ thống cho phép tài xế cập nhật trạng thái: đã đến điểm đón, đã đón khách, đang di chuyển, hoàn thành chuyến. |
| FR16 | Quản lý chuyến đi | Cập nhật vị trí tài xế | Hệ thống ghi nhận vị trí tài xế trong quá trình thực hiện chuyến để hỗ trợ tìm tài xế và dự kiến thời gian đến. |
| FR17 | Quản lý chuyến đi | Theo dõi chuyến | Hệ thống cho phép khách hàng theo dõi trạng thái hiện tại của chuyến. |
| FR18 | Quản lý chuyến đi | Hiển thị thông tin tài xế | Hệ thống hiển thị thông tin tài xế đã nhận chuyến và thời gian dự kiến tài xế đến. |
| FR19 | Tính cước & thanh toán | Tính tiền chuyến đi | Hệ thống xác định số tiền khách hàng phải trả dựa trên loại dịch vụ và thông tin chuyến đi. |
| FR20 | Tính cước & thanh toán | Thanh toán tiền mặt | Hệ thống hỗ trợ khách hàng thanh toán bằng tiền mặt. |
| FR21 | Tính cước & thanh toán | Thanh toán điện tử | Hệ thống hỗ trợ thanh toán điện tử thông qua nhà cung cấp thanh toán bên ngoài. |
| FR22 | Tính cước & thanh toán | Ghi nhận kết quả thanh toán | Hệ thống ghi nhận kết quả giao dịch thanh toán. |
| FR23 | Tính cước & thanh toán | Xử lý thanh toán thất bại | Khi thanh toán điện tử thất bại, hệ thống thông báo cho khách hàng và cho phép xử lý lại theo chính sách doanh nghiệp. |
| FR24 | Thông báo | Gửi thông báo cho khách hàng | Hệ thống gửi thông báo khi: yêu cầu được tiếp nhận, tài xế nhận chuyến, tài xế đến điểm đón, chuyến hoàn thành, thanh toán có kết quả. |
| FR25 | Thông báo | Gửi thông báo cho tài xế | Hệ thống gửi thông báo cho tài xế về chuyến mới hoặc thay đổi liên quan đến chuyến đang thực hiện. |
| FR26 | Lịch sử & đánh giá | Xem lịch sử chuyến | Hệ thống cho phép khách hàng xem lịch sử các chuyến đã thực hiện. |
| FR27 | Lịch sử & đánh giá | Xem số tiền phải trả | Hệ thống cho phép khách hàng xem số tiền phải trả cho từng chuyến. |
| FR28 | Lịch sử & đánh giá | Đánh giá tài xế | Hệ thống cho phép khách hàng đánh giá tài xế sau khi chuyến hoàn thành. |
| FR29 | Quản lý vận hành | Quản lý khách hàng | Nhân viên vận hành xem và quản lý thông tin khách hàng. |
| FR30 | Quản lý vận hành | Quản lý tài xế | Nhân viên vận hành xem và quản lý thông tin tài xế. |
| FR31 | Quản lý vận hành | Quản lý phương tiện | Nhân viên vận hành xem và quản lý thông tin phương tiện. |
| FR32 | Quản lý vận hành | Quản lý chuyến đi | Nhân viên vận hành xem và quản lý thông tin chuyến đi. |
| FR33 | Quản lý vận hành | Theo dõi chuyến đang diễn ra | Nhân viên vận hành xem danh sách các chuyến đang diễn ra và kiểm tra trạng thái tài xế liên quan. |
| FR34 | Quản lý vận hành | Xử lý chuyến bị lỗi | Nhân viên vận hành hỗ trợ xử lý các trường hợp chuyến gặp sự cố. |
| FR35 | Quản lý vận hành | Tra cứu lịch sử giao dịch | Nhân viên vận hành tra cứu lịch sử giao dịch của chuyến đi. |
| FR36 | Quản lý vận hành | Phân quyền quản trị | Hệ thống kiểm soát quyền truy cập để chỉ nhân viên được phân quyền phù hợp mới thực hiện được các thao tác quản trị nhạy cảm. |
| FR37 | Báo cáo cơ bản | Báo cáo hoạt động | Hệ thống cung cấp báo cáo về số lượng chuyến, doanh thu, tỷ lệ hoàn thành, tỷ lệ hủy và hiệu quả hoạt động của tài xế. |

---

# 8. Non-Functional Requirements

| ID | Category | Non-Functional Requirement | Mô tả |
|---|---|---|---|
| NFR01 | Performance | Đáp ứng tốt khi nhu cầu tăng cao | Hệ thống duy trì khả năng hoạt động ổn định khi số lượng khách hàng, tài xế và yêu cầu đặt xe tăng lên. |
| NFR02 | Scalability | Mở rộng độc lập theo thành phần | Các thành phần của hệ thống có thể mở rộng độc lập khi tải tăng, không cần mở rộng toàn bộ hệ thống. |
| NFR03 | Availability | Duy trì hoạt động khi một thành phần gặp lỗi | Lỗi tại chức năng thanh toán hoặc thông báo không được làm toàn bộ hệ thống đặt xe ngừng hoạt động. |
| NFR04 | Reliability | Hoạt động ổn định xuyên suốt quy trình | Hệ thống đảm bảo hoạt động ổn định trong quá trình đặt xe, tìm tài xế, thực hiện chuyến, thanh toán và thông báo. |
| NFR05 | Security | Xác thực người dùng | Khách hàng và tài xế phải được xác thực trước khi sử dụng các chức năng yêu cầu tài khoản. |
| NFR06 | Authorization | Kiểm soát quyền truy cập | Các chức năng quản trị phải được phân quyền để nhân viên không có quyền không thể thực hiện các thao tác nhạy cảm. |
| NFR07 | Data Security | Bảo vệ dữ liệu | Thông tin cá nhân, thông tin phương tiện, dữ liệu vị trí và dữ liệu giao dịch phải được bảo vệ. |
| NFR08 | Payment Security | Không lưu trực tiếp thông tin thanh toán nhạy cảm | Thông tin nhạy cảm của thẻ hoặc tài khoản thanh toán không được lưu trực tiếp trong hệ thống CAB. |
| NFR09 | Auditability | Lưu vết các thao tác quan trọng | Các thao tác quan trọng phải được ghi nhận để phục vụ kiểm tra khi có sự cố. |
| NFR10 | Maintainability | Triển khai chức năng từng phần | Các chức năng mới có thể được triển khai từng phần, hạn chế ảnh hưởng đến các chức năng đang hoạt động. |
| NFR11 | Extensibility | Linh hoạt mở rộng trong tương lai | Có thể bổ sung loại dịch vụ mới, phương thức thanh toán mới hoặc nhà cung cấp thông báo mới mà không phải xây dựng lại toàn bộ ứng dụng. |
| NFR12 | Modularity | Thay đổi thành phần kỹ thuật độc lập | Có thể thay đổi một số thành phần kỹ thuật, hạn chế ảnh hưởng đến toàn bộ hệ thống. |

---

# 9. Business Rules

Các quy tắc nghiệp vụ dưới đây là ràng buộc/điều kiện bắt buộc hệ thống phải tuân theo, được rút ra trực tiếp từ mô tả nghiệp vụ trong đề bài. Đây là cơ sở để thiết kế logic xử lý cho các Functional Requirement tương ứng.

| ID | Business Rule | Áp dụng cho | FR/NFR liên quan |
|---|---|---|---|
| RL01 | Hệ thống chỉ gửi yêu cầu chuyến cho tài xế đang ở trạng thái sẵn sàng (available) và phù hợp với vị trí/tiêu chí vận hành. | Tìm kiếm & phân công tài xế | FR09, FR10 |
| RL02 | Tại một thời điểm, hệ thống chỉ gửi yêu cầu chuyến đến một tài xế; nếu tài xế không phản hồi hoặc từ chối, hệ thống tự động chuyển sang tài xế phù hợp tiếp theo mà không yêu cầu khách hàng tạo lại yêu cầu. | Tìm kiếm & phân công tài xế | FR12, FR13 |
| RL03 | Khách hàng và tài xế phải được xác thực (đăng nhập) trước khi sử dụng bất kỳ chức năng nào yêu cầu tài khoản. | Toàn hệ thống | FR03, NFR05 |
| RL04 | Thông tin nhạy cảm của thẻ/tài khoản thanh toán không được lưu trực tiếp trong hệ thống CAB; giao dịch điện tử phải được xử lý qua nhà cung cấp thanh toán bên ngoài. | Tính cước & thanh toán | FR21, NFR08 |
| RL05 | Cước phí chỉ được xác định sau khi chuyến đi hoàn thành, dựa trên loại dịch vụ (loại xe) và thông tin chuyến đi. | Tính cước & thanh toán | FR19 |
| RL06 | Khách hàng chỉ được phép đánh giá tài xế sau khi chuyến đi đã hoàn thành. | Lịch sử & đánh giá | FR28 |
| RL07 | Khi thanh toán điện tử thất bại, hệ thống không được ghi nhận chuyến là đã thanh toán; phải thông báo cho khách hàng và cho phép xử lý lại theo chính sách doanh nghiệp. | Tính cước & thanh toán | FR22, FR23 |
| RL08 | Nhân viên vận hành chỉ được thực hiện thao tác quản trị nhạy cảm khi được phân quyền phù hợp với vai trò. | Quản lý vận hành | FR36, NFR06 |
| RL09 | Lỗi xảy ra ở module thanh toán hoặc thông báo không được làm gián đoạn quy trình đặt xe và thực hiện chuyến chính. | Toàn hệ thống | NFR03, NFR04 |
| RL10 | Mọi thao tác quan trọng liên quan đến tài khoản, chuyến đi, thanh toán và phân quyền quản trị phải được lưu vết (audit log) để phục vụ kiểm tra khi cần. | Toàn hệ thống | NFR09 |

---

# 10. Exception Cases & Open Questions

## 10.1. Các trường hợp ngoại lệ

| ID | Quy trình | Trường hợp ngoại lệ | Cách xử lý |
|---|---|---|---|
| EX01 | Tìm tài xế | Không tìm được tài xế phù hợp | Hệ thống thông báo rõ ràng cho khách hàng, không yêu cầu khách hàng tạo lại yêu cầu. |
| EX02 | Tìm tài xế | Tài xế được đề xuất không phản hồi | Hệ thống tiếp tục tìm tài xế phù hợp khác. |
| EX03 | Tìm tài xế | Tài xế từ chối chuyến | Hệ thống tiếp tục tìm tài xế phù hợp khác mà không yêu cầu khách hàng tạo lại yêu cầu. |
| EX04 | Thanh toán | Thanh toán điện tử thất bại | Hệ thống thông báo cho khách hàng và cho phép xử lý lại theo chính sách của doanh nghiệp. |
| EX05 | Chuyến đi | Chuyến đi xảy ra lỗi | Nhân viên vận hành kiểm tra và hỗ trợ xử lý trường hợp chuyến bị lỗi. |
| EX06 | Hệ thống | Lỗi ở chức năng thanh toán hoặc thông báo | Lỗi của một thành phần không được làm toàn bộ hệ thống đặt xe ngừng hoạt động. |
| EX07 | Bảo mật | Người dùng chưa được xác thực | Hệ thống không cho phép khách hàng hoặc tài xế sử dụng chức năng yêu cầu tài khoản khi chưa xác thực. |
| EX08 | Quản trị | Nhân viên không đủ quyền thực hiện thao tác nhạy cảm | Hệ thống kiểm soát quyền truy cập và ngăn thao tác không được phép. |

## 10.2. Những điểm còn chưa rõ cần xác nhận với khách hàng

| ID | Chủ đề | Điểm chưa rõ | Câu hỏi cần xác nhận |
|---|---|---|---|
| OQ01 | Tính cước | Cách tính tiền chuyến chưa được chốt. | Cước được tính dựa trên những yếu tố nào (quãng đường, thời gian, loại xe, phụ phí...)? |
| OQ02 | Ưu tiên tài xế | Tiêu chí ưu tiên tài xế chưa được xác định đầy đủ. | Hệ thống ưu tiên tài xế dựa trên khoảng cách, thời gian chờ, trạng thái hoạt động hay tiêu chí nào khác? |
| OQ03 | Phản hồi tài xế | Chưa xác định thời gian tài xế phải phản hồi yêu cầu chuyến. | Tài xế có bao nhiêu thời gian để chấp nhận/từ chối trước khi hệ thống chuyển sang tài xế khác? |
| OQ04 | Hủy chuyến | Chính sách hủy chuyến chưa được chốt. | Ai được phép hủy chuyến, ở thời điểm nào và có tính phí hủy hay không? |
| OQ05 | Mất kết nối mạng | Chưa xác định cách xử lý khi khách hàng hoặc tài xế mất kết nối. | Hệ thống xử lý trạng thái chuyến và cập nhật dữ liệu như thế nào khi mất kết nối mạng? |
| OQ06 | Lưu trữ dữ liệu | Chưa xác định thời gian lưu trữ dữ liệu. | Dữ liệu khách hàng, chuyến đi, vị trí và giao dịch được lưu trong bao lâu? |
| OQ07 | Thanh toán thất bại | Chính sách xử lý lại khi thanh toán điện tử thất bại chưa được chốt. | Khách hàng được phép thử thanh toán lại tối đa bao nhiêu lần, trong khoảng thời gian nào? |
| OQ08 | Tìm tài xế | Chưa xác định khi nào hệ thống kết luận là không tìm được tài xế. | Hệ thống tìm trong bao lâu hoặc thử tối đa bao nhiêu tài xế trước khi thông báo thất bại? |
| OQ09 | Vị trí tài xế | Chưa xác định tần suất cập nhật vị trí. | Vị trí tài xế được cập nhật với tần suất bao nhiêu và trong những trạng thái nào của chuyến? |
| OQ10 | Phân quyền quản trị | Chưa xác định chi tiết các vai trò và quyền quản trị. | Có những vai trò quản trị nào và mỗi vai trò được phép thực hiện những chức năng nào? |

---

# 11. ERD — CAB System MVP

> ERD dưới đây là mô hình dữ liệu đề xuất dựa trên các yêu cầu nghiệp vụ và chức năng đã xác định ở trên.

```mermaid
erDiagram

    USER {
        int user_id PK
        string username
        string password
        string role
        string status
        datetime created_at
    }

    CUSTOMER {
        int customer_id PK
        int user_id FK
        string full_name
        string phone
        string email
        string address
    }

    DRIVER {
        int driver_id PK
        int user_id FK
        string full_name
        string phone
        string license_number
        string status
        boolean available
        decimal latitude
        decimal longitude
    }

    VEHICLE {
        int vehicle_id PK
        int driver_id FK
        string license_plate
        string vehicle_type
        string brand
        string model
        string status
    }

    TRIP {
        int trip_id PK
        int customer_id FK
        int driver_id FK
        int vehicle_id FK
        string pickup_location
        string destination
        string trip_status
        datetime request_time
        datetime start_time
        datetime end_time
        decimal fare
    }

    PAYMENT {
        int payment_id PK
        int trip_id FK
        string payment_method
        decimal amount
        string payment_status
        string transaction_reference
        datetime payment_time
    }

    RATING {
        int rating_id PK
        int trip_id FK
        int customer_id FK
        int driver_id FK
        int rating
        string comment
        datetime created_at
    }

    NOTIFICATION {
        int notification_id PK
        int user_id FK
        int trip_id FK
        string notification_type
        string channel
        string message
        boolean is_read
        datetime created_at
    }

    USER ||--o| CUSTOMER : "has"
    USER ||--o| DRIVER : "has"

    DRIVER ||--o{ VEHICLE : "owns"

    CUSTOMER ||--o{ TRIP : "books"
    DRIVER ||--o{ TRIP : "accepts"
    VEHICLE ||--o{ TRIP : "used_for"

    TRIP ||--o| PAYMENT : "has"

    TRIP ||--o| RATING : "receives"
    CUSTOMER ||--o{ RATING : "gives"
    DRIVER ||--o{ RATING : "receives"

    USER ||--o{ NOTIFICATION : "receives"
    TRIP ||--o{ NOTIFICATION : "generates"
```

## Main Entities

| Entity | Vai trò |
|---|---|
| **USER** | Thông tin tài khoản và xác thực chung cho khách hàng và tài xế. |
| **CUSTOMER** | Thông tin khách hàng sử dụng dịch vụ đặt xe. |
| **DRIVER** | Thông tin tài xế, trạng thái hoạt động và vị trí hiện tại. |
| **VEHICLE** | Thông tin phương tiện được tài xế sử dụng. |
| **TRIP** | Thông tin yêu cầu và quá trình thực hiện chuyến xe. |
| **PAYMENT** | Thông tin thanh toán của từng chuyến đi. |
| **RATING** | Đánh giá của khách hàng dành cho tài xế sau chuyến. |
| **NOTIFICATION** | Các thông báo gửi đến khách hàng hoặc tài xế, theo kênh gửi (channel) để hỗ trợ mở rộng thêm kênh mới trong tương lai. |

---

# 12. Use Case Diagram — CAB System MVP

```mermaid
flowchart LR

    Customer["Khách hàng"]
    Driver["Tài xế"]
    Operator["Nhân viên vận hành"]
    Payment["Nhà cung cấp thanh toán"]
    Notification["Nhà cung cấp thông báo"]

    subgraph CAB["CAB System"]

        UC01(["Đăng ký tài khoản (KH)"])
        UC02(["Đăng ký/tạo tài khoản tài xế"])
        UC03(["Đăng nhập"])
        UC04(["Cập nhật thông tin cá nhân"])
        UC05(["Quản lý hồ sơ & phương tiện tài xế"])

        UC06(["Đặt xe"])
        UC07(["Tìm & phân công tài xế"])
        UC08(["Chấp nhận / Từ chối chuyến"])
        UC09(["Cập nhật trạng thái chuyến"])
        UC10(["Theo dõi chuyến đi"])

        UC11(["Tính cước & thanh toán"])
        UC12(["Xem lịch sử & số tiền phải trả"])
        UC13(["Đánh giá tài xế"])
        UC14(["Nhận thông báo"])

        UC15(["Quản lý khách hàng"])
        UC16(["Quản lý tài xế"])
        UC17(["Quản lý phương tiện"])
        UC18(["Quản lý chuyến đi"])
        UC19(["Theo dõi chuyến đang diễn ra"])
        UC20(["Xử lý chuyến bị lỗi"])
        UC21(["Tra cứu lịch sử giao dịch"])
        UC22(["Phân quyền quản trị"])
        UC23(["Báo cáo hoạt động"])
    end

    Customer --- UC01
    Customer --- UC03
    Customer --- UC04
    Customer --- UC06
    Customer --- UC10
    Customer --- UC12
    Customer --- UC13

    Driver --- UC02
    Driver --- UC03
    Driver --- UC05
    Driver --- UC08
    Driver --- UC09

    UC06 -.->|include| UC07
    UC07 -.->|include| UC14
    UC08 -.->|include| UC14
    UC09 -.->|include| UC14
    UC09 -.->|include| UC11
    UC11 -.->|include| UC14

    UC11 --- Payment
    UC14 --- Notification

    Operator --- UC15
    Operator --- UC16
    Operator --- UC17
    Operator --- UC18
    Operator --- UC19
    Operator --- UC20
    Operator --- UC21
    Operator --- UC22
    Operator --- UC23
```

---

# 13. Acceptance Criteria

| ID | Functional Requirement | Acceptance Criteria |
|---|---|---|
| AC01 | FR01 – Đăng ký tài khoản khách hàng | Khách hàng cung cấp đầy đủ thông tin bắt buộc thì tài khoản được tạo thành công; nếu thiếu/không hợp lệ, hệ thống báo lỗi và không tạo tài khoản. |
| AC02 | FR02 – Đăng ký/tạo tài khoản tài xế | Tài xế tự đăng ký hoặc được nhân viên vận hành tạo tài khoản thành công khi thông tin hợp lệ. |
| AC03 | FR03 – Đăng nhập | Người dùng nhập đúng thông tin thì đăng nhập thành công; nếu sai, hệ thống báo lỗi và không cho truy cập chức năng yêu cầu tài khoản. |
| AC04 | FR04 – Cập nhật thông tin cá nhân | Khách hàng có thể chỉnh sửa và lưu lại thông tin cá nhân; hệ thống lưu thay đổi thành công. |
| AC05 | FR05 – Cập nhật hồ sơ/phương tiện/trạng thái tài xế | Tài xế có thể cập nhật hồ sơ, phương tiện và trạng thái sẵn sàng; hệ thống ghi nhận thay đổi ngay sau khi lưu. |
| AC06 | FR06 – Nhập thông tin chuyến | Khách hàng nhập được điểm đón, điểm đến và chọn loại xe trước khi gửi yêu cầu. |
| AC07 | FR07 – Tạo yêu cầu đặt xe | Khi đủ thông tin chuyến, khách hàng gửi yêu cầu và hệ thống tạo yêu cầu thành công. |
| AC08 | FR08 – Tiếp nhận yêu cầu | Sau khi yêu cầu được tạo, hệ thống ghi nhận và chuyển sang bước tìm tài xế. |
| AC09 | FR09 – Xác định tài xế phù hợp | Hệ thống xác định được danh sách tài xế phù hợp dựa trên vị trí, trạng thái sẵn sàng và tiêu chí vận hành đã cấu hình. |
| AC10 | FR10 – Ưu tiên tài xế | Hệ thống sắp xếp/ưu tiên tài xế phù hợp và gần khách hàng theo tiêu chí vận hành. |
| AC11 | FR11 – Gửi yêu cầu đến tài xế | Khi tìm được tài xế phù hợp, hệ thống gửi yêu cầu chuyến đến tài xế đó. |
| AC12 | FR12 – Xử lý phản hồi tài xế | Hệ thống ghi nhận chính xác việc tài xế chấp nhận hoặc từ chối yêu cầu. |
| AC13 | FR13 – Tìm tài xế thay thế | Khi tài xế không phản hồi hoặc từ chối, hệ thống tự động tìm tài xế khác mà không yêu cầu khách hàng tạo lại yêu cầu. |
| AC14 | FR14 – Thông báo không tìm được tài xế | Khi không còn tài xế phù hợp, hệ thống thông báo rõ ràng cho khách hàng. |
| AC15 | FR15 – Cập nhật trạng thái chuyến | Tài xế cập nhật được các trạng thái: đã đến điểm đón, đã đón khách, đang di chuyển, hoàn thành chuyến, theo đúng thứ tự. |
| AC16 | FR16 – Cập nhật vị trí tài xế | Hệ thống ghi nhận vị trí tài xế trong quá trình thực hiện chuyến. |
| AC17 | FR17 – Theo dõi chuyến | Khách hàng xem được trạng thái hiện tại của chuyến trong suốt quá trình thực hiện. |
| AC18 | FR18 – Hiển thị thông tin tài xế | Sau khi tài xế nhận chuyến, khách hàng xem được thông tin tài xế và thời gian dự kiến đến. |
| AC19 | FR19 – Tính tiền chuyến đi | Sau khi chuyến hoàn thành, hệ thống xác định được số tiền khách hàng phải trả dựa trên loại dịch vụ và thông tin chuyến. |
| AC20 | FR20 – Thanh toán tiền mặt | Khách hàng chọn phương thức tiền mặt, hệ thống ghi nhận phương thức thanh toán của chuyến. |
| AC21 | FR21 – Thanh toán điện tử | Khi khách hàng chọn thanh toán điện tử, hệ thống gửi yêu cầu đến nhà cung cấp thanh toán và nhận kết quả giao dịch. |
| AC22 | FR22 – Ghi nhận kết quả thanh toán | Hệ thống ghi nhận trạng thái giao dịch đúng theo kết quả trả về từ phương thức thanh toán tương ứng. |
| AC23 | FR23 – Xử lý thanh toán thất bại | Khi thanh toán điện tử thất bại, hệ thống thông báo cho khách hàng và cho phép xử lý lại theo chính sách doanh nghiệp. |
| AC24 | FR24 – Thông báo khách hàng | Khách hàng nhận được thông báo tại từng mốc: yêu cầu tiếp nhận, tài xế nhận chuyến, tài xế đến điểm đón, hoàn thành chuyến, kết quả thanh toán. |
| AC25 | FR25 – Thông báo tài xế | Tài xế nhận được thông báo khi có chuyến mới hoặc khi có thay đổi liên quan đến chuyến đang thực hiện. |
| AC26 | FR26 – Xem lịch sử chuyến | Khách hàng xem được danh sách các chuyến đã thực hiện cùng thông tin liên quan. |
| AC27 | FR27 – Xem số tiền phải trả | Khách hàng xem được số tiền phải trả sau khi hệ thống hoàn tất tính cước. |
| AC28 | FR28 – Đánh giá tài xế | Sau khi chuyến hoàn thành, khách hàng thực hiện được đánh giá tài xế. |
| AC29 | FR29 – Quản lý khách hàng | Nhân viên vận hành xem/cập nhật được thông tin khách hàng theo quyền được cấp. |
| AC30 | FR30 – Quản lý tài xế | Nhân viên vận hành xem/cập nhật được thông tin tài xế theo quyền được cấp. |
| AC31 | FR31 – Quản lý phương tiện | Nhân viên vận hành xem/cập nhật được thông tin phương tiện theo quyền được cấp. |
| AC32 | FR32 – Quản lý chuyến đi | Nhân viên vận hành xem được thông tin và trạng thái của các chuyến đi. |
| AC33 | FR33 – Theo dõi chuyến đang diễn ra | Nhân viên vận hành xem được danh sách chuyến đang diễn ra và trạng thái tài xế liên quan theo thời gian thực. |
| AC34 | FR34 – Xử lý chuyến bị lỗi | Nhân viên vận hành thao tác được để hỗ trợ xử lý chuyến gặp sự cố. |
| AC35 | FR35 – Tra cứu lịch sử giao dịch | Nhân viên vận hành tra cứu được lịch sử giao dịch theo chuyến/khách hàng. |
| AC36 | FR36 – Phân quyền quản trị | Nhân viên không có quyền phù hợp không thực hiện được thao tác quản trị nhạy cảm; hệ thống từ chối và ghi nhận thao tác. |
| AC37 | FR37 – Báo cáo hoạt động | Hệ thống xuất được báo cáo số lượng chuyến, doanh thu, tỷ lệ hoàn thành, tỷ lệ hủy và hiệu quả tài xế theo khoảng thời gian yêu cầu. |

---

# 14. Requirements Traceability Matrix

| BG | BR | BPM/Module | FR | UC | AC |
|---|---|---|---|---|---|
| BG1 | BR01, BR11 | Quản lý tài khoản & xác thực | FR01 – Đăng ký tài khoản KH | UC01 | AC01 |
| BG1 | BR01, BR11 | Quản lý tài khoản & xác thực | FR02 – Đăng ký/tạo tài khoản tài xế | UC02 | AC02 |
| BG1 | BR11 | Quản lý tài khoản & xác thực | FR03 – Đăng nhập | UC03 | AC03 |
| BG3 | BR01 | Quản lý tài khoản & xác thực | FR04 – Cập nhật thông tin cá nhân | UC04 | AC04 |
| BG1 | BR01 | Quản lý tài xế & phương tiện | FR05 – Cập nhật hồ sơ/phương tiện/trạng thái tài xế | UC05 | AC05 |
| BG3 | BR01, BR04 | Đặt xe | FR06 – Nhập thông tin chuyến | UC06 | AC06 |
| BG3 | BR04 | Đặt xe | FR07 – Tạo yêu cầu đặt xe | UC06 | AC07 |
| BG3 | BR04 | Đặt xe | FR08 – Tiếp nhận yêu cầu | UC06 | AC08 |
| BG2 | BR03 | Tìm & phân công tài xế | FR09 – Xác định tài xế phù hợp | UC07 | AC09 |
| BG2 | BR03 | Tìm & phân công tài xế | FR10 – Ưu tiên tài xế | UC07 | AC10 |
| BG2 | BR03 | Tìm & phân công tài xế | FR11 – Gửi yêu cầu đến tài xế | UC07 | AC11 |
| BG2 | BR03 | Tìm & phân công tài xế | FR12 – Xử lý phản hồi tài xế | UC08 | AC12 |
| BG2 | BR03 | Tìm & phân công tài xế | FR13 – Tìm tài xế thay thế | UC07 | AC13 |
| BG2 | BR03 | Tìm & phân công tài xế | FR14 – Thông báo không tìm được tài xế | UC14 | AC14 |
| BG3 | BR04, BR05 | Thực hiện chuyến | FR15 – Cập nhật trạng thái chuyến | UC09 | AC15 |
| BG2 | BR03 | Thực hiện chuyến | FR16 – Cập nhật vị trí tài xế | UC09 | AC16 |
| BG3 | BR05 | Thực hiện chuyến | FR17 – Theo dõi chuyến | UC10 | AC17 |
| BG3 | BR05 | Thực hiện chuyến | FR18 – Hiển thị thông tin tài xế | UC10 | AC18 |
| BG5 | BR06 | Tính cước & thanh toán | FR19 – Tính tiền chuyến đi | UC11 | AC19 |
| BG5 | BR06 | Tính cước & thanh toán | FR20 – Thanh toán tiền mặt | UC11 | AC20 |
| BG5 | BR06 | Tính cước & thanh toán | FR21 – Thanh toán điện tử | UC11 | AC21 |
| BG5 | BR06 | Tính cước & thanh toán | FR22 – Ghi nhận kết quả thanh toán | UC11 | AC22 |
| BG5 | BR06 | Tính cước & thanh toán | FR23 – Xử lý thanh toán thất bại | UC11 | AC23 |
| BG3 | BR07 | Thông báo | FR24 – Thông báo khách hàng | UC14 | AC24 |
| BG3 | BR07 | Thông báo | FR25 – Thông báo tài xế | UC14 | AC25 |
| BG3 | BR05 | Hoàn tất chuyến | FR26 – Xem lịch sử chuyến | UC12 | AC26 |
| BG3 | BR05 | Hoàn tất chuyến | FR27 – Xem số tiền phải trả | UC12 | AC27 |
| BG3 | BR04 | Đánh giá | FR28 – Đánh giá tài xế | UC13 | AC28 |
| BG4 | BR08 | Quản lý vận hành | FR29 – Quản lý khách hàng | UC15 | AC29 |
| BG4 | BR08 | Quản lý vận hành | FR30 – Quản lý tài xế | UC16 | AC30 |
| BG4 | BR08 | Quản lý vận hành | FR31 – Quản lý phương tiện | UC17 | AC31 |
| BG4 | BR08 | Quản lý vận hành | FR32 – Quản lý chuyến đi | UC18 | AC32 |
| BG4 | BR08 | Quản lý vận hành | FR33 – Theo dõi chuyến đang diễn ra | UC19 | AC33 |
| BG4 | BR08 | Quản lý vận hành | FR34 – Xử lý chuyến bị lỗi | UC20 | AC34 |
| BG4 | BR08 | Quản lý vận hành | FR35 – Tra cứu lịch sử giao dịch | UC21 | AC35 |
| BG6 | BR11 | Quản lý vận hành | FR36 – Phân quyền quản trị | UC22 | AC36 |
| BG7 | BR09 | Báo cáo cơ bản | FR37 – Báo cáo hoạt động | UC23 | AC37 |
