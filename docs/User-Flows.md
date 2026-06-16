# User Flows - Car Rental SaaS Platform

## Mục lục
1. [Tenant Onboarding Flow](#1-tenant-onboarding-flow)
2. [Tenant Daily Operations](#2-tenant-daily-operations)
3. [Customer Booking Flow](#3-customer-booking-flow)
4. [Vehicle Transfer Flow](#4-vehicle-transfer-flow)
5. [Multi-tenant Isolation](#5-multi-tenant-isolation)

---

## 1. Tenant Onboarding Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    TENANT ONBOARDING FLOW                    │
└─────────────────────────────────────────────────────────────┘

1. ĐĂNG KÝ
 ├── Doanh nghiệp truy cập website → Landing page
 │   ├── Click "Đăng ký dùng thử" / "Bắt đầu miễn phí"
 │   ├── Điền thông tin:
 │   │   ├── Tên công ty
 │   │   ├── Email công ty
 │   │   ├── Số điện thoại
 │   │   ├── Password
 │   │   └── Quy mô fleet (5-20 xe, 20-50 xe, 50+ xe)
 │   └── Xác minh email → Active account

2. CHỌN GÓI DỊCH VỤ
 ├── Free: 1 chi nhánh, 10 xe, 50 bookings/tháng
 ├── Basic: 3 chi nhánh, 50 xe, 200 bookings/tháng
 ├── Pro: 10 chi nhánh, 200 xe, unlimited
 └── Enterprise: Custom - liên hệ sales
 └── Thanh toán subscription (nếu chọn Basic/Pro)

3. SETUP INITIAL
 ├── Tạo Central Branch (kho xe trung tâm)
 ├── Thêm các chi nhánh (nếu có)
 ├── Thêm loại xe (xe 4 chỗ, xe 7 chỗ...)
 ├── Setup bảng giá cơ bản
 └── Thêm nhân viên (admin, staff)

4. SẴN SÀNG
 └── Dashboard trống → Hướng dẫn thêm xe đầu tiên
```

---

## 2. Tenant Daily Operations

### 2.1 Branch Manager Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    BRANCH MANAGER DAILY FLOW                 │
└─────────────────────────────────────────────────────────────┘

1. Đăng nhập → Dashboard chi nhánh
   └── Xem: Xe đang trống, Xe đang thuê, Lịch book hôm nay

2. QUẢN LÝ XE
 ├── Thêm xe mới (biển số, dòng xe, màu...)
 ├── Cập nhật trạng thái xe
 │   ├── available (sẵn sàng)
 │   ├── maintenance (bảo dưỡng)
 │   └── transferred (đang chuyển)
 └── Xem lịch sử xe

3. TIẾP NHẬN BOOKING
 ├── Xem danh sách booking mới
 ├── Xác nhận booking (kiểm tra xe available)
 ├── Nếu không có xe → Request transfer từ Central
 └── Gửi xác nhận cho khách (SMS/Email)

4. GIAO XE
 ├── Check-in xe khi khách đến
 ├── Kiểm tra tình trạng xe (ghi nhận tình trạng xe)
 ├── Cập nhật booking status = 'in_progress'
 └── In phiếu giao xe

5. NHẬN XE
 ├── Check-out xe khi khách trả
 ├── Kiểm tra tình trạng xe
 ├── Tính tiền (dựa trên ngày thực tế)
 ├── Thu tiền (tiền mặt/chuyển khoản)
 └── Cập nhật booking status = 'completed'

6. BÁO CÁO
 ├── Xem doanh thu ngày/tháng
 ├── Xem lịch sử giao dịch
 └── Export báo cáo Excel/PDF
```

### 2.2 Central Manager Flow

```
CENTRAL MANAGER:
────────────────────────────
1. Dashboard trung tâm
   └── Xem tổng quan: Tất cả xe, xe đang ở chi nhánh nào

2. QUẢN LÝ TRANSFER
 ├── Xem request từ chi nhánh
 ├── Phê duyệt/từ chối transfer
 ├── Cập nhật trạng thái chuyển xe
 └── Track lịch sử chuyển xe

3. PHÂN BỔ XE
 ├── Xem fleet map (xe ở đâu)
 ├── Điều phối xe giữa các chi nhánh
 └── Lên kế hoạch bảo dưỡng định kỳ
```

### 2.3 SaaS Platform Operations Flow

```
┌─────────────────────────────────────────────────────────────┐
│                SAAS PLATFORM OPERATIONS FLOW                │
└─────────────────────────────────────────────────────────────┘

1. ĐĂNG NHẬP → SUPER ADMIN DASHBOARD
   └── Xem tổng quan: Số lượng Tenants, Doanh thu hệ thống, Trạng thái tài nguyên

2. QUẢN LÝ TENANTS (NHÀ XE)
 ├── Xem danh sách tất cả các tenants
 ├── Phê duyệt/kích hoạt tenant mới đăng ký
 ├── Khóa/Tạm ngưng tenant vi phạm điều khoản hoặc nợ phí
 └── Xem chi tiết thông tin, dung lượng sử dụng của từng tenant

3. QUẢN LÝ SUBSCRIPTION PLANS & BILLING
 ├── Cấu hình các gói dịch vụ (FREE, BASIC, PRO, ENTERPRISE)
 ├── Duyệt các yêu cầu thanh toán / gia hạn gói dịch vụ từ các tenants
 └── Tra cứu lịch sử thanh toán toàn hệ thống

4. CẤU HÌNH HỆ THỐNG & GIÁM SÁT
 ├── Cấu hình cổng thanh toán dùng chung (VNPAY, Momo, Bank Transfer...)
 ├── Giám sát logs hệ thống (System logs, Audit logs bảo mật)
 └── Cấu hình template thông báo (SMS, Email OTP...)
```

---

## 3. Customer Booking Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    CUSTOMER BOOKING FLOW                     │
└─────────────────────────────────────────────────────────────┘

1. TÌM XE
 ├── Truy cập website/App của tenant
 ├── Chọn chi nhánh gần nhất (Google Maps)
 ├── Chọn ngày nhận xe & ngày trả xe
 ├── Chọn loại xe (xe 4 chỗ, xe 7 chỗ...)
 └── Xem danh sách xe available + giá

2. ĐẶT XE
 ├── Chọn xe cụ thể (nếu có nhiều xe cùng loại)
 ├── Xem chi tiết giá:
 │   ├── Giá ngày thường: 500k
 │   ├── Giá cuối tuần: 600k
 │   └── Giá ngày lễ: 750k
 ├── Điền thông tin khách hàng:
 │   ├── Họ tên
 │   ├── SĐT
 │   ├── Email
 │   └── CCCD/GPLX
 └── Chọn phương thức thanh toán

3. THANH TOÁN CỌC
 ├── Tiền mặt → Nhận phiếu cọc
 ├── Chuyển khoản → Cung cấp STK
 ├── Ví điện tử → Quét QR
 └── Xác nhận đã thanh toán

4. NHẬN XÁC NHẬN
 ├── Email: Chi tiết booking + QR code
 ├── SMS: "Mã booking của bạn: CR-2024-XXXX"
 └── Thông tin chi nhánh + Google Maps link

5. ĐẾN CHI NHÁNH
 ├── Show QR code / Mã booking
 ├── Nhân viên kiểm tra thông tin
 ├── Kiểm tra xe cùng khách (ghi nhận tình trạng xe)
 ├── Ký hợp đồng thuê xe
 └── Nhận xe + keys

6. SỬ DỤNG XE
 └── Tự do sử dụng trong thời gian thuê

7. TRẢ XE
 ├── Đến chi nhánh đúng hẹn
 ├── Nhân viên kiểm tra xe
 ├── Tính tiền:
 │   ├── Giá thuê × Số ngày thực tế
 │   ├── Phí phạt (nếu trả muộn)
 │   └── Phí hư hỏng (nếu có)
 ├── Thanh toán số tiền còn lại
 └── Nhận hóa đơn + phiếu trả xe
```

---

## 4. Vehicle Transfer Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    VEHICLE TRANSFER FLOW                    │
└─────────────────────────────────────────────────────────────┘

1. BRANCH A NHẬN BOOKING NHƯNG KHÔNG CÓ XE
 │
 ▼
2. BRANCH A CHECK FLEET SIZE
 ├── Xe available >= threshold → Không cần transfer
 └── Xe available < threshold → Request transfer từ Central
 │
 ▼
3. CENTRAL NHẬN REQUEST
 ├── Check xe available tại kho
 ├── If có xe → Approve transfer
 └── If không có xe → Reject, notify Branch A
 │
 ▼
4. XE CHUYỂN TỪ CENTRAL → BRANCH A
 ├── Update vehicle.status = 'transferred'
 ├── Update vehicle.current_branch_id = Branch A
 ├── Create transfer record với timestamp
 └── Notify Branch A khi xe đến
 │
 ▼
5. BRANCH A XÁC NHẬN NHẬN XE
 ├── Update vehicle.status = 'available'
 └── Confirm booking với customer
```

---

## 5. Multi-tenant Isolation

### 5.1 Tenant Data Isolation

```
┌─────────────────────────────────────────────────────────────┐
│                    MULTI-TENANT ISOLATION                   │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    SHARED INFRASTRUCTURE                    │
│         (Same Database, Same APIs, Same Server)             │
└─────────────────────────────────────────────────────────────┘
 │
         ┌─────────────────────┼─────────────────────┐
         │                     │                     │
         ▼                     ▼                     ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│    TENANT A     │  │    TENANT B     │  │    TENANT C     │
│  "RentCar VN"   │  │  "Xe Điện Sài Gòn"│  │ "Taxi Nha Trang"│
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ Website:        │  │ Website:        │  │ Website:        │
│ rentcar-vn.com  │  │ xediensg.com │  │ taxinhathang.com│
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ App:            │  │ App:            │  │ App:            │
│ RentCar VN      │  │ Xe Điện Sài Gòn │  │ Taxi Nha Trang  │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ Customer A1     │  │ Customer B1     │  │ Customer C1     │
│ Customer A2     │  │ Customer B2     │  │ Customer C2     │
│ Customer A3     │  │ Customer B3     │  │ Customer C3     │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ Branch A1       │  │ Branch B1       │  │ Branch C1       │
│ Branch A2       │  │ Branch B2       │  │ Branch C2       │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ Xe: ABC-123     │  │ Xe: XYZ-789     │  │ Xe: QQQ-111     │
│ Xe: DEF-456     │  │ Xe: UVW-654     │  │ Xe: RRR-222     │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

### 5.2 Data Access Flow

```
Customer của Tenant A truy cập:
────────────────────────────────
rentcar-vn.com
    │
    ▼
Gọi API: GET /api/vehicles
    │ Header: X-Tenant-ID: A (hoặc subdomain)
    ▼
Backend tự động filter tenant_id = 'A'
    │
    ▼
Query: SELECT * FROM vehicles WHERE tenant_id = 'A'
    │
    ▼
Trả về chỉ xe của Tenant A ✓

Customer của Tenant B truy cập:
────────────────────────────────
xediensg.com
    │
    ▼
Gọi API: GET /api/vehicles
    │ Header: X-Tenant-ID: B
    ▼
Backend tự động filter tenant_id = 'B'
    │
    ▼
Query: SELECT * FROM vehicles WHERE tenant_id = 'B'
    │
    ▼
Trả về chỉ xe của Tenant B ✓
```

### 5.3 Tenant Comparison

| Aspect | Tenant A | Tenant B |
|--------|----------|----------|
| **Domain** | rentcar-vn.com | xediensg.com |
| **Branding** | Logo, màu sắc riêng | Logo, màu sắc riêng |
| **Data** | Chỉ thấy data của A | Chỉ thấy data của B |
| **Khách hàng** | A1, A2, A3... | B1, B2, B3... |
| **Xe** | Xe A1, A2... | Xe B1, B2... |
| **Giá** | Tự quyết định | Tự quyết định |
| **Staff** | Nhân viên A | Nhân viên B |

---

## 6. User Roles & Permissions

| Role | Scope | Permissions |
|------|-------|-------------|
| **Super Admin** | Toàn hệ thống SaaS | Quản trị toàn sàn, quản lý tenant, subscription, xem log & báo cáo doanh thu toàn hệ thống |
| **Tenant Admin** | Toàn tenant | Quản lý subscription, thêm nhân viên, xem báo cáo |
| **Branch Manager** | 1 chi nhánh | Quản lý xe, booking, customer của chi nhánh |
| **Staff** | 1 chi nhánh | Tiếp nhận booking, giao xe, thu tiền |
| **Central Manager** | Kho trung tâm | Phê duyệt transfer, điều phối xe |
| **Customer** | - | Đặt xe, xem lịch sử thuê |

---

## 7. Key Screens

### 7.1 Customer-facing (Customer Website)

| Screen | Purpose |
|--------|---------|
| Landing page | Giới thiệu dịch vụ, CTAs |
| Branch locator | Tìm chi nhánh gần nhất (Google Maps) |
| Vehicle catalog | Danh sách xe + giá |
| Booking form | Form đặt xe |
| Booking confirmation | Trang xác nhận + QR code |
| Customer portal | Cổng thông tin cá nhân: Lịch sử thuê xe, trạng thái đơn đặt, cập nhật thông tin cá nhân (CCCD, GPLX) |

### 7.2 Tenant-facing (Admin Dashboard)

| Screen | Purpose |
|--------|---------|
| Dashboard | Tổng quan KPIs |
| Branch management | CRUD chi nhánh |
| Vehicle management | CRUD xe, status |
| Booking management | Danh sách booking, cập nhật status |
| Customer management | Danh sách khách hàng |
| Pricing management | Setup bảng giá |
| Transfer management | Request/approve transfer |
| Reports | Doanh thu, thống kê |
| Settings | Tenant info, users, subscription |

### 7.3 SaaS Admin-facing (Super Admin Portal)

| Screen | Purpose |
|--------|---------|
| Overview Dashboard | Tổng quan KPIs toàn hệ thống (Doanh thu SaaS, Active Tenants...) |
| Tenant Directory | Quản lý danh sách các nhà xe đăng ký hệ thống (Kích hoạt/Tạm khóa) |
| Subscription Configurations | Thiết lập và quản lý cấu hình các gói dịch vụ (FREE/BASIC/PRO...) |
| Billing & Payments | Duyệt và đối soát các giao dịch thanh toán gói dịch vụ của các Tenant |
| Global Settings | Cấu hình tham số hệ thống dùng chung (Cổng thanh toán, Email/SMS Gateway...) |
| System Logs & Audits | Theo dõi lịch sử thao tác của các Tenant Admin và các log nghiệp vụ quan trọng |
