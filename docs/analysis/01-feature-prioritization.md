# Phân Tích Cắt Gọt & Ưu Tiên Tính Năng

> **Ngày:** 24/06/2026
> **Mục tiêu:** Rà soát toàn bộ tính năng trong mindmap, xác định cái nào thực sự cần, cái nào hiếm dùng trong điều kiện thực tế, và đâu là key feature sống còn của sản phẩm.

---

## 1. Ma Trận Cắt Gọt — Cái nào Giữ, Cái nào Bỏ?

Phân tích dựa trên đối tượng chính: **nhà xe vừa & nhỏ tại VN (5-50 xe, 1-3 chi nhánh)**.

### 1.1 Bảng đánh giá từng tính năng

| # | Tính năng | Priority cũ | Mức độ sử dụng thực tế | Đề xuất | Lý do |
|---|-----------|-------------|------------------------|---------|-------|
| 1 | **SaaS Admin & Tenant Onboarding** | P0 | CAO — Mỗi tenant mới đều qua đây | **GIỮ P0, giảm scope** | Bỏ automation onboarding, làm thủ công (Super Admin tạo tenant). Automation để Phase 2. |
| 2 | **Branch Management** | P0 | CAO — Nhà xe nào cũng có ít nhất 1 chi nhánh | **GIỮ P0** | CRUD cơ bản, không cần Google Maps tích hợp trong MVP |
| 3 | **Vehicle Management (Fleet)** | P0 | CAO — Quản lý xe là core | **GIỮ P0** | CRUD + trạng thái (available/rented/maintenance) |
| 4 | **Vehicle Transfer (Điều phối xe)** | P2 | **RẤT THẤP** — Nhà xe <20 xe gọi điện thoại là xong | **CẮT khỏi MVP** | Với fleet nhỏ, điều phối liên chi nhánh gần như không xảy ra. Chi nhánh thiếu xe → họ tự gọi điện chứ không vào phần mềm. Chỉ có giá trị với chuỗi 100+ xe. |
| 5 | **Customer Management** | P1 | TRUNG BÌNH — Nhà xe nhỏ nhớ mặt khách, ít tra cứu | **GIỮ P1, giảm scope** | Chỉ lưu thông tin cơ bản + lịch sử thuê. AES encryption giữ lại vì bắt buộc pháp lý. Bỏ upload ảnh CCCD (để Phase 2). |
| 6 | **Pricing Engine (đa hệ số)** | P0 | TRUNG BÌNH — Hầu hết chỉ dùng giá ngày thường + cuối tuần | **GIỮ nhưng ĐƠN GIẢN HÓA** | MVP: `basePrice × dayMultiplier` (weekday=1.0, weekend=1.2). **Bỏ SeasonMultiplier** (mùa cao điểm nhà xe tự sửa basePrice). **Bỏ Holiday calendar** (quá phức tạp, mỗi năm phải maintain). |
| 7 | **Booking & Pessimistic Locking** | P0 | CAO — Đây là lý do nhà xe cần phần mềm | **GIỮ P0, TĂNG CƯỜNG** | Đây là **key feature #1**. Xem chi tiết mục 2 bên dưới. |
| 8 | **Payment & VietQR** | P1 | CAO — Chuyển khoản là phương thức chính tại VN | **GIỮ P1** | VietQR là điểm khác biệt cạnh tranh. Chi phí implement thấp (dùng API ngân hàng hoặc VietQR generator), nhưng giá trị marketing cao. |
| 9 | **Report & Dashboard Analytics** | P1 | TRUNG BÌNH — Nhà xe nhỏ chỉ cần biết "hôm nay thu bao nhiêu" | **GIỮ P1, giảm mạnh scope** | MVP chỉ cần 3 báo cáo: (1) Doanh thu theo ngày, (2) Số xe đang thuê, (3) Danh sách booking hôm nay. Dashboard "thông minh", biểu đồ xu hướng, top khách hàng → Phase 2. |
| 10 | **Customer Portal (Public Web)** | Trong Phase 4 | CAO — Khách vãng lai là 70-80% nguồn thu | **ĐƯA LÊN P0, làm song song với admin** | Đây là **sai lầm lớn nhất** trong roadmap hiện tại. Xem phân tích chi tiết ở doc 02. |
| 11 | **Notification (SMS/Email)** | P2 | THẤP với SMS, TRUNG BÌNH với Email | **CẮT SMS, GIỮ Email cơ bản** | SMS tốn ~800đ/tin, nhà xe nhỏ không chịu chi phí này. Email xác nhận booking là đủ. Push notification để khi có app mobile. |
| 12 | **Subscription Plans 4 bậc** | P0 | THẤP cho MVP — Chưa có khách thì phân mảnh gói để làm gì? | **Giảm còn 2 bậc** | MVP: FREE (dùng thử 30 ngày, giới hạn 1 branch + 10 xe) + PAID (không giới hạn). Phân mảnh gói cước (BASIC/PRO/ENTERPRISE) là bài toán scaling, không phải MVP. |

### 1.2 Tổng thời gian cắt được

```
Module bị cắt hoàn toàn:
  - Vehicle Transfer:        2 tuần → 0
  - Notification SMS:        1 tuần → 0

Module bị giảm scope:
  - Pricing Engine:          2 tuần → 1 tuần (bỏ season/holiday)
  - Report & Dashboard:      2 tuần → 1 tuần (chỉ 3 báo cáo cơ bản)
  - Subscription Plans:      1 tuần → 0.5 tuần (chỉ 2 bậc)
  - Customer Management:     2 tuần → 1.5 tuần (bỏ upload ảnh)

Tổng cắt được: ~4-5 tuần (từ 16 tuần Phase 3 xuống còn 11-12 tuần)
```

---

## 2. Key Features — Đâu là "Linh Hồn" Sản Phẩm?

Không phải mọi tính năng đều quan trọng như nhau. Với sản phẩm này, **3 tính năng sau quyết định thành bại**:

### 2.1 KEY #1: Real-time Fleet Availability (Tồn kho xe thời gian thực)

```
Đây là TRÁI TIM của sản phẩm.
```

**Vì sao quan trọng:** Mọi thứ khác (booking, pricing, payment) đều vô nghĩa nếu inventory không chính xác. Khách vào web thấy "có xe" nhưng ra quầy hết xe → mất uy tín vĩnh viễn.

**Yêu cầu kỹ thuật cốt lõi:**
- Cache Redis cho availability query (tránh query DB mỗi lần khách xem)
- Pessimistic locking (`SELECT FOR UPDATE`) khi tạo booking
- Cơ chế auto-release xe nếu booking không được confirm trong X phút
- Real-time sync giữa public web và admin dashboard

**Tiêu chuẩn chất lượng:**
- Thời gian check availability < 200ms
- 0 double-booking (không khoan nhượng)
- Tự động refresh trạng thái sau mỗi booking/cancel

### 2.2 KEY #2: Tenant Isolation & 5-Minute Onboarding

```
Đây là SẢN PHẨM. Không phải tính năng.
```

**Vì sao quan trọng:** Nhà xe không mua "phần mềm quản lý xe", họ mua "cái web riêng để khách đặt xe". Họ chỉ quan tâm 2 thứ:
1. "Dữ liệu của tôi có bị lộ cho thằng khác không?"
2. "Mất bao lâu để tôi có web riêng?"

**Yêu cầu kỹ thuật cốt lõi:**
- Postgres RLS ở tầng database (không chỉ filter ở app)
- Tenant context resolver từ subdomain + header
- Audit log mọi truy cập cross-tenant
- Template web tự động deploy khi tenant đăng ký

**Tiêu chuẩn chất lượng:**
- 5 phút từ đăng ký → có web riêng chạy được
- 100% isolation (audit chứng minh tenant A không thể đọc data tenant B)
- 10 → 1000 tenant không cần đổi code

### 2.3 KEY #3: Quick Counter Operations (Giao/Nhận xe tại quầy)

```
Đây là thứ nhân viên nhà xe dùng 50 lần/ngày.
```

**Vì sao quan trọng:** Trong khi public web là "mặt tiền", thì quy trình giao/nhận xe tại quầy là thứ nhân viên dùng nhiều nhất. Nếu nó chậm hoặc khó dùng, họ sẽ quay lại dùng sổ giấy.

**Yêu cầu kỹ thuật cốt lõi:**
- Giao xe: Chọn booking → Xác nhận thông tin → Ghi nhận km/xăng ban đầu → 1 click hoàn tất
- Nhận xe: Quét mã booking → Nhập km/xăng về → Tự động tính tiền (gồm phụ phí trễ giờ/hư hỏng) → Chọn phương thức thanh toán → In hóa đơn
- Tối đa 3 bước cho mỗi thao tác
- Hỗ trợ offline-first (nếu mất mạng vẫn ghi nhận được, sync sau)

**Tiêu chuẩn chất lượng:**
- Giao xe < 60 giây
- Nhận xe + tính tiền < 90 giây
- Không cần training, nhìn UI là dùng được

---

## 3. Ma Trận Ưu Tiên Sau Điều Chỉnh

```
                  QUAN TRỌNG
                      ▲
                      │
         Key #1       │       Key #3
      Availability    │    Counter Ops
                      │
    ──────────────────┼──────────────────→ KHẨN CẤP
                      │
         Key #2       │
      Tenant Iso      │    Guest Booking
      + Onboarding    │    (Public Web)
                      │
```

| Góc phần tư | Tính năng | Hành động |
|-------------|-----------|-----------|
| **Quan trọng + Khẩn cấp** | Fleet Availability + Counter Ops | Làm đầu tiên, làm tốt nhất |
| **Quan trọng + Ít khẩn cấp** | Tenant Isolation + Onboarding | Làm nền móng trước khi code nghiệp vụ |
| **Ít quan trọng + Khẩn cấp** | Guest Booking (Public Web) | Làm song song, không đợi hết admin mới làm |
| **Ít quan trọng + Ít khẩn cấp** | Report, Notification, Transfer | Lùi Phase 2 hoặc cắt |

---

## 4. Khác Biệt Cạnh Tranh — Tại Sao Khách Chọn Mình?

Phân tích nhanh đối thủ:

| Đối thủ | Điểm mạnh | Điểm yếu |
|---------|-----------|----------|
| **Nanosoft** | Đầy đủ tính năng, có app mobile | Đắt (trọn gói ~50tr/năm), không có multi-tenant, không có public web riêng |
| **Smacar** | Giao diện đẹp, cloud-based | Tập trung vào garage sửa chữa, rental chỉ là module phụ |
| **EzCloud** | Mạnh về hotel, có rental car add-on | Rental car là add-on, không phải sản phẩm chính |

**Vị trí cạnh tranh của Car Rental SaaS:**

```
Đắt ▲
    │  Nanosoft (full-feature, đắt)
    │
    │  ★ CHÚNG TA (vừa đủ, giá hợp lý)
    │  - Multi-tenant gốc (không phải add-on)
    │  - Public web riêng cho từng tenant
    │  - Real-time inventory chính xác
    │  - 5-min onboarding
    │
    │  Excel / Sổ tay (miễn phí, thủ công)
    ▼
    Ít tính năng ──────────────────────► Nhiều tính năng
```

**Tuyên ngôn sản phẩm:** "Phần mềm cho thuê xe ĐƠN GIẢN NHẤT — đủ dùng, không thừa, giá hợp lý."

---

*Tài liệu này là một phần trong chuỗi phân tích trước khi tái cấu trúc roadmap. Xem tiếp:*
- [02 - Phân tích Hành vi Khách Thuê Xe](02-customer-booking-behavior.md)
- [03 - So sánh 2 Loại hình Cho thuê](03-business-model-comparison.md)
- [04 - Định hướng Công nghệ](04-technology-direction.md)
- [05 - Roadmap Tái cấu trúc](05-roadmap-restructure.md)
