# Công Nghệ Sau MVP (Phase 2+)

**Ngày:** 26/06/2026
**Tham chiếu:** [Roadmap.md](Roadmap.md#kế-hoạch-sau-mvp-phase-2), [Tech-Trends.md](Tech-Trends.md), [Định hướng công nghệ](analysis/04-technology-direction.md)

---

## Nguyên Tắc

Sau khi ổn định với 50+ tenant trả phí, mở rộng công nghệ theo nguyên tắc:

1. **Battle-tested > Trendy** — Chọn thứ đã được kiểm chứng, không chọn vì "hot"
2. **Team skill > Perfect tech** — Công nghệ hiệu quả nhất = công nghệ team sử dụng được
3. **Simple first, scale later** — Chỉ thêm khi MVP hiện tại không đáp ứng được
4. **Buy vs Build** — Tận dụng managed service, không tự build hạ tầng

---

## 1. Subscription Plans Đa Bậc (BASIC/PRO/ENTERPRISE)

**Thời gian:** 2 tuần | **Điều kiện:** Có 50+ tenant trả phí

| Công nghệ | Mục đích | Ghi chú |
|-----------|----------|---------|
| **Stripe Billing / Paddle** | Recurring payment, invoice tự động, proration khi nâng/hạ cấp | Paddle phù hợp hơn nếu target thị trường VN (hỗ trợ local payment) |
| **Feature Flags (LaunchDarkly hoặc custom)** | Bật/tắt tính năng theo gói cước không cần deploy | Bắt đầu với custom implementation, chuyển LaunchDarkly khi cần A/B testing |
| **Redis + Lua scripting** | Đếm usage quota (số xe, booking, user) theo tenant real-time | Tránh race condition khi đếm concurrent |

---

## 2. Module Xe Có Lái (Driver + Tour + Dispatch)

**Thời gian:** 6-8 tuần | **Điều kiện:** Có tenant cần quản lý tài xế + tour

| Công nghệ | Mục đích | Ghi chú |
|-----------|----------|---------|
| **Google Maps Platform / Mapbox** | Real-time GPS tracking, geofencing, ETA calculation | Mapbox rẻ hơn cho scale nhỏ |
| **WebSocket (Spring WebSocket + STOMP)** | Giao tiếp real-time khách - tài xế - dispatch | Thay thế polling REST |
| **Kafka** | Event sourcing cho dispatch flow: `driver.assigned` → `en_route` → `trip.completed` | Audit trail + replay capability |
| **Elasticsearch** | Tìm kiếm tài xế theo khu vực, rating, loại xe | Có thể dùng PostgreSQL full-text search nếu <1000 tài xế |

---

## 3. Ứng Dụng Mobile cho Tenant (iOS/Android)

**Thời gian:** 8-10 tuần | **Điều kiện:** Nhà xe yêu cầu quản lý qua mobile

| Công nghệ | Mục đích | Ghi chú |
|-----------|----------|---------|
| **React Native (Expo)** | 1 codebase iOS + Android | Team Next.js tận dụng được React knowledge |
| **Firebase Cloud Messaging (FCM) / APNs** | Push notification: booking mới, xe sắp quá hạn | Alternating: Twilio Notify |
| **React Native Maps** | Hiển thị fleet map, vị trí xe đang thuê | |
| **React Native Vision Camera** | Chụp CCCD/GPLX, biên bản giao nhận xe | OCR tích hợp sau |

---

## 4. API Tích Hợp Bên Thứ 3

**Thời gian:** 4 tuần | **Điều kiện:** Có đối tác tích hợp (bảo hiểm, cổng thanh toán)

| Công nghệ | Mục đích | Ghi chú |
|-----------|----------|---------|
| **OAuth 2.0 / OpenID Connect (OIDC)** | SSO cho enterprise, social login (Google/Facebook) | Spring Security hỗ trợ sẵn |
| **Spring Cloud Gateway** | API gateway: rate limiting, auth, routing cho partner API | Thay thế Nginx manual config |
| **HashiCorp Vault** | Quản lý API key/secrets bên thứ 3 | Tránh hardcode secret trong config |
| **Webhook Signature (HMAC)** | Xác thực callback từ cổng thanh toán, bảo hiểm | |

---

## 5. Migration Monolith → Microservices (Liên Tục)

**Thời gian:** Liên tục | **Điều kiện:** Monolith có dấu hiệu quá tải (response >2s, DB connection pool cạn)

| Công nghệ | Mục đích | Ghi chú |
|-----------|----------|---------|
| **Spring Cloud (Discovery, Config)** | Service registry, centralized config | Chỉ dùng khi có >3 services |
| **Docker Swarm (bước đệm)** | Multi-server orchestration đơn giản | Trước khi lên K8s |
| **Kubernetes** | Orchestration, auto-scaling, rolling update | Chỉ khi team >5 người + có DevOps |
| **Prometheus + Grafana + Loki** | Monitoring, alerting, log aggregation | Thay thế basic health check |
| **OpenTelemetry / Jaeger** | Distributed tracing giữa các service | Debug latency cross-service |
| **gRPC (service-to-service)** | High-throughput internal communication | Chỉ những service cần <10ms latency |

---

## Công Nghệ Dài Hạn (Cần Đánh Giá Thêm)

Chưa có trong lộ trình, cân nhắc khi đạt scale tương ứng:

| Công nghệ | Khi nào cân nhắc | Giá trị mang lại |
|-----------|-----------------|-----------------|
| **AI Dynamic Pricing Engine** | >20 tenant, đủ dữ liệu booking để train | Tăng 20-30% revenue |
| **IoT OBD-II Integration** | Tenant chạy fleet >100 xe | Tự động cập nhật km, nhiên liệu, cảnh báo bảo dưỡng |
| **Smart Lock / Keyless Entry** | Nhu cầu self-service 24/7 | Không cần nhân viên giao xe |
| **Digital Contract + E-Signature** | Yêu cầu hợp đồng pháp lý | Paperless, fast |
| **AI Chatbot (LLM)** | >500 booking/ngày cần hỗ trợ | Giảm 50% support cost |
| **GraphQL (BFF layer)** | Mobile app cần flexible data fetching | Tránh over-fetching, giảm round-trip |
| **Blockchain Contract** | Cần minh bạch, chống chỉnh sửa hợp đồng | Còn xa, đánh giá lại sau |

---

## Lộ Trình Áp Dụng

```
MVP ỔN ĐỊNH (50+ tenant)
│
├── Priority 1: Subscription Plans (2 tuần)
│   └── Stripe/Paddle + Feature Flags + Redis quota
│
├── Priority 2: Module Xe Có Lái (6-8 tuần)
│   └── Google Maps + WebSocket + Kafka + Elasticsearch
│
├── Priority 3: Mobile App (8-10 tuần)
│   └── React Native + FCM + Maps + Camera
│
├── Priority 4: Tích hợp bên thứ 3 (4 tuần)
│   └── OAuth2/OIDC + API Gateway + Vault + Webhook
│
└── Priority 5: Microservices (liên tục)
    └── Spring Cloud → K8s → Prometheus → Tracing
```

---

*Tài liệu bổ sung cho [Roadmap.md — Kế Hoạch Sau MVP](Roadmap.md#kế-hoạch-sau-mvp-phase-2).*
