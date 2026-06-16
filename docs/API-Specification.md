# API Specification - Car Rental SaaS

## Mục lục
1. [Overview](#1-overview)
2. [Authentication](#2-authentication)
3. [Tenant APIs](#3-tenant-apis)
4. [Branch APIs](#4-branch-apis)
5. [Vehicle APIs](#5-vehicle-apis)
6. [Customer APIs](#6-customer-apis)
7. [Booking APIs](#7-booking-apis)
8. [Payment APIs](#8-payment-apis)
9. [Pricing APIs](#9-pricing-apis)
10. [Report APIs](#10-report-apis)

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

**Response:**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 3600,
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

**Request:**
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

---

## 3. Tenant APIs

### 3.1 Get Tenant Info

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

### 3.2 Update Tenant

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

## 4. Branch APIs

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

## 5. Vehicle APIs

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

## 6. Customer APIs

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

## 7. Booking APIs

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

## 8. Payment APIs

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

## 9. Pricing APIs

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
  "season": "normal",
  "multiplier": 1.2
}
```

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

## 10. Report APIs

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

## 11. Vehicle Transfer APIs

### 11.1 List Transfers

```
GET /transfers
```

**Query Parameters:**
- `status` (string): pending, in_transit, completed
- `fromBranchId`, `toBranchId` (UUID)

### 11.2 Create Transfer Request

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

### 11.3 Approve/Reject Transfer

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

## 12. Public APIs (Customer-facing)

### 12.1 Get Available Vehicles

```
GET /public/vehicles
```

**Query Parameters:**
- `branchId` (UUID): Required
- `pickupDate` (date): Required
- `returnDate` (date): Required
- `vehicleTypeId` (UUID): Optional

### 12.2 Calculate Price (Public)

```
GET /public/pricing?branchId=xxx&pickupDate=xxx&returnDate=xxx&vehicleTypeId=xxx
```

### 12.3 Create Public Booking

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
