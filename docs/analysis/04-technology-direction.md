# Định Hướng Công Nghệ — Vì Sao Chọn Như Vậy?

**Ngày:** 24/06/2026
**Mục tiêu:** Giải thích lý do đằng sau mỗi lựa chọn công nghệ trong stack Car Rental SaaS. Mỗi quyết định đều có đánh đổi — tài liệu này làm rõ tại sao ta chấp nhận đánh đổi đó.

---

## 1. Tổng Quan Stack

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│  ┌──────────────────────┐  ┌──────────────────────┐             │
│  │  SaaS Admin Portal   │  │  Tenant Dashboard    │             │
│  │  (Angular 17+)       │  │  (Angular 17+)       │             │
│  └──────────────────────┘  └──────────────────────┘             │
│  ┌──────────────────────────────────────────────────┐           │
│  │  Customer Website (Next.js 14, App Router)        │           │
│  └──────────────────────────────────────────────────┘           │
├─────────────────────────────────────────────────────────────────┤
│                         SERVER LAYER                             │
│  ┌──────────────────────────────────────────────────┐           │
│  │  Spring Boot 3.x (Java 17) - Monolithic API       │           │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────────────┐  │           │
│  │  │ Security  │ │ Tenant   │ │ Business Modules │  │           │
│  │  │ JWT+RBAC │ │ Context  │ │ (Modular Pkg)    │  │           │
│  │  └──────────┘ └──────────┘ └──────────────────┘  │           │
│  └──────────────────────────────────────────────────┘           │
├─────────────────────────────────────────────────────────────────┤
│                        DATA & INFRA                              │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────┐       │
│  │PostgreSQL│ │  Redis   │ │  Kafka   │ │Docker Compose│       │
│  │(+ RLS)   │ │ (Cache)  │ │(Msg Q)   │ │(Local+Staging)│      │
│  └──────────┘ └──────────┘ └──────────┘ └──────────────┘       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. Backend — Tại Sao Java / Spring Boot?

### 2.1 Các Lựa Chọn Đã Cân Nhắc

| Tiêu chí | Java + Spring Boot | Node.js + NestJS | Go + Gin | Python + FastAPI |
|----------|-------------------|------------------|----------|------------------|
| **Type safety** | ★★★★★ (compile-time) | ★★★ (TypeScript) | ★★★★★ | ★★ (runtime) |
| **Multi-tenant support** | ★★★★★ (Hibernate filters, RLS) | ★★★ (tự build nhiều) | ★★★ | ★★ |
| **Transaction management** | ★★★★★ (@Transactional) | ★★★ (TypeORM) | ★★★ | ★★ |
| **Security ecosystem** | ★★★★★ (Spring Security) | ★★★ (Passport.js) | ★★ | ★★ |
| **Hệ sinh thái** | ★★★★★ (đầy đủ nhất) | ★★★★ | ★★★ | ★★★ |
| **Learning curve (team)** | CAO | TRUNG BÌNH | CAO | THẤP |
| **Tốc độ dev ban đầu** | CHẬM | NHANH | TRUNG BÌNH | NHANH |
| **Bảo trì dài hạn** | ★★★★★ | ★★★ | ★★★★ | ★★ |

### 2.2 Lý Do Chọn Java / Spring Boot

**Lý do #1: Multi-tenant transaction là bài toán phức tạp**
Hệ thống SaaS multi-tenant với RLS ở tầng database yêu cầu:
- Quản lý transaction context xuyên suốt request (ThreadLocal tenant context)
- Interceptor bắt tenant_id từ subdomain/header và set vào DB session
- Cơ chế bypass RLS cho Super Admin (tenant_id = null)
- `@Transactional` với isolation level phù hợp cho booking (pessimistic lock)

Spring Boot làm tất cả những việc này out-of-the-box. Node.js với TypeORM phải tự build rất nhiều plumbing code.

**Lý do #2: Spring Security là gold standard cho RBAC + JWT**
Không framework nào có hệ sinh thái security đầy đủ và battle-tested như Spring Security. Method-level security (`@PreAuthorize`), filter chain, OAuth2 resource server — tất cả đều có sẵn.

**Lý do #3: Team có chuyên gia backend (anh Tưởng)**
Yếu tố con người ảnh hưởng lớn đến thành công công nghệ. Có người dẫn dắt về Spring Boot là lợi thế lớn, giảm rủi ro mắc lỗi kiến trúc.

**Lý do #4: Refactor từ Monolith → Microservices dễ hơn**
Khi scale, Spring Boot monolith chuẩn (modular package) có thể tách thành microservices bằng cách extract module + thêm API gateway. Hệ sinh thái Spring Cloud hỗ trợ toàn bộ quá trình này.

### 2.3 Đánh Đổi Chấp Nhận

| Đánh đổi | Hệ quả | Cách giảm thiểu |
|----------|--------|-----------------|
| **Dev ban đầu chậm** | 2 tuần đầu chỉ setup framework | Phase 1 dành 3 tuần học tập + base code |
| **Team Frontend không code được BE** | Phụ thuộc vào BE lead | Mock API + Swagger để FE chạy song song |
| **JVM memory footprint lớn** | Cần server RAM cao hơn | 1GB RAM cho 100 tenant đầu tiên, chấp nhận được |

---

## 3. Database — Tại Sao PostgreSQL + RLS?

### 3.1 Các Lựa Chọn Đã Cân Nhắc

| Tiêu chí | PostgreSQL + RLS | MySQL (Schema per tenant) | MongoDB (Collection per tenant) |
|----------|-----------------|---------------------------|--------------------------------|
| **Isolation level** | ★★★★★ (row-level, DB enforced) | ★★★★ (schema-level) | ★★★ (application-level) |
| **Shared connection pool** | ★★★★★ (1 pool cho tất cả tenant) | ★★ (1 pool/schema) | ★★★★ |
| **Migration complexity** | ★★★★★ (1 schema để migrate) | ★ (phải migrate từng schema) | ★★★ |
| **Reporting cross-tenant** | ★★★★★ (Super Admin bypass RLS) | ★★ | ★★ |
| **Cost** | FREE | FREE | FREE (Community) |
| **RLS native support** | ★★★★★ | KHÔNG CÓ | KHÔNG CÓ |

### 3.2 PostgreSQL RLS — Cách Hoạt Động

```sql
-- 1. Enable RLS trên mọi bảng nghiệp vụ
ALTER TABLE vehicles ENABLE ROW LEVEL SECURITY;

-- 2. Tạo policy tự động filter theo tenant_id
CREATE POLICY tenant_isolation_policy ON vehicles
    FOR ALL
    USING (tenant_id = current_setting('app.current_tenant_id')::uuid);

-- 3. Spring Boot set tenant context trước mỗi query
-- TenantInterceptor.java:
SET app.current_tenant_id = 'tenant-uuid-here';

-- 4. Super Admin bypass (tenant_id = null)
CREATE POLICY super_admin_bypass ON vehicles
    FOR ALL
    USING (current_setting('app.current_tenant_id', true) IS NULL);
```

**Điểm mạnh:** Nếu app code quên filter tenant_id, DB VẪN chặn. Đây là defense-in-depth — an toàn hơn so với chỉ filter ở tầng application.

### 3.3 Đánh Đổi

- RLS policy cần maintain cho mọi bảng mới → tự động hóa bằng migration script
- Debugging phức tạp hơn (cần biết tenant context) → log tenant_id trong mọi SQL log

---

## 4. Cache — Tại Sao Redis?

### 4.1 Bài toán cần Redis giải quyết

```
KHÔNG CÓ REDIS:
───────────────
Khách A → GET /public/vehicles?branch=X&date=...
         → SELECT * FROM vehicles WHERE ... (200ms)
Khách B → GET /public/vehicles?branch=X&date=...  (cùng query)
         → SELECT * FROM vehicles WHERE ... (200ms)
         1000 khách × 200ms = DB quá tải

CÓ REDIS:
─────────
Khách A → GET /public/vehicles → Redis (2ms, cache hit)
Khách B → GET /public/vehicles → Redis (2ms)
         Khi có booking mới → invalidate cache key → query DB 1 lần
         1000 khách × 2ms = nhẹ nhàng
```

### 4.2 Các use case Redis trong hệ thống

| Use case | Key pattern | TTL |
|----------|-------------|-----|
| Availability cache | `tenant:{id}:branch:{id}:avail:{date}` | 60s |
| Pricing cache | `tenant:{id}:pricing:{vehicleType}` | 300s |
| JWT blacklist | `jwt:invalid:{tokenHash}` | = token expiry |
| Rate limiting | `rate:{ip}:{endpoint}` | 60s |
| Session (tenant context verify) | `session:{userId}` | 1800s |

### 4.3 Tại sao không Memcached?

- Redis có data structure phong phú hơn (Sorted Set cho ranking, Hash cho object cache)
- Redis hỗ trợ pub/sub → dùng cho real-time notification sau này
- Redis có persistence → không mất cache khi restart

---

## 5. Message Queue — Tại Sao Kafka?

### 5.1 Thận trọng: Kafka là "nice to have" trong MVP, không phải "must have"

Trong MVP (vài chục tenant, vài trăm booking/ngày), Kafka chưa cần thiết. Nhưng nên thiết kế hệ thống sẵn sàng tích hợp:

```
CÁC EVENT SẼ DÙNG KAFKA (bắt đầu từ Phase 2):
────────────────────────────────────────────────
• booking.created → Gửi email xác nhận
• booking.cancelled → Giải phóng xe trong cache
• payment.completed → Cập nhật báo cáo doanh thu
• vehicle.status_changed → Invalid cache
• tenant.onboarded → Provision resources
```

### 5.2 Tại sao Kafka thay vì RabbitMQ?

| Tiêu chí | Kafka | RabbitMQ |
|----------|-------|----------|
| **Throughput** | Hàng triệu msg/s | Hàng chục nghìn msg/s |
| **Persistence** | Mặc định (log-based) | Tùy chọn |
| **Replay event** | CÓ (quan trọng cho audit) | KHÔNG |
| **Độ phức tạp vận hành** | CAO | TRUNG BÌNH |
| **Phù hợp** | Event sourcing, stream processing | Task queue, RPC |

Kafka phù hợp hơn vì:
- Audit trail: mọi thay đổi trạng thái booking cần được lưu vĩnh viễn
- Có thể replay event khi cần debug hoặc rebuild state
- Sau này scale lên microservices, event-driven architecture rất quan trọng

---

## 6. Frontend — Tại Sao Angular + Next.js?

### 6.1 Angular 17+ cho Admin Portal & Tenant Dashboard

**Lý do chọn Angular:**
- Dashboard nặng về form, bảng, CRUD — Angular reactive forms + RxJS là đáng tin cậy nhất
- Dependency injection của Angular giúp quản lý service layer (API client, auth interceptor) sạch sẽ
- Team Frontend Admin đã làm chủ Angular
- Angular Material / Ant Design cho Angular cung cấp UI component đầy đủ

### 6.2 Next.js 14 cho Customer Website

**Lý do chọn Next.js:**
- SEO quan trọng cho public website (khách Google search "thuê xe Đà Nẵng" phải ra web nhà xe)
- SSR/SSG cho trang catalog xe → load nhanh, SEO tốt
- App Router + Server Components giảm JS bundle gửi xuống client
- Team Frontend Customer đã làm chủ Next.js

### 6.3 Tại sao KHÔNG dùng 1 framework cho tất cả?

| Nếu dùng chung Angular | Nếu dùng chung Next.js |
|------------------------|------------------------|
| SEO kém cho public web | Dashboard phức tạp khó quản lý state |
| Bundle size lớn cho mobile | Thiếu reactive forms mạnh |
| Không SSR native | Thiếu DI pattern chuẩn |

→ **Dùng cả 2 là đánh đổi đúng:** mỗi framework cho đúng thế mạnh.

---

## 7. Infrastructure — Docker Compose Cho MVP

### 7.1 Lộ trình deployment

```
MVP (0→100 tenants):        Scale-up (100→1000):         Enterprise (1000+):
┌──────────────────┐        ┌──────────────────┐        ┌──────────────────┐
│ Docker Compose    │   →    │ Docker Swarm /    │   →   │ Kubernetes       │
│ 1 server          │        │ Kubernetes Lite   │        │ Cloud (AWS/GCP)  │
│ Manual deploy     │        │ CI/CD pipeline    │        │ Auto-scaling     │
│ Local + Staging   │        │ Multi-server      │        │ Multi-region     │
└──────────────────┘        └──────────────────┘        └──────────────────┘
```

### 7.2 Tại sao không lên Cloud ngay?

- **Chi phí:** VPS 300k-500k/tháng đủ chạy 100 tenant. Cloud (RDS + ElastiCache + ECS) ~$200-300/tháng.
- **Độ phức tạp:** Team 3 người part-time không đủ người quản lý hạ tầng cloud.
- **Thời gian:** Setup Docker Compose = 1 ngày. Setup production-grade K8s = 1-2 tuần.

---

## 8. Những Công Nghệ Không Sử Dụng Trong MVP

| Công nghệ | Lý do không dùng |
|-----------|-----------------|
| **GraphQL** | Overkill cho CRUD API. REST + Swagger đủ tốt, team FE đã quen REST. |
| **Microservices (MVP)** | 3 người part-time vận hành 10+ services = không khả thi. Modular monolith trước. |
| **NoSQL (MongoDB)** | Nghiệp vụ cho thuê xe có quan hệ chặt chẽ (xe → booking → khách → hóa đơn). RDBMS phù hợp hơn. |
| **gRPC** | Không cần throughput cực cao. REST/JSON dễ debug, dễ tích hợp FE. |
| **Serverless (Lambda)** | Cold start + multi-tenant context propagation phức tạp. Server truyền thống phù hợp hơn. |
| **OAuth2/OIDC (MVP)** | JWT stateless đủ cho MVP. OAuth2 thêm complexity không cần thiết. Để Phase 2 nếu cần SSO. |

---

## 9. Tóm Tắt Nguyên Tắc Chọn Công Nghệ

```
1. BATTLE-TESTED > TRENDY
   └── Chọn thứ đã được kiểm chứng, không chọn vì "hot"

2. TEAM SKILL > PERFECT TECH
   └── Công nghệ hiệu quả nhất = công nghệ team sử dụng được

3. SIMPLE FIRST, SCALE LATER
   └── Monolith trước, microservices sau. Docker Compose trước, K8s sau.

4. BUY vs BUILD
   └── Database isolation?  Dùng Postgres RLS (built-in), không tự build.

5. ENOUGH IS ENOUGH
   └── Đừng optimize cho 1000 tenant khi đang có 0 tenant.
```

---

*Xem tiếp:*
- [01 - Cắt gọt & Ưu tiên Tính năng](01-feature-prioritization.md)
- [02 - Phân tích Hành vi Khách Thuê Xe](02-customer-booking-behavior.md)
- [03 - So sánh 2 Loại hình Cho thuê](03-business-model-comparison.md)
- [05 - Roadmap Tái cấu trúc](05-roadmap-restructure.md)
