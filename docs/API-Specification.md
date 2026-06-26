# API Specification - Car Rental SaaS

## Mục lục
1. [Overview](#1-overview)
2. [Authentication](#2-authentication)
3. [Super Admin APIs](#3-super-admin-apis)
4. [Tenant APIs](#4-tenant-apis)
5. [Branch APIs](#5-branch-apis)
6. [Vehicle APIs](#6-vehicle-apis)
7. [Customer APIs](#7-customer-apis)
8. [Booking APIs](#8-booking-apis)
9. [Payment APIs](#9-payment-apis)
10. [Pricing APIs](#10-pricing-apis)
11. [Report APIs](#11-report-apis)
12. [Vehicle Transfer APIs](#12-vehicle-transfer-apis)
13. [Public & Customer Portal APIs](#13-public--customer-portal-apis)

---

## 1. Overview

### 1.1 Base URL

```
Development: http://localhost:8080/api/v1
Staging: https://staging-api.carrental-saas.com/api/v1
Production: https://api.carrental-saas.com/api/v1
```

### 1.2 Headers

```
Content-Type: application/json
Authorization: Bearer <jwt_token>
X-Tenant-ID: <tenant_id>  (optional if subdomain used)
```

### 1.3 Response Format

```json
{
  "success": true,
  "data": { ... },
  "message": "Operation successful",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### 1.4 Error Response

```json
{
  "success": false,
  "error": {
    "code": "BOOKING_NOT_FOUND",
    "message": "Booking with ID xxx not found",
    "details": { ... }
  },
  "timestamp": "2024-01-15T10:30:00Z"
}
```

---

## 2. Authentication

### 2.1 Login

```
POST /auth/login
```

**Request:**
```json
{
  "email": "admin@tenantA.com",
  "password": "password123"
}
```

**Response (Tenant Admin / Staff):**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 900,
    "tenant": {
      "id": "uuid",
      "name": "RentCar VN",
      "plan": "PRO"
    },
    "user": {
      "id": "uuid",
      "email": "admin@tenantA.com",
      "name": "Nguyen Van A",
      "role": "TENANT_ADMIN"
    }
  }
}
```

**Response (Super Admin):**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 900,
    "tenant": null,
    "user": {
      "id": "uuid",
      "email": "superadmin@saas-carrental.com",
      "name": "Super Admin System",
      "role": "SUPER_ADMIN"
    }
  }
}
```

### 2.2 Register

```
POST /auth/register
```

**Request:**
```json
{
  "companyName": "RentCar VN",
  "email": "admin@rentcar-vn.com",
  "phone": "0901234567",
  "password": "password123",
  "fleetSize": "5-20"
}
```

### 2.3 Refresh Token

```
POST /auth/refresh
```

**Cơ chế:** Refresh token được lưu trong HttpOnly Cookie, không trả về trong JSON response). Client gọi endpoint này, cookie tự động được gửi kèm. Backend trả access token mới trong response body.

**Request:** Cookie `refresh_token` do browser tự động gửi kèm.

---

## 3. Super Admin APIs

Endpoints under `/api/v1/super-admin/**` are restricted to `SUPER_ADMIN` only.

### 3.1 List Tenants

```
GET /super-admin/tenants
```

**Query Parameters:**
- `page`, `size` (int): Pagination
- `search` (string): Search by tenant name or domain

**Response:**
```json
{
  "success": true,
  "data": {
    "content": [
      {
        "id": "uuid",
        "name": "RentCar VN",
        "domain": "rentcar-vn.com",
        "planTier": "PRO",
        "isActive": true,
        "createdAt": "2024-01-01T00:00:00Z"
      }
    ],
    "totalElements": 1,
    "totalPages": 1
  }
}
```

### 3.2 Update Tenant Status

```
PATCH /super-admin/tenants/{tenantId}/status
```

**Request:**
```json
{
  "isActive": false
}
```

### 3.3 Manage Subscriptions

```
POST /super-admin/subscriptions/billing-approval
```

**Request:**
```json
{
  "tenantId": "uuid",
  "approved": true,
  "transactionId": "SAAS-BILL-1002"
}
```

### 3.4 Get System Dashboard Stats

```
GET /super-admin/dashboard/stats
```

**Response:**
```json
{
  "success": true,
  "data": {
    "totalTenants": 120,
    "activeTenants": 115,
    "totalActiveVehicles": 2500,
    "monthlySystemRevenue": 150000000
  }
}
```

## 4. Tenant APIs

### 4.1 Get Tenant Info

```
GET /tenants/me
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "RentCar VN",
    "domain": "rentcar-vn.com",
    "plan": "PRO",
    "branchCount": 3,
    "vehicleCount": 25,
    "createdAt": "2024-01-01T00:00:00Z"
  }
}
```

### 4.2 Update Tenant

```
PUT /tenants/me
```

**Request:**
```json
{
  "name": "RentCar VN Updated",
  "logo": "https://...",
  "contactEmail": "contact@rentcar-vn.com"
}
```

---

## 5. Branch APIs

### 4.1 List Branches

```
GET /branches
```

**Query Parameters:**
- `page` (int): Page number (default: 0)
- `size` (int): Page size (default: 20)
- `isCentral` (boolean): Filter central branch

**Response:**
```json
{
  "success": true,
  "data": {
    "content": [
      {
        "id": "uuid",
        "name": "Chi nhánh Quận 1",
        "address": "123 Nguyen Hue, Q1, HCM",
        "phone": "0901234567",
        "isCentral": false,
        "vehicleCount": 10,
        "availableVehicleCount": 7
      }
    ],
    "totalElements": 3,
    "totalPages": 1,
    "currentPage": 0
  }
}
```

### 4.2 Create Branch

```
POST /branches
```

**Request:**
```json
{
  "name": "Chi nhánh Quận 1",
  "address": "123 Nguyen Hue, Q1, HCM",
  "phone": "0901234567",
  "isCentral": false
}
```

### 4.3 Get Branch

```
GET /branches/{branchId}
```

### 4.4 Update Branch

```
PUT /branches/{branchId}
```

### 4.5 Delete Branch

```
DELETE /branches/{branchId}
```

---

## 6. Vehicle APIs

### 5.1 List Vehicles

```
GET /vehicles
```

**Query Parameters:**
- `branchId` (UUID): Filter by branch
- `status` (string): available, rented, maintenance, transferred
- `vehicleTypeId` (UUID): Filter by type
- `page`, `size` (int): Pagination

**Response:**
```json
{
  "success": true,
  "data": {
    "content": [
      {
        "id": "uuid",
        "licensePlate": "ABC-123",
        "model": "Toyota Camry",
        "vehicleType": {
          "id": "uuid",
          "name": "Xe 4 chỗ"
        },
        "branch": {
          "id": "uuid",
          "name": "Chi nhánh Quận 1"
        },
        "status": "available",
        "createdAt": "2024-01-01T00:00:00Z"
      }
    ],
    "totalElements": 25,
    "totalPages": 2
  }
}
```

### 5.2 Create Vehicle

```
POST /vehicles
```

**Request:**
```json
{
  "licensePlate": "ABC-123",
  "model": "Toyota Camry",
  "vehicleTypeId": "uuid",
  "branchId": "uuid",
  "color": "Đen",
  "year": 2023,
  "description": "Xe mới, sạch đẹp"
}
```

### 5.3 Get Vehicle

```
GET /vehicles/{vehicleId}
```

### 5.4 Update Vehicle

```
PUT /vehicles/{vehicleId}
```

### 5.5 Update Vehicle Status

```
PATCH /vehicles/{vehicleId}/status
```

**Request:**
```json
{
  "status": "maintenance"
}
```

### 5.6 Delete Vehicle

```
DELETE /vehicles/{vehicleId}
```

---

## 7. Customer APIs

### 6.1 List Customers

```
GET /customers
```

**Query Parameters:**
- `page`, `size` (int): Pagination
- `search` (string): Search by name, phone, email

### 6.2 Create Customer

```
POST /customers
```

**Request:**
```json
{
  "name": "Nguyen Van B",
  "phone": "0909876543",
  "email": "nguyenvanb@email.com",
  "address": "456 Le Lai, Q1, HCM",
  "idCard": "123456789",
  "driverLicense": "DL123456"
}
```

### 6.3 Get Customer

```
GET /customers/{customerId}
```

### 6.4 Get Customer Rental History

```
GET /customers/{customerId}/rentals
```

---

## 8. Booking APIs

### 7.1 List Bookings

```
GET /bookings
```

**Query Parameters:**
- `branchId` (UUID): Filter by branch
- `status` (string): pending, confirmed, in_progress, completed, cancelled
- `fromDate`, `toDate` (date): Filter by date range
- `page`, `size` (int): Pagination

### 7.2 Create Booking

```
POST /bookings
```

**Request:**
```json
{
  "branchId": "uuid",
  "customerId": "uuid",
  "vehicleId": "uuid",
  "pickupDate": "2024-01-20",
  "returnDate": "2024-01-22",
  "depositAmount": 500000,
  "paymentMethod": "bank_transfer"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "bookingCode": "CR-2024-0001",
    "customer": {
      "id": "uuid",
      "name": "Nguyen Van B"
    },
    "vehicle": {
      "id": "uuid",
      "licensePlate": "ABC-123",
      "model": "Toyota Camry"
    },
    "branch": {
      "id": "uuid",
      "name": "Chi nhánh Quận 1"
    },
    "pickupDate": "2024-01-20",
    "returnDate": "2024-01-22",
    "status": "pending",
    "pricing": {
      "basePrice": 500000,
      "dayType": "weekday",
      "days": 2,
      "subtotal": 1000000,
      "total": 1000000
    },
    "depositAmount": 500000,
    "depositPaid": true,
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

### 7.3 Get Booking

```
GET /bookings/{bookingId}
```

### 7.4 Update Booking Status

```
PATCH /bookings/{bookingId}/status
```

**Request:**
```json
{
  "status": "confirmed"
}
```

### 7.5 Cancel Booking

```
POST /bookings/{bookingId}/cancel
```

**Request:**
```json
{
  "reason": "Khách hủy"
}
```

### 7.6 Start Rental (Giao xe)

```
POST /bookings/{bookingId}/start
```

**Request:**
```json
{
  "initialKm": 15000,
  "fuelLevel": "full",
  "notes": "Xe sạch sẽ"
}
```

### 7.7 Complete Rental (Trả xe)

```
POST /bookings/{bookingId}/complete
```

**Request:**
```json
{
  "finalKm": 15200,
  "fuelLevel": "half",
  "notes": "Khách trả đúng hẹn",
  "lateFee": 0,
  "damageFee": 0
}
```

---

## 9. Payment APIs

### 8.1 List Payments

```
GET /payments
```

**Query Parameters:**
- `bookingId` (UUID): Filter by booking
- `method` (string): cash, bank_transfer, e_wallet
- `fromDate`, `toDate` (date): Filter by date

### 8.2 Create Payment

```
POST /payments
```

**Request:**
```json
{
  "bookingId": "uuid",
  "amount": 500000,
  "method": "bank_transfer",
  "transactionId": "MB123456789",
  "paidAt": "2024-01-15T10:30:00Z"
}
```

### 8.3 Get Payment

```
GET /payments/{paymentId}
```

---

## 10. Pricing APIs

### 9.1 List Pricing Rules

```
GET /pricing-rules
```

### 9.2 Create Pricing Rule

```
POST /pricing-rules
```

**Request:**
```json
{
  "vehicleTypeId": "uuid",
  "dayType": "weekend",
  "multiplier": 1.2
}
```
**MVP:** Chỉ hỗ trợ `weekday` (1.0) và `weekend` (1.2). Season/holiday multipliers sẽ có ở Phase 2.

### 9.3 Calculate Price

```
POST /pricing/calculate
```

**Request:**
```json
{
  "vehicleTypeId": "uuid",
  "pickupDate": "2024-01-20",
  "returnDate": "2024-01-22"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "breakdown": [
      {
        "date": "2024-01-20",
        "dayType": "weekend",
        "season": "normal",
        "basePrice": 500000,
        "multiplier": 1.2,
        "price": 600000
      },
      {
        "date": "2024-01-21",
        "dayType": "weekend",
        "season": "normal",
        "basePrice": 500000,
        "multiplier": 1.2,
        "price": 600000
      }
    ],
    "totalDays": 2,
    "totalPrice": 1200000
  }
}
```

---

## 11. Report APIs

### 10.1 Revenue Report

```
GET /reports/revenue
```

**Query Parameters:**
- `fromDate` (date): Start date
- `toDate` (date): End date
- `branchId` (UUID): Filter by branch (optional)
- `groupBy` (string): day, month, branch

**Response:**
```json
{
  "success": true,
  "data": {
    "summary": {
      "totalRevenue": 50000000,
      "totalBookings": 50,
      "averageBookingValue": 1000000
    },
    "breakdown": [
      {
        "date": "2024-01-15",
        "revenue": 5000000,
        "bookings": 5
      }
    ]
  }
}
```

### 10.2 Fleet Report

```
GET /reports/fleet
```

**Response:**
```json
{
  "success": true,
  "data": {
    "totalVehicles": 25,
    "available": 15,
    "rented": 8,
    "maintenance": 2,
    "utilizationRate": 32
  }
}
```

### 10.3 Customer Report

```
GET /reports/customers
```

---

## 12. Vehicle Transfer APIs

**Lùi Phase 2.** MVP không cần module điều phối xe liên chi nhánh. API spec bên dưới giữ làm tham khảo cho Phase 2.

### 12.1 List Transfers

```
GET /transfers
```

**Query Parameters:**
- `status` (string): pending, in_transit, completed
- `fromBranchId`, `toBranchId` (UUID)

### 12.2 Create Transfer Request

```
POST /transfers
```

**Request:**
```json
{
  "vehicleId": "uuid",
  "toBranchId": "uuid",
  "reason": "Chi nhánh thiếu xe"
}
```

### 12.3 Approve/Reject Transfer

```
PATCH /transfers/{transferId}/status
```

**Request:**
```json
{
  "status": "in_transit"
}
```

---

## 13. Public & Customer Portal APIs

These endpoints are exposed under `/api/v1/public/**` and are consumed by the unified **Customer Website**.

### 13.1 Get Available Vehicles (Public Search)

```
GET /public/vehicles
```

**Query Parameters:**
- `branchId` (UUID): Required
- `pickupDate` (date): Required
- `returnDate` (date): Required
- `vehicleTypeId` (UUID): Optional

### 13.2 Calculate Price (Public)

```
GET /public/pricing?branchId=xxx&pickupDate=xxx&returnDate=xxx&vehicleTypeId=xxx
```

### 13.3 Create Booking (Public / Guest checkout)

```
POST /public/bookings
```

**Request:**
```json
{
  "branchId": "uuid",
  "vehicleTypeId": "uuid",
  "customer": {
    "name": "Nguyen Van B",
    "phone": "0909876543",
    "email": "nguyenvanb@email.com"
  },
  "pickupDate": "2024-01-20",
  "returnDate": "2024-01-22"
}
```

### 13.4 Customer Login (Portal access)

```
POST /public/customer/login
```

**Response:**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
    "user": {
      "id": "uuid",
      "email": "customer@email.com",
      "name": "Nguyen Van B",
      "role": "CUSTOMER"
    }
  }
}
```

### 13.5 Get My Rental History (Portal Dashboard)

Requires token authentication.

```
GET /public/customer/me/bookings
```

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "bookingCode": "CR-2024-0001",
      "pickupDate": "2024-01-20",
      "returnDate": "2024-01-22",
      "totalAmount": 1200000,
      "status": "completed"
    }
  ]
}
```
