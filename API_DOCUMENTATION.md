# API Documentation

## Base URL
```
Development: http://localhost:5000/api
Production: https://api.example.com/api
```

## Authentication
All protected endpoints require a JWT token in the Authorization header:
```
Authorization: Bearer <token>
```

---

## Endpoints

### 🔐 Authentication

#### Register User
```http
POST /auth/register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "SecurePass123@"
}
```

**Response (201 Created):**
```json
{
  "message": "User registered successfully"
}
```

**Error (409 Conflict):**
```json
{
  "error": "Email already registered"
}
```

---

#### Login User
```http
POST /auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "SecurePass123@"
}
```

**Response (200 OK):**
```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwi...",
  "user": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com",
    "role": "user"
  }
}
```

**Error (401 Unauthorized):**
```json
{
  "error": "Invalid credentials"
}
```

---

### 👥 Users

#### Get Current User Profile
```http
GET /users/profile
Authorization: Bearer <token>
```

**Response (200 OK):**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user",
  "created_at": "2025-04-03T10:30:00Z"
}
```

**Error (401 Unauthorized):**
```json
{
  "error": "Access token required"
}
```

---

#### Get All Users (Admin Only)
```http
GET /users
Authorization: Bearer <admin_token>
```

**Response (200 OK):**
```json
[
  {
    "id": 1,
    "name": "Admin User",
    "email": "admin@example.com",
    "role": "admin",
    "created_at": "2025-04-03T10:00:00Z"
  },
  {
    "id": 2,
    "name": "Regular User",
    "email": "user@example.com",
    "role": "user",
    "created_at": "2025-04-03T11:00:00Z"
  }
]
```

**Error (403 Forbidden):**
```json
{
  "error": "Insufficient permissions"
}
```

---

#### Get Single User (Admin Only)
```http
GET /users/:id
Authorization: Bearer <admin_token>
```

**Response (200 OK):**
```json
{
  "id": 2,
  "name": "Regular User",
  "email": "user@example.com",
  "role": "user",
  "created_at": "2025-04-03T11:00:00Z"
}
```

**Error (404 Not Found):**
```json
{
  "error": "User not found"
}
```

---

#### Delete User (Admin Only)
```http
DELETE /users/:id
Authorization: Bearer <admin_token>
```

**Response (200 OK):**
```json
{
  "message": "User deleted successfully"
}
```

**Error (400 Bad Request):**
```json
{
  "error": "Cannot delete your own account"
}
```

---

### 📦 Products

#### Get All Products
```http
GET /products?page=1&limit=10
```

**Query Parameters:**
- `page` (optional, default: 1) - Page number
- `limit` (optional, default: 10, max: 50) - Items per page

**Response (200 OK):**
```json
{
  "data": [
    {
      "id": 1,
      "name": "Laptop",
      "description": "High-performance laptop for development",
      "price": 999.99,
      "stock": 10,
      "created_at": "2025-04-03T10:00:00Z",
      "updated_at": "2025-04-03T10:00:00Z"
    },
    {
      "id": 2,
      "name": "Mouse",
      "description": "Wireless mouse with precision tracking",
      "price": 29.99,
      "stock": 50,
      "created_at": "2025-04-03T10:05:00Z",
      "updated_at": "2025-04-03T10:05:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 3,
    "pages": 1
  }
}
```

---

#### Get Single Product
```http
GET /products/:id
```

**Response (200 OK):**
```json
{
  "id": 1,
  "name": "Laptop",
  "description": "High-performance laptop for development",
  "price": 999.99,
  "stock": 10,
  "created_at": "2025-04-03T10:00:00Z",
  "updated_at": "2025-04-03T10:00:00Z"
}
```

**Error (404 Not Found):**
```json
{
  "error": "Product not found"
}
```

---

#### Create Product (Admin Only)
```http
POST /products
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "name": "Keyboard",
  "description": "Mechanical keyboard with RGB lighting",
  "price": 149.99,
  "stock": 25
}
```

**Response (201 Created):**
```json
{
  "message": "Product created successfully",
  "id": 4
}
```

**Error (400 Bad Request):**
```json
{
  "error": "\"price\" must be a positive number"
}
```

---

#### Update Product (Admin Only)
```http
PUT /products/:id
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "name": "Keyboard Pro",
  "description": "Premium mechanical keyboard with RGB lighting",
  "price": 199.99,
  "stock": 15
}
```

**Response (200 OK):**
```json
{
  "message": "Product updated successfully"
}
```

**Error (404 Not Found):**
```json
{
  "error": "Product not found"
}
```

---

#### Delete Product (Admin Only)
```http
DELETE /products/:id
Authorization: Bearer <admin_token>
```

**Response (200 OK):**
```json
{
  "message": "Product deleted successfully"
}
```

**Error (404 Not Found):**
```json
{
  "error": "Product not found"
}
```

---

### ✅ Health Check

#### Server Health
```http
GET /health
```

**Response (200 OK):**
```json
{
  "status": "Server is running",
  "timestamp": "2025-04-03T10:30:00.123Z"
}
```

---

## Error Handling

### Error Response Format
```json
{
  "error": {
    "status": 400,
    "message": "Error description",
    "stack": "Error stack trace (development only)"
  }
}
```

### HTTP Status Codes
| Status | Meaning |
|--------|---------|
| 200 | OK - Request successful |
| 201 | Created - Resource created |
| 400 | Bad Request - Invalid input |
| 401 | Unauthorized - Missing or invalid token |
| 403 | Forbidden - Insufficient permissions |
| 404 | Not Found - Resource not found |
| 409 | Conflict - Resource already exists |
| 429 | Too Many Requests - Rate limit exceeded |
| 500 | Internal Server Error - Server error |

---

## Rate Limiting

API endpoints are rate limited:
- **General**: 30 requests per 15 minutes per IP
- **API**: 100 requests per 15 minutes per IP

When limit exceeded:
```
HTTP/1.1 429 Too Many Requests

{
  "error": "Too many requests from this IP, please try again later."
}
```

---

## Pagination

Endpoints that return lists support pagination:

```http
GET /products?page=2&limit=20
```

Response includes pagination metadata:
```json
{
  "data": [...],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 100,
    "pages": 5
  }
}
```

---

## Validation Rules

### User Registration
- **name**: String, 2-50 characters
- **email**: Valid email format
- **password**: 8+ characters, alphanumeric + special chars

### Product Creation/Update
- **name**: String, 2-100 characters (required)
- **description**: String, max 500 characters
- **price**: Positive number (required)
- **stock**: Non-negative integer (required)

---

## CORS

Configured for development. Update `FRONTEND_URL` in `.env` for production:

```env
FRONTEND_URL=https://yourdomain.com
```

---

## Testing with cURL

### Login
```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@example.com",
    "password": "password"
  }'
```

### Create Product (as Admin)
```bash
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "name": "New Product",
    "price": 99.99,
    "stock": 10
  }'
```

### Get Products
```bash
curl http://localhost:5000/api/products?page=1&limit=10
```

---

## Testing with Postman

1. Import the collection: (Collection JSON available in repository)
2. Set `{{base_url}}` variable to `http://localhost:5000/api`
3. Set `{{token}}` variable after login
4. Run requests

---

## Changelog

### v1.0.0 (2025-04-03)
- Initial API release
- User authentication with JWT
- CRUD operations for products
- Role-based access control
- Rate limiting and security headers
