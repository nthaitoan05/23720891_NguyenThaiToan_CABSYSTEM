# Module 10 — Admin Staff & Roles API — Test Cases (Draft)

> Nguồn tham chiếu: `10_admin_staff_api.yaml` — CRUD `/operator/staff`, `PUT /operator/staff/{staffId}/roles`
> (FR18, NFR06, Business Rule RL08). Chỉ vai trò `operator_admin` được tạo/xóa/phân quyền nhân viên.

## Scenario: Phân quyền quản trị (FR18)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| TC-ADM-ROLE-001 | Phân quyền quản trị | operator_admin xem danh sách nhân viên vận hành | Đăng nhập bằng vai trò operator_admin | 1. Gửi request GET /operator/staff | (token operator_admin) | Trả về status 200; danh sách nhân viên vận hành | High |
| TC-ADM-ROLE-002 | Phân quyền quản trị | operator_admin xem chi tiết một nhân viên | Nhân viên tồn tại | 1. Gửi request GET /operator/staff/{staffId} | staffId: (id hợp lệ) | Trả về status 200; đầy đủ thông tin nhân viên | High |
| TC-ADM-ROLE-003 | Phân quyền quản trị | operator_admin tạo tài khoản nhân viên mới hợp lệ | username chưa tồn tại | 1. Gửi request POST /operator/staff | fullName: Pham Thi Van<br>username: van.pham<br>password: Password@123<br>roles: [operator_basic] | Tạo thành công; status 201 | High |
| TC-ADM-ROLE-004 | Phân quyền quản trị | operator_admin phân quyền lại cho nhân viên | Nhân viên tồn tại, đang có role operator_basic | 1. Gửi request PUT /operator/staff/{staffId}/roles | roles: [operator_basic, finance_viewer] | Cập nhật phân quyền thành công; status 200 | High |
| TC-ADM-ROLE-005 | Phân quyền quản trị | operator_admin vô hiệu hóa tài khoản nhân viên | Nhân viên đang active | 1. Gửi request DELETE /operator/staff/{staffId} | staffId: (id hợp lệ) | Vô hiệu hóa thành công; status 204 | High |
| TC-ADM-ROLE-006 | Phân quyền quản trị | operator_basic cố tạo tài khoản nhân viên mới | Đăng nhập bằng vai trò operator_basic | 1. Gửi request POST /operator/staff bằng tài khoản operator_basic | fullName: Test<br>username: test.user<br>password: Password@123<br>roles: [operator_basic] | Từ chối; status 403 | High |
| TC-ADM-ROLE-007 | Phân quyền quản trị | operator_basic cố phân quyền cho nhân viên khác | Đăng nhập bằng vai trò operator_basic | 1. Gửi request PUT /operator/staff/{staffId}/roles bằng tài khoản operator_basic | roles: [operator_admin] | Từ chối; status 403 | High |
| TC-ADM-ROLE-008 | Phân quyền quản trị | operator_basic cố vô hiệu hóa tài khoản nhân viên | Đăng nhập bằng vai trò operator_basic | 1. Gửi request DELETE /operator/staff/{staffId} bằng tài khoản operator_basic | staffId: (id hợp lệ) | Từ chối; status 403 | High |
| TC-ADM-ROLE-009 | Phân quyền quản trị | Xem chi tiết nhân viên với staffId không tồn tại | staffId không có trong hệ thống | 1. Gửi request GET /operator/staff/{staffId} với id không tồn tại | staffId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-ROLE-010 | Phân quyền quản trị | Vô hiệu hóa nhân viên với staffId không tồn tại | staffId không có trong hệ thống | 1. Gửi request DELETE /operator/staff/{staffId} với id không tồn tại (bằng operator_admin) | staffId: 00000000-0000-0000-0000-000000000000 | Trả về status 404 | Medium |
| TC-ADM-ROLE-011 | Phân quyền quản trị | Tạo tài khoản nhân viên với username đã tồn tại | username van.pham đã tồn tại | 1. Gửi request POST /operator/staff với username trùng | username: van.pham | Từ chối; status 400; lỗi username đã tồn tại | High |
| TC-ADM-ROLE-012 | Phân quyền quản trị | Tạo tài khoản với đầy đủ cả 3 vai trò cùng lúc | operator_admin đã đăng nhập | 1. Gửi request POST /operator/staff với roles gồm cả 3 giá trị | roles: [operator_basic, operator_admin, finance_viewer] | Tạo thành công; status 201; roles lưu đủ 3 giá trị | Medium |
| TC-ADM-ROLE-013 | Phân quyền quản trị | username ở độ dài tối đa cho phép (giả định 50 ký tự) | operator_admin đã đăng nhập | 1. Gửi request POST /operator/staff với username dài 50 ký tự | username: (đúng độ dài tối đa) | Tạo thành công; status 201 | Low |
| TC-ADM-ROLE-014 | Phân quyền quản trị | username vượt quá độ dài tối đa cho phép | operator_admin đã đăng nhập | 1. Gửi request POST /operator/staff với username dài 51 ký tự | username: (vượt độ dài tối đa) | Từ chối; status 400; lỗi vượt quá độ dài cho phép | Medium |
| TC-ADM-ROLE-015 | Phân quyền quản trị | Tạo tài khoản thiếu password | operator_admin đã đăng nhập | 1. Gửi request POST /operator/staff, bỏ trống password | password: (rỗng) | Từ chối; status 400; lỗi thiếu password (required) | High |
| TC-ADM-ROLE-016 | Phân quyền quản trị | Tạo tài khoản thiếu roles (mảng rỗng) | operator_admin đã đăng nhập | 1. Gửi request POST /operator/staff với roles = [] | roles: [] | Từ chối; status 400; lỗi thiếu roles (required, phải có ít nhất 1 vai trò) | High |
| TC-ADM-ROLE-017 | Phân quyền quản trị | Phân quyền với roles là mảng rỗng | Nhân viên tồn tại | 1. Gửi request PUT /operator/staff/{staffId}/roles với roles = [] | roles: [] | Từ chối; status 400; không cho phép nhân viên không có quyền nào | High |
| TC-ADM-ROLE-018 | Phân quyền quản trị | roles chứa giá trị ngoài enum cho phép | operator_admin đã đăng nhập | 1. Gửi request POST/PUT roles với giá trị không hợp lệ | roles: [super_admin] | Từ chối; status 400; lỗi giá trị role không hợp lệ | Medium |
| TC-ADM-ROLE-019 | Phân quyền quản trị | staffId sai định dạng UUID | operator_admin đã đăng nhập | 1. Gửi request GET/PUT/DELETE /operator/staff/{staffId} với id không đúng định dạng | staffId: not-a-uuid | Từ chối; status 400 | Medium |
