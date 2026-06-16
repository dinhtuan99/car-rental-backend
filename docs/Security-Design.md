# Security Design - Car Rental SaaS

## Mục lục
1. [Security Overview](#1-security-overview)
2. [Authentication Design](#2-authentication-design)
3. [Authorization Design](#3-authorization-design)
4. [Multi-tenant Isolation](#4-multi-tenant-isolation)
5. [Token Management](#5-token-management)
6. [API Security](#6-api-security)
7. [Data Security](#7-data-security)
8. [Security Checklist](#8-security-checklist)

---

## 1. Security Overview

### 1.1 Security Goals

| Goal | Description |
|------|-------------|
| **Authentication** | Verify user identity |
| **Authorization** | Control access to resources |
| **Tenant Isolation** | Prevent cross-tenant data access |
| **Data Protection** | Encrypt sensitive data at rest and in transit |
| **Audit Logging** | Track all security-relevant events |

### 1.2 Security Stack

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Security Stack                                      │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    Presentation Layer                                 │    │
│  │  - HTTPS/TLS 1.3                                                     │    │
│  │  - CORS Configuration                                                 │    │
│  │  - Content Security Policy (CSP)                                      │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    Application Layer                                  │    │
│  │  - JWT Authentication                                                │    │
│  │  - Spring Security                                                   │    │
│  │  - Role-based Access Control (RBAC)                                   │    │
│  │  - Tenant Context Isolation                                          │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    Data Layer                                       │    │
│  │  - PostgreSQL Row Level Security (RLS)                              │    │
│  │  - Encryption at rest (AES-256)                                      │    │
│  │  - Password Hashing (BCrypt)                                         │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Authentication Design

### 2.1 Authentication Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      Authentication Flow                                    │
│                                                                              │
│  ┌──────────┐         ┌──────────┐         ┌──────────┐                │
│  │  Client  │         │   API    │         │   DB    │                │
│  └────┬─────┘         └────┬─────┘         └────┬─────┘                │
│       │                    │                    │                        │
│       │  1. POST /auth/login                     │                        │
│       │  {email, password}                        │                        │
│       │ ───────────────────────────────────────► │                        │
│       │                    │                    │                        │
│       │                    │  2. Find user by email                       │
│       │                    │ ──────────────────────────────────────────► │
│       │                    │                    │                        │
│       │                    │  3. Verify password (BCrypt)                 │
│       │                    │ ◄────────────────────────────────────────── │
│       │                    │                    │                        │
│       │                    │  4. Generate JWT tokens                      │
│       │                    │     - access_token (15 min)                  │
│       │                    │     - refresh_token (7 days)                 │
│       │                    │                    │                        │
│       │  5. Response       │                    │                        │
│       │  {access_token,   │                    │                        │
│       │   refresh_token,  │                    │                        │
│       │   user, tenant}    │                    │                        │
│       │ ◄────────────────────────────────────── │                        │
│       │                    │                    │                        │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 JWT Token Structure

#### Access Token Payload
```json
{
  "sub": "user-uuid",
  "tenant_id": "tenant-uuid",
  "email": "admin@tenantA.com",
  "role": "TENANT_ADMIN",
  "type": "access",
  "iat": 1704067200,
  "exp": 1704068100
}
```

#### Super Admin Access Token Payload
```json
{
  "sub": "super-admin-uuid",
  "tenant_id": null,
  "email": "superadmin@saas-carrental.com",
  "role": "SUPER_ADMIN",
  "type": "access",
  "iat": 1704067200,
  "exp": 1704068100
}
```

#### Refresh Token Payload
```json
{
  "sub": "user-uuid",
  "tenant_id": "tenant-uuid",
  "type": "refresh",
  "jti": "unique-token-id",
  "iat": 1704067200,
  "exp": 1704672000
}
```

### 2.3 Token Configuration

| Token | Expiration | Storage | Purpose |
|-------|------------|---------|---------|
| Access Token | 15 minutes | Memory (JS) | API authentication |
| Refresh Token | 7 days | HttpOnly Cookie | Get new access token |

### 2.4 Spring Security Configuration

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .cors(cors -> cors.configurationSource(corsConfigurationSource()))
            .sessionManagement(session ->
                session.sessionCreationPolicy(STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/v1/auth/**").permitAll()
                .requestMatchers("/api/v1/public/**").permitAll()
                .requestMatchers("/api/v1/super-admin/**").hasRole("SUPER_ADMIN")
                .requestMatchers("/api/v1/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
}
```

---

## 3. Authorization Design

### 3.1 Role Hierarchy

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Role Hierarchy                                     │
│                                                                              │
│                         SUPER_ADMIN                                         │
│                    (Global SaaS Operations)                                 │
│                           │                                                 │
│                           ▼                                                 │
│                         TENANT_ADMIN                                        │
│                    (Full access to tenant)                                  │
│                           │                                                 │
│           ┌───────────────┼───────────────┐                                │
│           │               │               │                                │
│           ▼               ▼               ▼                                │
│    CENTRAL_MANAGER   BRANCH_MANAGER    STAFF                              │
│    (All branches)    (One branch)    (Limited)                            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Role Permissions

| Role | Tenant Data | Branch Data | Booking | Payment | Report | Settings | System Config |
|------|-------------|------------|---------|---------|--------|----------|---------------|
| SUPER_ADMIN | RW (all) | RW (all) | RW (all) | RW (all) | RW (all) | RW (all) | RW |
| TENANT_ADMIN | RW | RW | RW | RW | RW | RW | - |
| CENTRAL_MANAGER | R | RW (all) | RW | R | RW | - | - |
| BRANCH_MANAGER | R | RW (own) | RW | R | R | - | - |
| STAFF | R | R (own) | RW (limited) | R | - | - | - |

### 3.3 Method-Level Security

```java
@Service
public class BookingService {

    @PreAuthorize("hasAnyRole('TENANT_ADMIN', 'CENTRAL_MANAGER', 'BRANCH_MANAGER', 'STAFF')")
    public Booking createBooking(CreateBookingRequest request) {
        // Only authenticated users with proper role can create booking
    }

    @PreAuthorize("hasAnyRole('TENANT_ADMIN', 'CENTRAL_MANAGER', 'BRANCH_MANAGER')")
    public Booking cancelBooking(UUID bookingId, String reason) {
        // Only managers can cancel bookings
    }

    @PreAuthorize("hasRole('TENANT_ADMIN')")
    public void deleteBooking(UUID bookingId) {
        // Only tenant admin can delete bookings
    }

    @PreAuthorize("hasRole('SUPER_ADMIN')")
    public void deactivateTenant(UUID tenantId) {
        // Only Super Admin can deactivate a tenant
    }
}
```

---

## 4. Multi-tenant Isolation

### 4.1 Isolation Strategy

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Multi-tenant Isolation Flow                              │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     JWT Token Contains:                              │    │
│  │  - tenant_id (UUID, nullable for SUPER_ADMIN)                        │    │
│  │  - user_id (UUID)                                                    │    │
│  │  - role (string)                                                      │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                              │
│                              ▼                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                  JwtAuthenticationFilter                           │    │
│  │  1. Extract token from Authorization header                        │    │
│  │  2. Validate token signature & expiration                           │    │
│  │  3. Extract tenant_id from token claims                             │    │
│  │  4. If tenant_id != null, set TenantContext.setCurrentTenant(id)   │    │
│  │     Else if role == 'SUPER_ADMIN', TenantContext.setTenantId(null)   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                              │
│                              ▼                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                  TenantContext (ThreadLocal)                        │    │
│  │                                                                      │    │
│  │  public class TenantContext {                                        │    │
│  │      private static final ThreadLocal<UUID> currentTenant =           │    │
│  │          new ThreadLocal<>();                                        │    │
│  │                                                                      │    │
│  │      public static void setTenantId(UUID tenantId) {                  │    │
│  │          currentTenant.set(tenantId);                               │    │
│  │      }                                                               │    │
│  │                                                                      │    │
│  │      public static UUID getTenantId() {                              │    │
│  │          return currentTenant.get();                                 │    │
│  │      }                                                               │    │
│  │  }                                                                   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                              │
│                              ▼                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                  BaseRepository Implementation                       │    │
│  │                                                                      │    │
│  │  @Repository                                                         │    │
│  │  public interface VehicleRepository extends JpaRepository<Vehicle,   │    │
│  │          JpaSpecificationExecutor<Vehicle> {                         │    │
│  │                                                                      │    │
│  │      default Specification<Vehicle> withTenant(UUID tenantId) {     │    │
│  │          return (root, query, cb) -> {                               │    │
│  │              if (tenantId == null) return cb.conjunction(); // Bypassed│    │
│  │              return cb.equal(root.get("tenantId"), tenantId);        │    │
│  │          };                                                          │    │
│  │      }                                                               │    │
│  │  }                                                                   │    │
│  │                                                                      │    │
│  │  // In service:                                                      │    │
│  │  public List<Vehicle> findAllVehicles() {                            │    │
│  │      UUID tenantId = TenantContext.getTenantId();                     │    │
│  │      return vehicleRepository.findAll(withTenant(tenantId));        │    │
│  │  }                                                                   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 4.2 Repository Pattern with Tenant Filter

```java
// BaseRepositoryWithTenant.java
public interface BaseRepositoryWithTenant<T> {
    default Specification<T> withTenant(UUID tenantId) {
        return (root, query, cb) -> cb.equal(root.get("tenantId"), tenantId);
    }

    default Specification<T> withTenantAndId(UUID tenantId, UUID id) {
        return withTenant(tenantId).and(
            (root, query, cb) -> cb.equal(root.get("id"), id)
        );
    }
}

// VehicleRepository.java
@Repository
public interface VehicleRepository extends JpaRepository<Vehicle, UUID>,
        JpaSpecificationExecutor<Vehicle>,
        BaseRepositoryWithTenant<Vehicle> {

    Optional<Vehicle> findByIdAndTenantId(UUID id, UUID tenantId);

    List<Vehicle> findByTenantIdAndBranchId(UUID tenantId, UUID branchId);

    List<Vehicle> findByTenantIdAndStatus(UUID tenantId, VehicleStatus status);
}
```

### 4.3 AOP Aspect for Tenant Validation

```java
@Aspect
@Component
public class TenantAccessAspect {

    @Around("execution(* com.carrental.repository.*.*(..))")
    public Object validateTenantAccess(ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        String methodName = signature.getName();

        // Auto-add tenant filter for list/find methods
        if (methodName.startsWith("find") || methodName.startsWith("get") ||
            methodName.startsWith("list") || methodName.startsWith("count")) {

            UUID currentTenant = TenantContext.getTenantId();
            if (currentTenant == null) {
                throw new TenantAccessDeniedException("No tenant context");
            }
        }

        return joinPoint.proceed();
    }
}
```

---

## 5. Token Management

### 5.1 Token Storage

| Storage | Access | XSS Safe | CSRF Safe | Use Case |
|---------|--------|----------|-----------|----------|
| **Memory** | JS only | ✅ | ✅ | Access token |
| **HttpOnly Cookie** | Server only | ✅ | ✅ | Refresh token |
| **LocalStorage** | JS | ❌ | ✅ | Not recommended |
| **SessionStorage** | JS | ❌ | ✅ | Not recommended |

### 5.2 Token Refresh Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      Token Refresh Flow                                     │
│                                                                              │
│  Client                          API                         Database        │
│    │                              │                              │            │
│    │  1. Access Token Expired      │                              │            │
│    │ ─────────────────────────►   │                              │            │
│    │                              │                              │            │
│    │  2. POST /auth/refresh       │                              │            │
│    │  {refresh_token}             │                              │            │
│    │ ─────────────────────────►   │                              │            │
│    │                              │                              │            │
│    │                              │  3. Validate refresh token   │            │
│    │                              │ ───────────────────────────► │            │
│    │                              │                              │            │
│    │                              │  4. Check if revoked        │            │
│    │                              │ ───────────────────────────► │            │
│    │                              │                              │            │
│    │                              │  5. Generate new tokens     │            │
│    │                              │                              │            │
│    │  6. New tokens              │                              │            │
│    │ ◄─────────────────────────  │                              │            │
│    │                              │                              │            │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 5.3 Token Revocation

```java
@Service
public class TokenService {

    // In-memory set for revoked tokens (use Redis for production)
    private final Set<String> revokedTokens = new HashSet<>();

    public void revokeToken(String token) {
        revokedTokens.add(token);
    }

    public boolean isTokenRevoked(String token) {
        return revokedTokens.contains(token);
    }

    public void revokeAllUserTokens(UUID userId) {
        // In production, use Redis with userId as key
        // Revoke all tokens associated with user
    }
}
```

---

## 6. API Security

### 6.1 API Security Headers

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
            .allowedOrigins("https://app.carrental-saas.com")
            .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS")
            .allowedHeaders("*")
            .allowCredentials(true)
            .maxAge(3600);
    }
}

// Security headers filter
@Component
public class SecurityHeadersFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) {
        response.setHeader("X-Content-Type-Options", "nosniff");
        response.setHeader("X-Frame-Options", "DENY");
        response.setHeader("X-XSS-Protection", "1; mode=block");
        response.setHeader("Strict-Transport-Security", "max-age=31536000; includeSubDomains");
        response.setHeader("Content-Security-Policy", "default-src 'self'");
        filterChain.doFilter(request, response);
    }
}
```

### 6.2 Rate Limiting

```java
@Component
public class RateLimitingFilter extends OncePerRequestFilter {

    private final Map<String, Counter> requestCounts = new ConcurrentHashMap<>();
    private static final int MAX_REQUESTS_PER_MINUTE = 60;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) {
        String clientId = getClientId(request);
        Counter counter = requestCounts.computeIfAbsent(clientId, k -> new Counter());

        if (counter.increment() > MAX_REQUESTS_PER_MINUTE) {
            response.setStatus(429);
            response.getWriter().write("Too many requests");
            return;
        }

        filterChain.doFilter(request, response);
    }
}
```

### 6.3 Input Validation

```java
@Data
public class CreateBookingRequest {

    @NotNull(message = "Branch ID is required")
    private UUID branchId;

    @NotNull(message = "Customer ID is required")
    private UUID customerId;

    @NotNull(message = "Vehicle ID is required")
    private UUID vehicleId;

    @NotNull(message = "Pickup date is required")
    @FutureOrPresent(message = "Pickup date must be today or in the future")
    private LocalDate pickupDate;

    @NotNull(message = "Return date is required")
    private LocalDate returnDate;

    @Positive(message = "Deposit amount must be positive")
    private BigDecimal depositAmount;
}
```

---

## 7. Data Security

### 7.1 Password Hashing

```java
@Service
public class PasswordService {

    private final PasswordEncoder passwordEncoder = new BCryptPasswordEncoder(12);

    public String encode(String rawPassword) {
        return passwordEncoder.encode(rawPassword);
    }

    public boolean matches(String rawPassword, String encodedPassword) {
        return passwordEncoder.matches(rawPassword, encodedPassword);
    }
}
```

### 7.2 Sensitive Data Encryption

```java
@Component
public class EncryptionService {

    private final AESEncryption encryption = new AESEncryption(
        System.getenv("ENCRYPTION_KEY")
    );

    // Encrypt sensitive fields before saving
    public String encrypt(String plainText) {
        return encryption.encrypt(plainText);
    }

    // Decrypt when reading
    public String decrypt(String cipherText) {
        return encryption.decrypt(cipherText);
    }
}

// Apply to sensitive fields
@Entity
public class Customer {
    // Store encrypted
    @Convert(converter = AESEncryptionConverter.class)
    private String idCardNumber;

    @Convert(converter = AESEncryptionConverter.class)
    private String driverLicense;
}
```

### 7.3 Audit Logging

```java
@Aspect
@Component
public class AuditAspect {

    @Autowired
    private AuditLogService auditLogService;

    @AfterReturning(pointcut = "execution(* com.carrental.service.*.*(..))",
                     returning = "result")
    public void logAfter(JoinPoint joinPoint, Object result) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        String methodName = signature.getName();
        String className = signature.getDeclaringType().getSimpleName();

        // Log security-relevant operations
        if (methodName.contains("delete") || methodName.contains("update") ||
            methodName.contains("create")) {
            auditLogService.log(new AuditEvent(
                TenantContext.getTenantId(),
                TenantContext.getUserId(),
                className + "." + methodName,
                "SUCCESS"
            ));
        }
    }
}
```

---

## 8. Security Checklist

### 8.1 Pre-Production Security Checklist

| Category | Item | Status |
|----------|------|--------|
| **Authentication** | JWT tokens with proper expiration | ☐ |
| | Password hashing with BCrypt (cost factor 12+) | ☐ |
| | Secure token storage (HttpOnly cookies) | ☐ |
| | Token refresh mechanism | ☐ |
| | Account lockout after failed attempts | ☐ |
| **Authorization** | Role-based access control | ☐ |
| | Method-level security annotations | ☐ |
| | Tenant isolation verified | ☐ |
| **API Security** | HTTPS enforced | ☐ |
| | CORS properly configured | ☐ |
| | Rate limiting in place | ☐ |
| | Input validation on all endpoints | ☐ |
| | Security headers set | ☐ |
| **Data Security** | Sensitive data encrypted at rest | ☐ |
| | Database RLS enabled | ☐ |
| | Audit logging implemented | ☐ |
| **Infrastructure** | Secrets in environment variables | ☐ |
| | No secrets in code | ☐ |
| | Security updates applied | ☐ |

### 8.2 Security Test Cases

| Test | Expected Result |
|-----|----------------|
| Access another tenant's data | Denied, 403 Forbidden |
| Use expired token | 401 Unauthorized |
| SQL injection in search field | Sanitized, no effect |
| XSS in customer name | Escaped, no script execution |
| Brute force login | Account locked after 5 attempts |
| Access admin endpoint as staff | 403 Forbidden |
| Modify booking of another tenant | Denied |

---

## Appendix: Security Configuration Reference

### Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `JWT_SECRET` | JWT signing key (min 256 bits) | `your-256-bit-secret` |
| `JWT_EXPIRATION` | Access token expiration (ms) | `900000` (15 min) |
| `JWT_REFRESH_EXPIRATION` | Refresh token expiration (ms) | `604800000` (7 days) |
| `ENCRYPTION_KEY` | AES encryption key | `your-32-byte-key` |
| `DATABASE_PASSWORD` | Database password | `strong-password` |

### Key Files

| File | Purpose |
|------|---------|
| `SecurityConfig.java` | Spring Security configuration |
| `JwtTokenProvider.java` | JWT generation and validation |
| `JwtAuthenticationFilter.java` | JWT extraction and authentication |
| `TenantContext.java` | ThreadLocal tenant storage |
| `BaseRepositoryWithTenant.java` | Tenant filtering interface |
| `PasswordService.java` | Password hashing |
| `EncryptionService.java` | Sensitive data encryption |
