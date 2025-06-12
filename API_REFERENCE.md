# E-Banking Backend API Reference

## Base URL
```
http://localhost:8085
```

## Authentication
All endpoints (except `/auth/login`) require JWT Bearer token authentication.

### Users
- **admin**: username=`admin`, password=`12345` (has USER + ADMIN authorities)
- **user1**: username=`user1`, password=`12345` (has USER authority only)

## Endpoints Overview

### 🔐 Authentication
| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/auth/login` | Login and get JWT token | No |
| GET | `/auth/profile` | Get current user profile | Yes |

### 👥 Customers (CustomerRestController)
| Method | Endpoint | Description | Required Authority |
|--------|----------|-------------|-------------------|
| GET | `/customers` | Get all customers | USER |
| GET | `/customers/search?keyword={keyword}` | Search customers | USER |
| GET | `/customers/{id}` | Get customer by ID | USER |
| POST | `/customers` | Create new customer | ADMIN |
| PUT | `/customers/{id}` | Update customer | ADMIN |
| DELETE | `/customers/{id}` | Delete customer | ADMIN |

### 🏦 Bank Accounts (BankAccountRestAPI)
| Method | Endpoint | Description | Required Authority |
|--------|----------|-------------|-------------------|
| GET | `/accounts` | Get all bank accounts | USER |
| GET | `/accounts/{accountId}` | Get specific account | USER |
| GET | `/accounts/{accountId}/operations` | Get account operations | USER |
| GET | `/accounts/{accountId}/pageOperations?page={page}&size={size}` | Get paginated operations | USER |

### 💰 Account Operations
| Method | Endpoint | Description | Required Authority |
|--------|----------|-------------|-------------------|
| POST | `/accounts/debit` | Debit account | USER |
| POST | `/accounts/credit` | Credit account | USER |
| POST | `/accounts/transfer` | Transfer between accounts | USER |

## Request/Response Examples

### Login Request
```http
POST /auth/login
Content-Type: application/x-www-form-urlencoded

username=admin&password=12345
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJIUzUxMiJ9..."
}
```

### Create Customer Request
```http
POST /customers
Content-Type: application/json
Authorization: Bearer {token}

{
  "name": "John Doe",
  "email": "john.doe@example.com"
}
```

### Debit Account Request
```http
POST /accounts/debit
Content-Type: application/json
Authorization: Bearer {token}

{
  "accountId": "account123",
  "amount": 100.0,
  "description": "Test debit"
}
```

### Transfer Request
```http
POST /accounts/transfer
Content-Type: application/json
Authorization: Bearer {token}

{
  "accountSource": "account123",
  "accountDestination": "account456",
  "amount": 50.0
}
```

## Error Responses

### 401 Unauthorized
```json
{
  "error": "invalid_token",
  "error_description": "Bearer token is malformed"
}
```

### 403 Forbidden
```json
{
  "error": "access_denied",
  "error_description": "Access is denied"
}
```

## Testing Workflow

1. **Login first** to get a token
2. **Use the token** in subsequent requests
3. **Use admin token** for ADMIN-only endpoints
4. **Use user token** for USER endpoints

## Authority Mapping
- JWT scope `"USER"` → Spring Security authority `"SCOPE_USER"`
- JWT scope `"ADMIN"` → Spring Security authority `"SCOPE_ADMIN"`

## Notes
- JWT tokens expire after 10 minutes
- All endpoints support CORS with `@CrossOrigin("*")`
- Account operations may throw `BankAccountNotFoundException` or `BalanceNotSufficientException`