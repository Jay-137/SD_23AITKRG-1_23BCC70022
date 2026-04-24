# Food Delivery System - API Design

## 1. API Design Principles

* RESTful design
* Versioned APIs
* JSON request/response
* Idempotency for critical operations
* Proper status codes and error handling

Base URL:

```
/api/v1
```

---

## 2. Authentication APIs

### Register User

```
POST /api/v1/auth/register
```

Request:

```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "phone": "9999999999",
  "password": "securepassword"
}
```

Response:

```json
{
  "user_id": 101,
  "message": "User registered successfully"
}
```

---

### Login

```
POST /api/v1/auth/login
```

Request:

```json
{
  "email": "john@example.com",
  "password": "securepassword"
}
```

Response:

```json
{
  "token": "jwt_token_here"
}
```

---

## 3. User APIs

### Get Profile

```
GET /api/v1/users/profile
```

Headers:

```
Authorization: Bearer <token>
```

Response:

```json
{
  "user_id": 101,
  "name": "John Doe",
  "email": "john@example.com"
}
```

---

### Add Address

```
POST /api/v1/users/addresses
```

Request:

```json
{
  "address_line": "Street 1",
  "city": "Delhi",
  "pincode": "110001",
  "latitude": 28.61,
  "longitude": 77.20
}
```

---

## 4. Restaurant APIs

### Get Restaurants

```
GET /api/v1/restaurants
```

Query Params:

```
?city=Delhi&rating=4
```

Response:

```json
[
  {
    "restaurant_id": 201,
    "name": "Food Hub",
    "rating": 4.5
  }
]
```

---

### Get Menu

```
GET /api/v1/restaurants/{restaurant_id}/menu
```

Response:

```json
[
  {
    "item_id": 1,
    "name": "Burger",
    "price": 120
  }
]
```

---

## 5. Order APIs

### Create Order

```
POST /api/v1/orders
```

Headers:

```
Authorization: Bearer <token>
Idempotency-Key: unique-request-id
```

Request:

```json
{
  "restaurant_id": 201,
  "items": [
    {
      "item_id": 1,
      "quantity": 2
    }
  ],
  "address_id": 301
}
```

Response:

```json
{
  "order_id": 5001,
  "status": "CREATED",
  "total_price": 240
}
```

---

### Get Order Details

```
GET /api/v1/orders/{order_id}
```

Response:

```json
{
  "order_id": 5001,
  "status": "OUT_FOR_DELIVERY",
  "total_price": 240
}
```

---

### Cancel Order

```
POST /api/v1/orders/{order_id}/cancel
```

Response:

```json
{
  "message": "Order cancelled"
}
```

---

## 6. Payment APIs

### Initiate Payment

```
POST /api/v1/payments
```

Request:

```json
{
  "order_id": 5001,
  "payment_method": "UPI"
}
```

Response:

```json
{
  "payment_id": 9001,
  "status": "PENDING"
}
```

---

### Payment Webhook

```
POST /api/v1/payments/webhook
```

Purpose:

* Called by payment gateway
* Updates payment status

---

## 7. Delivery APIs

### Assign Delivery

```
POST /api/v1/delivery/assign
```

Response:

```json
{
  "delivery_id": 7001,
  "status": "ASSIGNED"
}
```

---

### Update Location

```
POST /api/v1/delivery/location
```

Request:

```json
{
  "delivery_id": 7001,
  "latitude": 28.61,
  "longitude": 77.20
}
```

---

## 8. Real-Time Tracking

* Protocol: WebSocket

Endpoint:

```
/ws/orders/{order_id}
```

Events:

* location_update
* status_update

---

## 9. Status Codes

* 200 OK
* 201 Created
* 400 Bad Request
* 401 Unauthorized
* 404 Not Found
* 409 Conflict
* 500 Internal Server Error

---

## 10. Error Response Format

```json
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Invalid input data"
  }
}
```

---

## 11. Idempotency

* Required for:

  * Order creation
  * Payments
* Achieved using:

  * Idempotency-Key header

---

## 12. Rate Limiting

* Applied at API Gateway
* Example:

  * 100 requests per minute per user

---

## 13. Security

* JWT-based authentication
* HTTPS enforced
* Input validation
* Protection against replay attacks

---
