# Car Rental SaaS Platform — Presentation

**Ghi chú:** Số liệu trong slide được đánh dấu nguồn: `[KS]` = từ khảo sát thực tế, `[ƯT]` = ước tính dựa trên dữ liệu thị trường, `[MT]` = mục tiêu nội bộ, `[DB]` = dựa trên quan sát thực địa.

---

## Slide 1: Sản Phẩm Là Gì?

**Car Rental SaaS** — Nền tảng B2B SaaS giúp nhà xe cho thuê quản lý xe, đơn hàng, khách hàng và thanh toán. Mỗi nhà xe có một web đặt xe riêng với domain riêng.

| Ứng dụng | Ai dùng? | Tech |
|-----------|----------|------|
| **SaaS Admin Portal** | Super Admin — quản lý toàn bộ tenant, subscription | Angular 17+ |
| **Tenant Dashboard** | Nhân viên nhà xe — quản lý xe, booking, khách, tiền | Angular 17+ |
| **Customer Website** | Khách thuê xe — tìm xe, đặt xe không cần tài khoản | Next.js 14 |

**Mô hình:** Multi-tenant (1 codebase, nhiều nhà xe) + Multi-branch (mỗi nhà xe có nhiều chi nhánh)

**Thị trường Phase 1:** Cho thuê xe tự lái (xe máy & ô tô) tại các điểm du lịch.

---

## Slide 2: 7 Module Chính & Vai Trò Trong Sản Phẩm

| # | Module | Vai trò | Priority |
|---|--------|---------|----------|
| 1 | **Vehicle Management** | Quản lý đội xe: CRUD, trạng thái (available/rented/maintenance), cache Redis tồn kho real-time | P0 |
| 2 | **Tenant & Branch Management** | Cô lập dữ liệu giữa các nhà xe (PostgreSQL RLS), quản lý chi nhánh, subscription FREE/PAID | P0 |
| 3 | **Booking & Counter Ops** | Tạo/hủy đơn, giao/nhận xe tại quầy, chống double-booking bằng `SELECT FOR UPDATE` | P0 |
| 4 | **Customer Management** | Lưu thông tin khách, mã hóa CCCD/GPLX (AES), lịch sử thuê, tự động chuyển Guest → Member | P1 |
| 5 | **Pricing Engine** | `basePrice x dayMultiplier` (weekday=1.0, weekend=1.2) | P0 |
| 6 | **Payment Management** | Tiền mặt, chuyển khoản, tạo mã VietQR | P1 |
| 7 | **Reporting** | 3 báo cáo cơ bản: doanh thu ngày, fleet status, booking hôm nay | P1 |

### Subscription (MVP)

| Plan | Giá/tháng | Giới hạn |
|------|-----------|----------|
| **FREE** | 0đ | 1 Branch, 10 Xe, 30 Bookings, dùng thử 30 ngày |
| **PAID** | 1.500.000đ | 5 Branches, 100 Xe, 500 Bookings |

`[MT]` Mức giá PAID là mục tiêu ban đầu, sẽ điều chỉnh sau khi có phản hồi từ nhà xe dùng thử.

---

## Slide 3: Key Features Gắn Với Module

Mỗi key feature thuộc về một module cụ thể. Đây là 4 thứ ảnh hưởng đến quyết định dùng sản phẩm của nhà xe.

### Key #1: Tồn Kho Xe Real-time (Module: Vehicle Management)

Module quản lý xe chịu trách nhiệm hiển thị đúng trạng thái xe tại mọi thời điểm.

| Yêu cầu | Cách làm | Thuộc module |
|----------|----------|---------------|
| Check availability < 200ms | Redis cache `tenant:{id}:branch:{id}:avail:{date}`, TTL 60s | Vehicle |
| 0 double-booking | `SELECT FOR UPDATE` tại tầng DB khi tạo booking | Vehicle + Booking |
| Tự động trả xe nếu booking hết hạn | Scheduled job quét booking `pending` quá X phút, release cache | Booking → Vehicle |

**Vì sao quan trọng:** `[DB]` Qua trao đổi với một số nhà xe, vấn đề họ gặp nhiều nhất là khách vào web thấy xe trống nhưng ra quầy hết xe. Nếu inventory không chính xác, các tính năng khác mất đi giá trị.

### Key #2: Cô Lập Dữ Liệu + Web Riêng Cho Mỗi Nhà Xe (Module: Tenant & Branch)

Module tenant quản lý việc tách biệt dữ liệu và cấp web riêng cho từng nhà xe.

| Yêu cầu | Cách làm | Thuộc module |
|----------|----------|---------------|
| Tenant không đọc được data của nhau | PostgreSQL RLS — policy filter `tenant_id` ở tầng DB | Tenant |
| Nhận diện tenant từ request | `TenantInterceptor` bắt subdomain/header → set `app.current_tenant_id` | Tenant |
| Đăng ký xong có web riêng ngay | Template web tự động gắn domain tenant, kích hoạt trong < 5 phút `[MT]` | Tenant |

**Vì sao quan trọng:** `[DB]` Nhà xe không mua "phần mềm quản lý", họ muốn "web riêng để khách đặt xe". 2 câu hỏi đầu tiên họ hỏi: "Dữ liệu tôi có bị lộ không?" và "Mất bao lâu có web?"

### Key #3: Giao/Nhận Xe Tại Quầy Nhanh (Module: Booking & Counter Ops)

Module booking quản lý toàn bộ vòng đời đơn hàng. Trọng tâm là tối ưu 2 thao tác nhân viên làm nhiều nhất trong ngày: **giao xe** (lúc khách đến lấy) và **nhận xe** (lúc khách trả).

#### Flow Giao Xe (Pick-up) — 3 bước, dưới 2 phút

```
Bước 1: Chọn booking         Bước 2: Xác nhận + kiểm tra      Bước 3: Hoàn tất
┌──────────────────┐       ┌──────────────────────────┐       ┌──────────────────┐
│ Nhân viên tìm    │       │ • Check CCCD/GPLX gốc    │       │ In phiếu giao xe │
│ booking (theo    │──→    │ • Ghi km lúc giao        │──→    │ (có chữ ký số)   │
│ tên/SĐT/mã book) │       │ • Ghi mức xăng lúc giao  │       │ Xe → in_progress │
│                  │       │ • Khách ký xác nhận      │       │                  │
└──────────────────┘       └──────────────────────────┘       └──────────────────┘
```

#### Flow Nhận Xe (Return) — tự động tính tiền, không cần bấm máy tính tay

```
Bước 1: Quét mã             Bước 2: Nhập số cuối        Bước 3: Hệ thống tự tính       Bước 4: Thanh toán
┌──────────────────┐       ┌──────────────────┐       ┌──────────────────────────┐    ┌──────────────────┐
│ Quét mã QR trên  │       │ • Km lúc trả     │       │ Tiền thuê =              │    │ Chọn PTTT:       │
│ phiếu giao xe    │──→    │ • Mức xăng lúc trả│──→   │ basePrice × số ngày      │──→ │ • Tiền mặt       │
│ → hiện ra booking│       └──────────────────┘       │ + phụ phí trả trễ (nếu có)│    │ • Chuyển khoản   │
└──────────────────┘                                  │ + phụ phí hư hỏng (nếu có)│    │ • VietQR         │
                                                      │ + phụ phí vượt km (nếu có)│    └──────────────────┘
                                                      │ − cọc (nếu có)            │
                                                      └──────────────────────────┘
```

**Các khoản phụ phí hệ thống tự động áp dụng:**
- **Trả trễ:** Quá giờ trả → phụ phí theo giờ (vd: 50k/giờ), do nhà xe cấu hình
- **Vượt km:** Vượt giới hạn km trong gói thuê → phụ phí theo km (vd: 5k/km)
- **Hư hỏng:** Nhân viên chọn mức hư hỏng (nhẹ/vừa/nặng) → hệ thống áp mức phí tương ứng

#### Chống Quên & Chống Nhầm

| Cơ chế | Cách hoạt động |
|--------|----------------|
| Status workflow bắt buộc | `pending → confirmed → in_progress → completed` — không được nhảy bước, không được bỏ trống |
| Cảnh báo xe quá hạn trả | Scheduled job quét mỗi 5 phút, highlight booking `in_progress` đã quá `return_time` |
| Gắn booking với xe cụ thể | 1 xe chỉ có 1 booking `in_progress` tại một thời điểm — DB constraint đảm bảo |

**Vì sao quan trọng:** `[DB]` Nhân viên nhà xe thực hiện giao/nhận xe 10-30 lượt/ngày (mùa thấp điểm) đến 30-60 lượt/ngày (mùa cao điểm) với shop 20-30 xe. Nếu mỗi lần nhận xe mà nhân viên phải bấm máy tính tay để cộng trừ các khoản → vừa chậm, vừa dễ sai. Hệ thống tự tính toàn bộ, nhân viên chỉ cần nhập 2 con số (km về, xăng về) → nhanh hơn ghi sổ, khách không phải đợi lâu.

### Key #4: Khách Đặt Xe Không Cần Tài Khoản (Module: Customer Website)

Module public web cho phép khách vãng lai tìm và đặt xe không cần đăng ký.

| Yêu cầu | Cách làm | Thuộc module |
|----------|----------|---------------|
| Guest checkout < 3 phút | Form tối thiểu: tên + SĐT + CCCD + ngày thuê | Customer Website |
| Mobile-first | Khách du lịch dùng điện thoại là chính → responsive bắt buộc | Customer Website |
| Xác nhận tức thì | Mã QR + email xác nhận gửi ngay sau khi đặt | Customer Website + Email |

**Vì sao quan trọng:** `[KS]` Khảo sát sơ bộ từ một số nhà xe tự lái tại Đà Nẵng, Nha Trang (2024-2025) cho thấy khách vãng lai chiếm phần lớn lượt thuê (xem slide 5). Họ không muốn tạo tài khoản.

---

## Slide 4: Sản Phẩm Cần Làm Tốt Nhất Điều Gì?

Không phải mọi tính năng đều quan trọng như nhau. Với đối tượng nhà xe nhỏ & vừa (10-50 xe), 3 thứ sau ảnh hưởng đến quyết định trả tiền của họ:

```
Ưu tiên cao nhất:

  1. TỒN KHO CHÍNH XÁC
     └── Khách thấy "còn xe" trên web → ra quầy phải CÓ xe
     └── Module: Vehicle Management + Redis cache + SELECT FOR UPDATE

  2. ĐẶT XE KHÔNG CẦN TÀI KHOẢN
     └── Khách du lịch vào web → tìm xe → đặt → nhận mã QR, tất cả dưới 5 phút
     └── Module: Customer Website (Next.js), Public API

  3. DỮ LIỆU RIÊNG — WEB RIÊNG
     └── Nhà xe đăng ký → 5 phút sau có web riêng chạy được, data được cô lập
     └── Module: Tenant Management + PostgreSQL RLS

Ưu tiên thứ hai:

  4. GIAO/NHẬN XE NHANH TẠI QUẦY
     └── Module: Booking & Counter Ops

  5. THANH TOÁN ĐA DẠNG (tiền mặt, chuyển khoản, VietQR)
     └── Module: Payment Management
```

### Định vị so với đối thủ `[DB]`

Dựa trên quan sát thị trường (chưa phải khảo sát chính thức):

| Đối thủ | Điểm mạnh | Điểm yếu (theo quan sát) |
|---------|-----------|--------------------------|
| **Nanosoft** | Đầy đủ tính năng, có app mobile | Giá cao (theo thông tin từ người dùng), không multi-tenant, không web riêng cho từng nhà xe |
| **Smacar** | Giao diện đẹp, cloud-based | Tập trung vào garage sửa chữa, rental là module phụ |
| **EzCloud** | Mạnh về hotel | Rental car là add-on, không phải sản phẩm chính |

**Vị trí mong muốn của Car Rental SaaS:** Phần mềm vừa đủ tính năng cho nhà xe nhỏ & vừa, giá hợp lý, khác biệt ở multi-tenant gốc + web riêng mỗi tenant.

---

## Slide 5: Khách Vãng Lai vs Khách Thường Xuyên

### Số liệu từ khảo sát sơ bộ `[KS]`

Tháng 2024-2025, nhóm đã trao đổi trực tiếp với một số nhà xe tự lái tại Đà Nẵng và Nha Trang (số lượng mẫu nhỏ, chưa đủ đại diện thống kê). Kết quả ghi nhận:

```
Phân bổ lượt booking (ước tính từ mẫu khảo sát):

  Khách vãng lai (Guest):    khoảng 70-75% lượt đặt
  Khách quen (Member):       khoảng 25-30% lượt đặt

  → Guest booking là luồng chính, không phải luồng phụ.
```

**Lưu ý:** Đây là số liệu từ mẫu nhỏ. Cần khảo sát rộng hơn (30-50 nhà xe) để có con số tin cậy. Tỷ lệ thực tế sẽ khác nhau theo địa điểm và loại hình nhà xe.

### Đặc điểm 2 nhóm khách `[DB]`

| | Khách Vãng Lai (Guest) | Khách Thường Xuyên (Member) |
|---|---|---|
| **Chân dung điển hình** | Khách du lịch nội địa/quốc tế, nhóm bạn đi chơi cuối tuần, người đi công tác ngắn ngày | Dân địa phương cần xe định kỳ, doanh nhân thuê xe tháng |
| **Tần suất thuê** | 1-2 lần/năm (họ thuê xe ở nơi họ ĐẾN, không phải nơi họ SỐNG) | 1-4 lần/tháng (thuê ở nơi họ SỐNG) |
| **Kênh tìm đến nhà xe** | Google Search ("thuê xe máy Đà Nẵng"), Google Maps gần khách sạn, Facebook group du lịch, TikTok | Đã biết nhà xe từ trước, chủ yếu gọi Zalo/điện thoại đặt |
| **Yêu cầu với web** | Không muốn tạo tài khoản, form ngắn nhất có thể, xem giá tổng trước khi đặt | Lưu CCCD/GPLX, lịch sử thuê, đặt lại 1 chạm |
| **Mức trung thành** | Thấp — không quan tâm nhà xe tên gì, chỉ quan tâm "có xe không, giá bao nhiêu" | Cao — đã quen nhà xe thì ít chuyển |

### Hệ quả với thiết kế sản phẩm

1. Guest checkout phải là flow chính, không phải "tính năng làm sau"
2. Public API cần được phát triển song song với Admin API ngay từ Phase B
3. Không phân biệt Guest và Member ở tầng DB — chung 1 bảng `customers`, khác flag `is_guest`

---

## Slide 6: Hành Trình Khách Vãng Lai & Chiến Lược Chuyển Đổi

### Flow đặt xe của khách vãng lai (không có tài khoản)

Đây là flow dự kiến cho MVP, dựa trên quan sát hành vi khách du lịch `[DB]`:

```
  1. NHU CẦU              2. TÌM KIẾM             3. SO SÁNH
  ┌──────────┐           ┌──────────┐           ┌──────────┐
  │ Đang ở   │──Google──→│ Vào web   │──Xem──→  │ So giá   │
  │ khách sạn│  Maps     │ nhà xe    │  list    │ 2-3 xe   │
  │ Đà Nẵng  │           │(domain riêng)│        │ cùng loại│
  └──────────┘           └──────────┘           └──────────┘
                                                      │
                                                      ▼
  6. RA QUẦY NHẬN XE    5. XÁC NHẬN             4. ĐẶT XE
  ┌──────────┐           ┌──────────┐           ┌──────────┐
  │ Đến chi  │←──Mã QR──│ Nhận email│←──Guest──│ Điền: tên│
  │ nhánh    │           │ + mã book │  checkout│ SĐT, CCCD│
  │ lấy xe   │           └──────────┘           │ ngày thuê│
  └──────────┘                                 └──────────┘
```

### Chiến lược chuyển đổi Guest → Member

Sau khi guest đặt xe thành công, hệ thống mời tạo tài khoản:

```
┌──────────────────────────────────────┐
│ "Đặt xe thành công."                  │
│                                      │
│ Lưu thông tin để lần sau              │
│ đặt xe nhanh hơn chỉ với 1 chạm?      │
│                                      │
│ [Tạo mật khẩu]      [Để sau]         │
└──────────────────────────────────────┘
```

`[MT]` Tỷ lệ chuyển đổi mục tiêu ban đầu: 15-25% guest tạo tài khoản sau lần đặt đầu tiên. Sẽ đo thực tế sau khi có dữ liệu từ tenant dùng thử.

### Lưu ý về giả định

Các giả định về hành vi trên cần được kiểm chứng qua:
- Analytics trên web tenant đầu tiên (thời gian trung bình mỗi bước, tỷ lệ bounce)
- Phỏng vấn khách thuê tại quầy
- A/B test vị trí nút "Tạo tài khoản"

---

## Slide 7: Hai Loại Hình Cho Thuê Xe Du Lịch

### Loại A: Thuê Xe Có Lái (Chauffeur-driven)

```
KHÁCH ←────→ NHÀ XE ←────→ TÀI XẾ
(thuê cả xe+lái)  (sở hữu xe,     (nhân viên hoặc hợp đồng)
                   quản lý tài xế)

Dịch vụ: Đưa đón sân bay, tour du lịch theo ngày, xe hợp đồng doanh nghiệp, cưới hỏi, sự kiện
```

### Loại B: Thuê Xe Tự Lái (Self-drive)

```
KHÁCH ←────→ NHÀ XE
(chỉ thuê xe, tự lái)  (sở hữu xe, chỉ quản lý xe)

Dịch vụ: Khách du lịch thuê xe máy/ô tô tham quan tự túc, dân địa phương thuê xe đi việc
```

### So sánh 2 loại hình

| Khía cạnh | Xe Có Lái | Xe Tự Lái |
|-----------|-----------|-----------|
| **Khách hàng chính** | Tour du lịch nhóm, cưới hỏi, doanh nghiệp, sự kiện | Khách du lịch cá nhân, Tây ba lô, dân địa phương |
| **Đối tượng quản lý** | Xe + Tài xế + Lịch tour | Xe + Booking + Giao nhận |
| **Booking pattern** | Đặt trước vài ngày - vài tuần, theo tour/lịch trình | Đặt trước vài giờ - vài ngày, linh hoạt |
| **Thủ tục giao nhận** | Không có (tài xế đi cùng xe) | Có (kiểm tra xe, ký hợp đồng, ghi km/xăng) |
| **Yêu cầu pháp lý** | GPKD vận tải, phù hiệu xe | Hợp đồng thuê tài sản (đơn giản hơn) |
| **Số lượng cơ sở `[ƯT]`** | Khoảng 5,000 doanh nghiệp vận tải du lịch (tập trung TP lớn) | Khoảng 15,000-20,000 cơ sở (rải khắp 63 tỉnh thành) |
| **Mức sẵn sàng chi `[ƯT]`** | Trung bình 2-5tr/tháng cho phần mềm | Trung bình 500k-2tr/tháng |
| **Đối thủ hiện tại** | Nanosoft, VNPT, các PMS vận tải | Phân mảnh, chưa có player SaaS rõ ràng |

`[ƯT]` Số lượng cơ sở và mức chi là ước tính từ dữ liệu công khai (Tổng cục Du lịch, Sở GTVT các tỉnh). Cần nghiên cứu thị trường chính thức để xác nhận.

---

## Slide 8: Vì Sao Chọn Xe Tự Lái Cho Phase 1?

### Lý do chính (dựa trên phân tích nội bộ, không phải nghiên cứu thị trường chính thức)

| # | Lý do | Giải thích |
|---|-------|-------------|
| 1 | **Domain đơn giản hơn** | Không cần quản lý tài xế, tour, lịch trình, chấm công. Chỉ cần: Xe + Booking + Khách hàng + Thanh toán |
| 2 | **Thị trường đông hơn** `[ƯT]` | Số cơ sở xe tự lái ước tính gấp 3-4 lần xe có lái. Mỗi điểm du lịch (Đà Nẵng, Nha Trang, Phú Quốc, Đà Lạt...) có hàng chục shop |
| 3 | **Ít đối thủ SaaS hơn** | Xe có lái đã có Nanosoft, VNPT. Xe tự lái phần lớn dùng Excel/Zalo, chưa có SaaS player chiếm lĩnh |
| 4 | **Phù hợp Guest Booking** | Khách vãng lai là chính → đúng thế mạnh Next.js (SEO, SSR) cho web đặt xe công cộng |
| 5 | **Dễ nhân bản** | Shop xe máy ở Đà Nẵng, Nha Trang, Phú Quốc có nghiệp vụ gần giống hệt nhau → template hóa nhanh |

### Lộ trình 2 pha

```
Phase 1 (MVP): XE TỰ LÁI                  Phase 2 (Mở rộng): THÊM XE CÓ LÁI
┌───────────────────────────┐            ┌───────────────────────────┐
│ • 4 module core: Vehicle, │            │ • Thêm module: Driver,    │
│   Booking, Customer,      │  ────→     │   Tour, Dispatch         │
│   Payment                 │  50+ tenant│ • Upsell tenant hiện tại  │
│ • Guest web + Admin       │  có revenue│ • Tăng ARPU              │
└───────────────────────────┘            └───────────────────────────┘
```

### Rủi ro đã biết của xe tự lái `[DB]`

- **Bằng lái:** Khách nước ngoài cần IDP (1968 Vienna Convention). Nhiều nước dùng Geneva 1949 → IDP không hợp lệ tại VN. Thực tế CSGT ít kiểm tra nhưng nếu tai nạn → bảo hiểm từ chối.
- **Bảo hiểm:** Phần lớn shop xe máy không có bảo hiểm cho khách thuê. Khi tai nạn → tranh chấp dân sự.
- **Hệ quả cho sản phẩm:** Cần hợp đồng điện tử + xác nhận đã kiểm tra bằng lái. Module bảo hiểm tích hợp có thể để Phase 2.

---

## Slide 9: Công Nghệ — Tổng Quan & Lý Do Chọn

### Stack tổng thể

```
CLIENT:   Angular 17+ (Admin Portal, Tenant Dashboard)
          Next.js 14 (Customer Website — App Router, SSR/SSG)

SERVER:   Spring Boot 3.x + Java 17 (Monolithic Modular API)
          Spring Security + JWT + RBAC

DATA:     PostgreSQL + Row-Level Security (multi-tenant isolation)
          Redis (availability cache, rate limiting, JWT blacklist)
          Kafka (Phase 2: event-driven, audit trail)

INFRA:    Docker Compose (local dev + staging)
          Docker Swarm / K8s Lite (scale-up, 100+ tenant)
```

### Lý do chọn Java / Spring Boot (thay vì Node.js, Go, Python)

| Tiêu chí | Đánh giá |
|----------|----------|
| **Multi-tenant transaction** | ThreadLocal tenant context + `@Transactional` + Interceptor hoạt động out-of-the-box. Node.js/TypeORM phải tự build plumbing code. |
| **Security** | Spring Security là framework RBAC battle-tested nhất. Method-level security (`@PreAuthorize`), OAuth2 resource server có sẵn. |
| **Database isolation** | Hibernate filters + Postgres RLS tích hợp chặt. Không framework nào hỗ trợ RLS native như Spring Boot. |
| **Refactor sau này** | Monolith modular → tách microservices + Spring Cloud (có sẵn khi cần scale). |
| **Team** | Backend Lead có kinh nghiệm Spring Boot → giảm rủi ro kiến trúc. |

**Đánh đổi đã chấp nhận:** Dev ban đầu chậm hơn Node.js ~2 tuần (thời gian setup framework). JVM memory footprint cao hơn (~1GB RAM cho 100 tenant đầu tiên).

### Lý do chọn PostgreSQL + RLS (thay vì Schema-per-tenant hoặc NoSQL)

| Cách làm | Vấn đề |
|----------|--------|
| **Schema-per-tenant** | 100 tenant = 100 lần migrate. Connection pool riêng cho từng schema. Khó report cross-tenant. |
| **NoSQL (MongoDB)** | Nghiệp vụ thuê xe quan hệ chặt (xe → booking → khách → hóa đơn), RDBMS phù hợp hơn. |
| **RLS `[chọn]`** | 1 schema để migrate. 1 connection pool cho tất cả tenant. Defense-in-depth: app quên filter tenant_id → DB vẫn chặn. |

### Lý do chọn Redis (có ngay từ Phase A)

- Availability query cho public web cần < 200ms. Nếu 1000 khách cùng query DB → quá tải.
- Cache hit rate mục tiêu `[MT]`: > 80% cho availability queries
- Dùng thêm cho: JWT blacklist, rate limiting, session verify
- Memcached không được chọn vì thiếu data structure phong phú và không có persistence

### Kafka: Cân nhắc, chưa dùng trong MVP

`[MT]` Kafka được thiết kế sẵn trong kiến trúc nhưng chưa triển khai trong MVP. Với vài chục tenant + vài trăm booking/ngày, event-driven chưa cần thiết. Sẽ tích hợp khi có > 50 tenant, cần audit trail, hoặc chuyển sang microservices.

---

## Slide 10: Công Nghệ — Frontend & Infrastructure

### Angular 17+ cho Admin Dashboard

| Lý do | Giải thích |
|-------|------------|
| **Form/Table/CRUD** | Dashboard nặng form nhập liệu, bảng quản lý, CRUD. Angular Reactive Forms + RxJS đáng tin cậy nhất cho tác vụ này. |
| **Dependency Injection** | Quản lý service layer (API client, auth interceptor, cache) sạch sẽ. |
| **Team đã làm chủ** | FE Admin Lead có kinh nghiệm Angular — giảm rủi ro và thời gian học. |

### Next.js 14 cho Customer Website

| Lý do | Giải thích |
|-------|------------|
| **SEO** | Khách Google "thuê xe máy Đà Nẵng" cần ra web nhà xe. Next.js SSR/SSG giúp index tốt hơn SPA. |
| **Performance** | App Router + Server Components giảm JS bundle cho mobile (khách du lịch dùng điện thoại là chính). |
| **Team đã làm chủ** | FE Customer Lead có kinh nghiệm Next.js. |

### Vì sao KHÔNG dùng 1 framework cho cả 2?

| Phương án | Vấn đề |
|-----------|--------|
| Angular cho tất cả | SEO kém cho public web, bundle lớn cho mobile, không SSR |
| Next.js cho tất cả | Dashboard phức tạp khó quản lý state, thiếu Reactive Forms chuẩn, thiếu DI pattern |

**Quyết định:** Dùng cả 2, mỗi framework cho đúng thế mạnh. Đây là đánh đổi có chủ đích.

### Infrastructure — Docker Compose cho MVP

```
MVP (0→100 tenant):    Docker Compose, 1 server, manual deploy
Scale-up (100→1000):   Docker Swarm / K8s Lite, CI/CD, multi-server
Enterprise (1000+):    Kubernetes, Cloud (AWS/GCP), auto-scaling
```

`[MT]` Docker Compose đủ cho giai đoạn đầu. Chi phí VPS ước tính 300k-500k/tháng cho 100 tenant đầu tiên. Cloud (RDS + ElastiCache + ECS) ~$200-300/tháng — chưa cần khi chưa có revenue.

---

## Slide 11: Nguyên Tắc Chọn Công Nghệ

5 nguyên tắc áp dụng cho mọi quyết định công nghệ:

```
1. BATTLE-TESTED > TRENDY
   → Chọn thứ đã được kiểm chứng qua thời gian, không chọn vì "đang hot"

2. TEAM SKILL > PERFECT TECH
   → Công nghệ hiệu quả nhất = công nghệ team thực sự dùng được

3. SIMPLE FIRST, SCALE LATER
   → Monolith modular trước, microservices khi thực sự cần
   → Docker Compose trước, K8s khi có revenue ổn định

4. BUY vs BUILD
   → Dùng giải pháp có sẵn (Postgres RLS built-in), đừng tự build

5. ENOUGH IS ENOUGH
   → Đừng tối ưu cho 1000 tenant khi đang có 0 tenant
   → MVP chỉ cần hoạt động tốt cho 10-50 tenant đầu tiên
```

### Công nghệ đã cân nhắc và không sử dụng trong MVP

| Công nghệ | Lý do không dùng trong MVP |
|-----------|---------------------------|
| **GraphQL** | CRUD API không cần GraphQL. REST + Swagger đáp ứng đủ. |
| **Microservices** | 3 người part-time không thể vận hành 10+ services. Modular monolith trước. |
| **NoSQL (MongoDB)** | Nghiệp vụ thuê xe quan hệ chặt (xe→booking→khách→hóa đơn). RDBMS phù hợp hơn. |
| **gRPC** | Không cần throughput cực cao. REST/JSON dễ debug, dễ tích hợp FE. |
| **Serverless (Lambda)** | Cold start + multi-tenant context propagation phức tạp. Server truyền thống đơn giản hơn. |
| **OAuth2/OIDC** | JWT stateless đủ cho MVP. OAuth2 thêm phức tạp không cần thiết. Để Phase 2 nếu cần SSO. |

---

## Slide 12: Roadmap — Tổng Quan 4 Phase, 21 Tuần

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    CAR RENTAL SAAS — ROADMAP 2.0                              │
├───────────────┬───────────────┬─────────────────┬─────────────────────────────┤
│   PHASE A     │   PHASE B     │    PHASE C       │      PHASE D               │
│   NỀN MÓNG    │   MVP CORE    │   MVP PUBLIC      │    POLISH & GO-LIVE        │
│   (6 tuần)    │   (8 tuần)    │   (4 tuần)        │    (3 tuần)                │
├───────────────┼───────────────┼─────────────────┼─────────────────────────────┤
│ W1     W6     │ W7     W14    │ W15     W18      │ W19        W21             │
└───────────────┴───────────────┴─────────────────┴─────────────────────────────┘
         │               │               │                    │
         ▼               ▼               ▼                    ▼
    ┌─────────┐    ┌──────────┐    ┌──────────┐        ┌──────────┐
    │Có auth  │    │Nhà xe    │    │Khách đặt │        │Deploy    │
    │+ tenant │    │quản lý   │    │được online│       │production│
    │+ DB     │    │được nội bộ│   │không cần TK│      │có khách  │
    └─────────┘    └──────────┘    └──────────┘        └──────────┘
```

### Nguyên tắc thiết kế Roadmap

1. **Mỗi phase có sản phẩm demo** → Demo được cho khách, không phải "làm dần"
2. **Public + Admin đồng thời** → Không tách "admin trước, public sau"
3. **Key features được ưu tiên đầu tư** → Availability, Tenant Iso, Counter Ops
4. **Gate chất lượng bắt buộc** → Không đạt → không qua phase tiếp

---

## Slide 13: Roadmap Chi Tiết — Phase A & Phase B

### PHASE A: NỀN MÓNG (Weeks 1-6)

```
Week 1-3: BOOTCAMP                     Week 4-6: BASE SYSTEM
├── Java Core & Spring Boot            ├── Boilerplate BE + FE
├── PostgreSQL + Spring Data JPA       ├── Auth: Login/Register/JWT/RBAC
├── Spring Security + JWT + RLS        ├── Multi-tenant Engine
└── Docker Compose                     └── DB Schema + RLS + Flyway
```

**Deliverables:** Login flow hoàn chỉnh, DB Schema + RLS, Tenant Isolation Test, Docker one-command, Swagger docs

**Gate:** Mọi TV tự code được API CRUD có JWT + Tenant Isolation Test PASS + Docker chạy trên 3 máy

---

### PHASE B: MVP CORE — Quản Lý Nội Bộ Nhà Xe (Weeks 7-14)

```
Sprint 1 (W7-8)          Sprint 2 (W9-10)         Sprint 3 (W11-12)        Sprint 4 (W13-14)
┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐
│ Branch + Vehicle │    │ Customer +       │    │ Booking +        │    │ Payment +        │
│ Management       │    │ Pricing Engine   │    │ Counter Ops      │    │ Basic Reports    │
│                  │    │                  │    │                  │    │                  │
│ • CRUD Branch    │    │ • CRUD Customer  │    │ • CRUD Booking   │    │ • Cash, Transfer │
│ • CRUD Vehicle   │    │ • AES encrypt    │    │ • Status workflow│    │ • VietQR Code    │
│ • Redis cache    │    │   CCCD/GPLX      │    │ • SELECT FOR     │    │ • 3 Báo cáo:     │
│ • Public search  │    │ • basePrice ×    │    │   UPDATE lock    │    │   doanh thu ngày │
│                  │    │   dayMultiplier  │    │ • Giao/nhận xe   │    │   fleet status   │
│                  │    │ • Public pricing │    │ • Guest booking  │    │   booking hôm nay│
└──────────────────┘    └──────────────────┘    └──────────────────┘    └──────────────────┘
```

**Gate M4 (W12):** Cho 1 nhà xe dùng thử nội bộ

**Gate B:** Double-booking test (10 req đồng thời → 1 thành công) + Cache hit rate > 80% + Full flow test

---

## Slide 14: Roadmap Chi Tiết — Phase C & Phase D

### PHASE C: MVP PUBLIC (Weeks 15-18)

```
Sprint 5 (W15-16): Public Website            Sprint 6 (W17-18): Customer Portal
┌──────────────────────────────────┐        ┌──────────────────────────────────┐
│ • Trang chủ + tìm kiếm xe        │        │ • Customer login/register        │
│ • Trang chi tiết xe + bảng giá   │        │ • Guest → Member tự động         │
│ • Form đặt xe guest checkout     │        │ • Customer dashboard             │
│ • Responsive mobile              │        │ • Lịch sử booking + trạng thái   │
│ • Multi-tenant domain routing    │        │ • 1-click rebook                 │
│ • Email xác nhận booking         │        │ • Quản lý CCCD/GPLX cá nhân      │
└──────────────────────────────────┘        └──────────────────────────────────┘
```

**Deliverables:** Public web hoàn chỉnh, Guest→Member flow, Customer Portal, Multi-tenant Domain

**Gate M6 (W18):** Mở đăng ký dùng thử (free trial)

---

### PHASE D: POLISH & GO-LIVE (Weeks 19-21)

```
Sprint 7 (W19-20): Testing                 Sprint 8 (W21): Deploy
┌──────────────────────────────────┐       ┌──────────────────────────────────┐
│ • Unit test BE coverage > 60%    │       │ • Deploy production (VPS/Cloud)  │
│ • Integration test full flow     │       │ • SSL/TLS cho từng domain        │
│ • Security audit (OWASP Top 10)  │       │ • Monitoring + health check      │
│ • Tenant isolation pentest       │       │ • User guide cho nhà xe          │
│ • Performance test               │       │ • SaaS landing page              │
│ • Bug fixing                     │       │ • Onboard tenant đầu tiên        │
└──────────────────────────────────┘       └──────────────────────────────────┘
```

**Deliverables:** Production deploy, Test > 60%, Security audit 0 Critical/High, Tenant Iso certification

**Gate M7 (W21):** GO-LIVE — Bắt đầu thu phí

---

## Slide 15: Milestones & Quyết Định Kinh Doanh

| Milestone | Tuần | Sản phẩm demo được | Hành động kinh doanh |
|-----------|------|---------------------|----------------------|
| **M1: Bootcamp** | W3 | API CRUD có JWT | Xác nhận team sẵn sàng code nghiệp vụ |
| **M2: Base Ready** | W6 | Auth + Tenant Isolation | Tenant isolation test PASS |
| **M3: Fleet Live** | W8 | Quản lý xe + xem xe trống | Demo 2-3 nhà xe, lấy feedback `[MT]` |
| **M4: Counter Ops** | W12 | Booking + giao/nhận xe | Cho 1 nhà xe dùng thử nội bộ `[MT]` |
| **M5: Payment Done** | W14 | Thanh toán + báo cáo cơ bản | Sẵn sàng thu tiền thật |
| **M6: Public Web** | W18 | Web công cộng hoàn chỉnh | Mở đăng ký dùng thử miễn phí `[MT]` |
| **M7: GO-LIVE** | W21 | Production system | Bắt đầu thu phí tenant đầu tiên `[MT]` |

`[MT]` Đây là mục tiêu nội bộ. Ngày thực tế có thể thay đổi dựa trên feedback từ tenant dùng thử và kết quả quality gate mỗi phase.

### Khác biệt chính so với Roadmap cũ

| Tiêu chí | Roadmap cũ (đã bỏ) | Roadmap mới (hiện tại) |
|----------|---------------------|------------------------|
| **Có sản phẩm chạy được** | Tuần 23 | Tuần 14 (Phase B xong) |
| **Public booking** | Tuần 22-23 (cuối cùng) | Từ tuần 7 (song song admin) |
| **Demo được cho khách** | Sau ~5 tháng | Sau ~3 tháng (W8) |
| **Cơ hội điều chỉnh** | Không có (làm 1 mạch) | Sau mỗi phase |
| **Quality Gate** | 5 mốc mơ hồ | 4 gate cứng, có test case cụ thể |

---

## Slide 16: Kế Hoạch Sau MVP (Phase 2+)

Các tính năng dưới đây là định hướng, sẽ được đánh giá lại sau khi có dữ liệu thực tế từ MVP:

| Ưu tiên | Tính năng | Thời gian dự kiến `[MT]` | Điều kiện kích hoạt |
|---------|-----------|--------------------------|---------------------|
| 1 | Subscription Plans đa bậc (BASIC/PRO/ENTERPRISE) | ~2 tuần | Có > 50 tenant trả phí, cần phân mảnh gói cước |
| 2 | Module Xe Có Lái (Driver + Tour + Dispatch) | ~6-8 tuần | Có revenue ổn định, ít nhất 5 tenant hỏi về xe có lái |
| 3 | Ứng dụng Mobile cho Tenant (iOS/Android) | ~8-10 tuần | Có > 100 tenant, nhu cầu mobile từ người dùng |
| 4 | API tích hợp bên thứ 3 (bảo hiểm, cổng thanh toán) | ~4 tuần | Có đối tác tích hợp cụ thể |
| 5 | Migration Monolith → Microservices | Liên tục | Khi monolith trở thành bottleneck thực sự |

### Tính năng đã cắt/lùi khỏi MVP

| Tính năng | Lý do |
|-----------|-------|
| **Vehicle Transfer (điều phối xe liên chi nhánh)** | Nhà xe < 50 xe không dùng — gọi điện thoại là xong `[DB]` |
| **SMS Notification** | Tốn ~800đ/tin, nhà xe nhỏ không chấp nhận chi phí này `[DB]`. Giữ Email. |
| **Pricing Season/Holiday Multiplier** | Nhà xe tự chỉnh basePrice mùa cao điểm. Không cần calendar phức tạp. |
| **Upload ảnh CCCD/GPLX** | Giữ AES encrypt text. Upload ảnh để Phase 2. |

---

## Slide 17: Tổng Kết

### Sản phẩm này giải quyết vấn đề gì?

Nhà xe nhỏ & vừa tại điểm du lịch đang dùng Excel/Zalo/sổ giấy để quản lý. Họ không có web riêng để khách đặt xe online. Các phần mềm hiện tại hoặc có chi phí cao (Nanosoft), hoặc không có multi-tenant/web riêng.

### Điểm khác biệt

| Yếu tố | Cách làm của Car Rental SaaS |
|--------|------------------------------|
| **Multi-tenant gốc** | PostgreSQL RLS — data isolation ở tầng database, không phải add-on |
| **Web riêng mỗi nhà xe** | Mỗi tenant có domain riêng, web đặt xe công cộng riêng |
| **Guest checkout** | Khách vãng lai đặt xe không cần tài khoản — đúng nhu cầu thị trường `[KS]` |
| **Giá** | FREE cho shop nhỏ dùng thử, PAID 1.5tr/tháng — cạnh tranh với chi phí Excel + thời gian |

### Rủi ro chính cần theo dõi

1. **Mức sẵn sàng trả tiền** `[ƯT]` — Cần xác nhận qua trial thực tế
2. **Thói quen dùng Excel/Zalo** `[DB]` — Khó thay đổi, cần onboarding tốt
3. **Tỷ lệ Guest/Member** `[KS]` — Số liệu từ mẫu nhỏ, cần đo thực tế
4. **Pháp lý xe tự lái** — Bằng lái quốc tế, bảo hiểm cho khách thuê

---

**Nguồn dữ liệu:**
- `[KS]` — Khảo sát sơ bộ từ một số nhà xe (mẫu nhỏ, chưa đại diện thống kê)
- `[ƯT]` — Ước tính từ dữ liệu công khai (Tổng cục Du lịch, Sở GTVT)
- `[MT]` — Mục tiêu nội bộ của dự án
- `[DB]` — Dựa trên quan sát thực địa và trao đổi với nhà xe

Chi tiết phân tích: [docs/analysis/](analysis/)
