# API Summary

## Overview
RESTful API endpoints quick reference. For complete specifications, see [API_DOCS.md](../detailed/API_DOCS.md).

## Base URL
- Development: `http://localhost:3000/api/v1`
- Production: `https://api.example.com/v1`

## Authentication
All endpoints except `/auth/*` require authentication.

**Header**: `Authorization: Bearer <jwt_token>`

## Core Endpoints

### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/auth/login` | User login |
| POST | `/auth/register` | New user registration |
| POST | `/auth/refresh` | Refresh access token |
| POST | `/auth/logout` | Logout user |

### Users
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/users/me` | Get current user | ✓ |
| PUT | `/users/me` | Update current user | ✓ |
| GET | `/users` | List all users | Admin |
| GET | `/users/:id` | Get specific user | Admin |

### Products
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/products` | List products | - |
| GET | `/products/:id` | Get product details | - |
| POST | `/products` | Create product | Admin |
| PUT | `/products/:id` | Update product | Admin |
| DELETE | `/products/:id` | Delete product | Admin |

### Orders
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/orders` | List user's orders | ✓ |
| GET | `/orders/:id` | Get order details | ✓ |
| POST | `/orders` | Create new order | ✓ |
| PUT | `/orders/:id/status` | Update order status | Admin |
| POST | `/orders/:id/cancel` | Cancel order | ✓ |

## Request/Response Format

### Standard Response
```json
{
  "success": true,
  "data": {
    // Response data
  },
  "meta": {
    "timestamp": "2024-01-15T10:30:00Z",
    "version": "1.0.0",
    "requestId": "uuid"
  }
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  },
  "meta": {
    "timestamp": "2024-01-15T10:30:00Z",
    "version": "1.0.0",
    "requestId": "uuid"
  }
}
```

### Pagination
```json
{
  "success": true,
  "data": {
    "items": [...],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "totalPages": 5,
      "hasNext": true,
      "hasPrev": false
    }
  }
}
```

## Common Query Parameters

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| page | number | Page number (1-based) | `?page=2` |
| limit | number | Items per page (max: 100) | `?limit=50` |
| sort | string | Sort field | `?sort=created_at` |
| order | string | Sort order (asc/desc) | `?order=desc` |
| search | string | Search term | `?search=widget` |
| filter | object | Field filters | `?filter[status]=active` |

## Status Codes

| Code | Meaning | Usage |
|------|---------|-------|
| 200 | OK | Successful GET, PUT |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Missing/invalid auth |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Resource conflict |
| 422 | Unprocessable | Validation errors |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Server Error | Internal error |

## Rate Limiting

- **Authenticated**: 1000 requests per hour
- **Unauthenticated**: 100 requests per hour
- **Headers**:
  - `X-RateLimit-Limit`: Total allowed
  - `X-RateLimit-Remaining`: Requests left
  - `X-RateLimit-Reset`: Reset timestamp

## Common Error Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `AUTH_REQUIRED` | Authentication required | Include auth token |
| `INVALID_TOKEN` | Token invalid or expired | Refresh token |
| `PERMISSION_DENIED` | Insufficient permissions | Check user role |
| `VALIDATION_ERROR` | Input validation failed | Check error details |
| `RESOURCE_NOT_FOUND` | Resource doesn't exist | Verify ID |
| `DUPLICATE_RESOURCE` | Resource already exists | Use different identifier |
| `RATE_LIMIT_EXCEEDED` | Too many requests | Wait and retry |

## Webhook Events

| Event | Description | Payload |
|-------|-------------|---------|
| `order.created` | New order placed | Order object |
| `order.completed` | Order fulfilled | Order object |
| `user.registered` | New user signup | User object |
| `product.low_stock` | Inventory alert | Product object |

---

*For complete API documentation with request/response examples, see [API_DOCS.md](../detailed/API_DOCS.md)*