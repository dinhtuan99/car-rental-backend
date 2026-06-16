# Multi-tenant vs Multi-branch - Giải thích kiến thức nền tảng

## Mục lục
1. [Tổng quan](#1-tổng-quan)
2. [Multi-tenant: Kiến trúc phần mềm](#2-multi-tenant-kiến-trúc-phần-mềm)
3. [Multi-branch: Mô hình nghiệp vụ](#3-multi-branch-mô-hình-nghiệp-vụ)
4. [Sự khác biệt cốt lõi](#4-sự-khác-biệt-cốt-lõi)
5. [Kết hợp Multi-tenant + Multi-branch](#5-kết-hợp-multi-tenant--multi-branch)

---

## 1. Tổng quan

| Khái niệm | Loại | Quyết định bởi | Cấp độ |
|-----------|------|----------------|--------|
| **Multi-tenant** | Kiến trúc phần mềm | SaaS Provider | System-wide |
| **Multi-branch** | Mô hình nghiệp vụ | Tenant (khách hàng) | Company-wide |

---

## 2. Multi-tenant: Kiến trúc phần mềm

### 2.1 Định nghĩa

```
Multi-tenant là CÁCH XÂY DỰNG PHẦN MỀM để phục vụ NHIỀU khách hàng trên
1 hệ thống duy nhất.
```

### 2.2 Câu hỏi cần trả lời

| Câu hỏi | Ý nghĩa |
|---------|---------|
| Làm sao 1 phần mềm phục vụ được nhiều công ty? | Shared infrastructure |
| Làm sao data của công ty A không thấy được công ty B? | Tenant isolation |
| Làm sao deploy/update chỉ cần làm 1 lần? | Single codebase |
| Làm sao scale khi có thêm khách hàng mới? | Auto-scaling |

### 2.3 Ví dụ kỹ thuật

```sql
-- Shared database với tenant_id
SELECT * FROM vehicles WHERE tenant_id = 'A'  -- Tenant A thấy xe A
SELECT * FROM vehicles WHERE tenant_id = 'B'  -- Tenant B thấy xe B

-- Cùng 1 database, cùng 1 code, nhưng data TÁCH BIỆT
```

### 2.4 So sánh Single vs Multi-tenant

| Aspect | Single-tenant | Multi-tenant |
|--------|---------------|--------------|
| **Codebase** | 1 cho mỗi khách hàng | 1 cho tất cả khách hàng |
| **Database** | 1 database riêng | Chia sẻ, phân biệt bằng `tenant_id` |
| **Server** | 1 server riêng | Chia sẻ tài nguyên |
| **Deploy** | Nhiều instance | 1 instance |
| **Chi phí** | Cao (mỗi khách 1 server) | Thấp (shared infrastructure) |
| **Scale** | Khó scale | Dễ scale |
| **Bảo trì** | Phức tạp | Đơn giản |

### 2.5 Minh họa

```
┌─────────────────────────────────────────────────────────────┐
│                 MULTI-TENANT ARCHITECTURE                     │
└─────────────────────────────────────────────────────────────┘

1 "INSTANCE" (1 codebase, 1 server, 1 database)
            │
            │ phục vụ
            ▼
┌─────────────────────────────────────────────────────────────┐
│                      TẤT CẢ KHÁCH HÀNG                         │
│                                                              │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐         │
│  │Tenant A │  │Tenant B │  │Tenant C │  │Tenant D │  ...     │
│  │RentCar  │  │Xe Điện  │  │Taxi MT  │  │Cho thuê  │         │
│  │   VN    │  │  Sài Gòn│  │Nha Trang│  │  xe ABC  │         │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘         │
│                                                              │
│  MỖI TENANT LÀ 1 CÔNG TY THUÊ XE ĐỘC LẬP                     │
│  - Data hoàn toàn TÁCH BIỆT                                   │
│  - Branding riêng (logo, màu sắc)                             │
│  - Giá riêng                                                  │
│  - Nhân viên riêng                                            │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Multi-branch: Mô hình nghiệp vụ

### 3.1 Định nghĩa

```
Multi-branch là CÁCH TỔ CHỨC KINH DOANH của mỗi công ty thuê xe,
trong đó họ có thể có NHIỀU CHI NHÁNH hoạt động độc lập hoặc
phối hợp với nhau.
```

### 3.2 Câu hỏi cần trả lời

| Câu hỏi | Ý nghĩa |
|---------|---------|
| Công ty có mấy chi nhánh? | Branch count |
| Chi nhánh có xe riêng hay chia sẻ? | Fleet ownership |
| Ai quản lý xe ở mỗi chi nhánh? | Branch manager |
| Khi chi nhánh thiếu xe, lấy xe từ đâu? | Central/Transfer |

### 3.3 Ví dụ nghiệp vụ

```
Tenant A: "RentCar VN" quyết định:
──────────────────────────────────────
├── Có 3 chi nhánh: Quận 1, Quận 3, Bình Thạnh
├── Mỗi chi nhánh quản lý 10 xe riêng
├── Kho trung tâm giữ 5 xe dự phòng
└── Khi thiếu xe → request từ kho trung tâm

Tenant B: "Xe Điện Sài Gòn" quyết định:
──────────────────────────────────────
├── Có 5 chi nhánh trải rộng Sài Gòn
├── Mỗi chi nhánh quản lý 15 xe riêng
├── 2 kho trung tâm (Đông và Tây Sài Gòn)
└── Tự do chuyển xe giữa các chi nhánh

→ CÙNG 1 PHẦN MỀM, nhưng MỖI CÔNG TY tổ chức KHÁC NHAU
```

### 3.4 Mối quan hệ Branch-Vehicle-Customer

```
┌─────────────────────────────────────────────────────────────┐
│                  BRANCH STRUCTURE                           │
└─────────────────────────────────────────────────────────────┘

CENTRAL BRANCH (Kho trung tâm)
    │
    ├── Có fleet DỰ PHÒNG để cấp cho chi nhánh thiếu
    ├── KHÔNG phục vụ khách hàng trực tiếp (thường)
    └── Chỉ điều phối xe

BRANCH (Chi nhánh)
    │
    ├── Phục vụ khách hàng TRỰC TIẾP
    ├── Có fleet RIÊNG để cho thuê
    ├── Nhận xe từ Central khi thiếu
    └── Báo cáo doanh thu về Central
```

### 3.5 Vehicle Transfer Flow (Multi-branch)

```
SCENARIO: Chi nhánh Quận 1 hết xe, khách cần thuê

1. CUSTOMER đặt xe tại Branch A1 (Quận 1)
   │
   ▼
2. BRANCH A1 kiểm tra fleet
   └── Xe available = 0 → Hết xe!
   │
   ▼
3. BRANCH A1 request TRANSFER từ Central
   └── "Cần 2 xe 4 chỗ gấp"
   │
   ▼
4. CENTRAL xem xe dự phòng
   └── Có 5 xe tại kho
   │
   ▼
5. CENTRAL duyệt TRANSFER
   └── Chuyển 2 xe → Branch A1
   │
   ▼
6. BRANCH A1 xác nhận nhận xe
   └── Xe available = 2
   │
   ▼
7. Xác nhận booking cho CUSTOMER
```

---

## 4. Sự khác biệt cốt lõi

```
┌─────────────────────────────────────────────────────────────────┐
│                    MULTI-TENANT                                  │
│                    (Architecture)                               │
├─────────────────────────────────────────────────────────────────┤
│  WHO?        │ Nhà phát triển phần mềm (SaaS provider)          │
│  WHAT?       │ Cách xây dựng phần mềm để phục vụ nhiều khách    │
│  WHEN?       │ Quyết định khi THIẾT KẾ phần mềm                 │
│  SCOPE        │ Toàn bộ hệ thống (system-wide)                   │
│  CHANGES?     │ Ít thay đổi sau khi đã design                   │
│  Câu hỏi      │ "Phần mềm hoạt động như thế nào?"               │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    MULTI-BRANCH                                  │
│                    (Business Model)                              │
├─────────────────────────────────────────────────────────────────┤
│  WHO?        │ Khách hàng thuê SaaS (tenant)                     │
│  WHAT?       │ Cách tổ chức hoạt động kinh doanh của họ          │
│  WHEN?       │ Quyết định khi KHÁCH ĐĂNG KÝ SaaS                │
│  SCOPE        │ Trong phạm vi công ty đó (per-tenant)           │
│  CHANGES?     │ Có thể thay đổi theo nhu cầu kinh doanh         │
│  Câu hỏi      │ "Công ty tổ chức như thế nào?"                  │
└─────────────────────────────────────────────────────────────────┘
```

### Bảng so sánh

| | Multi-tenant | Multi-branch |
|---|---|---|
| **Loại** | Kiến trúc phần mềm | Mô hình nghiệp vụ |
| **Quyết định bởi** | SaaS Provider | Tenant (khách hàng) |
| **Cấp độ** | System-wide | Company-wide |
| **Thay đổi** | Hiếm khi thay đổi | Có thể thay đổi theo nhu cầu |
| **Ví dụ** | Shared DB, tenant_id column | 3 chi nhánh, xe dự phòng |
| **Câu hỏi** | "Phần mềm hoạt động như thế nào?" | "Công ty tổ chức như thế nào?" |

---

## 5. Kết hợp Multi-tenant + Multi-branch

### 5.1 Sơ đồ hoàn chỉnh

```
┌─────────────────────────────────────────────────────────────────┐
│                    CAR RENTAL SaaS PLATFORM                     │
└─────────────────────────────────────────────────────────────────┘

                    ┌─────────────────┐
                    │   SaaS Platform │
                    │  (Shared Infra) │
                    └────────┬────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
        ▼                    ▼                    ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│   TENANT A    │    │   TENANT B    │    │   TENANT C    │
│  "RentCar VN" │    │ "Xe Điện SG"  │    │"Taxi Nha Trang"│
└───────┬───────┘    └───────┬───────┘    └───────┬───────┘
        │                    │                    │
        ▼                    ▼                    ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│   BRANCHES    │    │   BRANCHES    │    │   BRANCHES    │
│  ┌─────────┐  │    │  ┌─────────┐  │    │  ┌─────────┐  │
│ │ Central │  │    │  │ Central │  │    │  │ Central │  │
│  │ (5 xe)  │  │    │  │ (10 xe) │  │    │  │ (20 xe) │  │
│  └─────────┘  │    │  └─────────┘  │    │  └─────────┘  │
│  ┌─────────┐  │    │  ┌─────────┐  │    │  ┌─────────┐  │
│  │ Branch1 │  │    │  │ Branch1 │  │    │  │ Branch1 │  │
│  │ (10 xe) │  │    │  │ (15 xe) │  │    │  │ (25 xe) │  │
│  └─────────┘  │    │  └─────────┘  │    │  └─────────┘  │
│  ┌─────────┐  │    │  ┌─────────┐  │    │  ┌─────────┐  │
│  │ Branch2 │  │    │  │ Branch2 │  │    │  │ Branch2 │  │
│  │ (8 xe)  │  │    │  │ (12 xe) │  │    │  │ (20 xe) │  │
│  └─────────┘  │    │  └─────────┘  │    │  └─────────┘  │
└───────────────┘    └───────────────┘    └───────────────┘
        │                    │                    │
        ▼                    ▼                    ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│   CUSTOMERS   │    │   CUSTOMERS   │    │   CUSTOMERS   │
│   A1, A2...  │    │   B1, B2...  │    │   C1, C2...  │
└───────────────┘    └───────────────┘    └───────────────┘
```

### 5.2 Minh họa thực tế

```
Tenant A: "RentCar VN"
├── Branch A1: Quận 1 (10 xe)
├── Branch A2: Quận 3 (8 xe)
└── Central: Kho trung tâm (5 xe)

Tenant B: "Xe Điện Sài Gòn"
├── Branch B1: Q1 (15 xe)
├── Branch B2: Q2 (12 xe)
├── Branch B3: Q7 (10 xe)
└── Central: Kho trung tâm (10 xe)

─────────────────────────────────────────────────────────────
Tenant A không thấy xe của Tenant B (multi-tenant isolation)
Branch A1 không thấy xe của Branch A2 (multi-branch, cùng tenant)
```

### 5.3 Luồng dữ liệu

```
┌─────────────────────────────────────────────────────────────┐
│                 DATA FLOW                                    │
└─────────────────────────────────────────────────────────────┘

1. Request từ User
   │
   ▼
2. JWT Token → Extract tenant_id
   │
   ▼
3. Security Filter → Validate tenant
   │
   ▼
4. Repository Layer → Auto-add tenant_id filter
   │
   ▼
5. Database → Chỉ trả về data của tenant đó
   │
   ▼
6. Response → User chỉ thấy data CỦA MÌNH
```

---

## 6. Tại sao cần cả hai?

| Mô hình | Lợi ích | Ví dụ |
|---------|---------|-------|
| **Multi-tenant** | Tiết kiệm chi phí, dễ scale, bảo trì đơn giản | 1 server cho 100+ tenant |
| **Multi-branch** | Mỗi chi nhánh tự quản lý xe, nhân viên | RentCar VN có 3 chi nhánh |

**Trong thực tế:**
- **Tenant** = Công ty cho thuê xe (khách hàng SaaS của bạn)
- **Branch** = Chi nhánh của công ty đó
- Bạn (nhà cung cấp SaaS) chỉ cần quản lý 1 hệ thống, nhưng phục vụ được NHIỀU công ty
- Mỗi công ty tự quản lý các chi nhánh của mình

---

## 7. Hiểu đơn giản

| | Giải thích bằng bất động sản |
|---|---|
| **Multi-tenant** | "Nhà xây như thế nào?" - 1 tòa nhà có nhiều căn hộ, mỗi căn 1 gia đình |
| **Multi-branch** | "Người thuê sắp xếp nội thất ra sao?" - Mỗi gia đình tự trang trí theo ý thích |

**Tóm lại:**
- **Multi-tenant Architecture** = Cách xây dựng (kỹ thuật)
- **Multi-branch Business Model** = Cách tổ chức (kinh doanh)
