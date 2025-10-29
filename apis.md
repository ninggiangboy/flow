## **POST** `/api/auth/sessions`

### **Description**

Authenticate a user using their credentials and issue a new **refresh token**.

### Authentication

Not requires authenticated

### **Request Body**

| Field      | Type   | Required | Description     |
| ---------- | ------ | -------- | --------------- |
| `username` | string | ✅        | User’s username |
| `password` | string | ✅        | User’s password |

```json
{
  "username": "user@example.com",
  "password": "P@ssw0rd!"
}
```

### **Responses**

#### ✅ **201 Created**

Issued a new refresh token.

```json
{
  "refresh_token": "def50200...",
  "expires_in": 86400
}
```

#### ❌ **401 Unauthorized**

Invalid credentials.

```json
{
  "error": "Invalid username or password"
}
```

---

## **GET** `/api/auth/sessions`

### **Description**

Retrieve a list of all active sessions (devices) for the authenticated user.

### Authentication

Required (user must be logged in)

### **Responses**

#### ✅ **200 OK**

```json
[
  {
    "id": "session_123",
    "device": "Chrome on Windows",
    "ip": "192.168.1.10",
    "created_at": "2025-10-29T10:00:00Z",
    "last_active": "2025-10-29T16:00:00Z"
  }
]
```

#### ❌ **401 Unauthorized**

```json
{
  "error": "Authentication required"
}
```

---

## **POST** `/api/auth/sessions/current/access-tokens`

### **Description**

Generate a new **access token** using a valid refresh token.

### Authentication

Not required (refresh token provided in body)

### **Request Body**

| Field           | Type   | Required | Description                |
| --------------- | ------ | -------- | -------------------------- |
| `refresh_token` | string | ✅        | User's valid refresh token |

```json
{
  "refresh_token": "def50200..."
}
```

### **Responses**

#### ✅ **201 Created**

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 3600
}
```

#### ❌ **401 Unauthorized**

```json
{
  "error": "Invalid or expired refresh token"
}
```

---

## **DELETE** `/api/auth/sessions/current`

### **Description**

Logout the current session and revoke its refresh token.

### Authentication

Required

### **Responses**

#### ✅ **204 No Content**

Successful logout.

#### ❌ **401 Unauthorized**

```json
{
  "error": "Authentication required"
}
```

---

## **DELETE** `/api/auth/sessions/:id`

### **Description**

Logout from a specific device/session identified by `:id`.

### Authentication

Required

### **Responses**

#### ✅ **204 No Content**

Session revoked successfully.

#### ❌ **401 Unauthorized**

```json
{
  "error": "Authentication required"
}
```

#### ❌ **404 Not Found**

```json
{
  "error": "Session not found"
}
```

---

## **DELETE** `/api/auth/sessions/others`

### **Description**

Logout from all other sessions except the current one.

### Authentication

Required

### **Responses**

#### ✅ **204 No Content**

All other sessions revoked.

#### ❌ **401 Unauthorized**

```json
{
  "error": "Authentication required"
}
```

---

## **POST** `/api/auth/otps`

### **Description**

Generate and send an OTP to the user (email/phone).

### Authentication

Not required

### **Request Body**

| Field         | Type   | Required | Description           |
| ------------- | ------ | -------- | --------------------- |
| `type`        | string | ✅        | "email" or "phone"    |
| `destination` | string | ✅        | Email or phone number |

```json
{
  "type": "email",
  "destination": "user@example.com"
}
```

### **Responses**

#### ✅ **201 Created**

```json
{
  "otp_id": "otp_123",
  "expires_in": 300
}
```

#### ❌ **400 Bad Request**

```json
{
  "error": "Invalid destination"
}
```

---

## **PUT** `/api/auth/otps/:id`

### **Description**

Verify OTP and generate a temporary token for further actions (e.g., registration, password reset).

### Authentication

Not required

### **Request Body**

| Field  | Type   | Required | Description               |
| ------ | ------ | -------- | ------------------------- |
| `code` | string | ✅        | OTP code sent to the user |

```json
{
  "code": "123456"
}
```

### **Responses**

#### ✅ **200 OK**

```json
{
  "otp_token": "tmp_token_abc",
  "expires_in": 600
}
```

#### ❌ **400 Bad Request**

```json
{
  "error": "Invalid or expired OTP"
}
```

---

## **POST** `/api/auth/users`

### **Description**

Register a new user using a valid OTP token.

### Authentication

Not required

### **Request Body**

| Field       | Type   | Required | Description            |
| ----------- | ------ | -------- | ---------------------- |
| `otp_token` | string | ✅        | OTP verification token |
| `username`  | string | ✅        | Desired username/email |
| `password`  | string | ✅        | Password               |

```json
{
  "otp_token": "tmp_token_abc",
  "username": "user@example.com",
  "password": "P@ssw0rd!"
}
```

### **Responses**

#### ✅ **201 Created**

User registered successfully.

```json
{
  "id": "user_123",
  "username": "user@example.com"
}
```

#### ❌ **400 Bad Request**

```json
{
  "error": "OTP token invalid or expired"
}
```

---

## **PUT** `/api/auth/users/password`

### **Description**

Change password using a valid OTP token (e.g., for password reset).

### Authentication

Not required

### **Request Body**

| Field       | Type   | Required | Description            |
| ----------- | ------ | -------- | ---------------------- |
| `otp_token` | string | ✅        | OTP verification token |
| `password`  | string | ✅        | New password           |

```json
{
  "otp_token": "tmp_token_abc",
  "password": "NewP@ssw0rd!"
}
```

### **Responses**

#### ✅ **200 OK**

Password updated successfully.

#### ❌ **400 Bad Request**

```json
{
  "error": "OTP token invalid or expired"
}
```

---

## **GET** `/api/auth/me/profile`

### **Description**

Retrieve the current user's profile information.

### Authentication

Required

### **Responses**

#### ✅ **200 OK**

```json
{
  "id": "user_123",
  "username": "user@example.com",
  "email": "user@example.com",
  "created_at": "2025-10-01T10:00:00Z"
}
```

#### ❌ **401 Unauthorized**

```json
{
  "error": "Authentication required"
}
```

---

## **PUT** `/api/auth/users/me/password`

### **Description**

Change password using the current (old) password.

### Authentication

Required

### **Request Body**

| Field          | Type   | Required | Description      |
| -------------- | ------ | -------- | ---------------- |
| `old_password` | string | ✅        | Current password |
| `new_password` | string | ✅        | New password     |

```json
{
  "old_password": "OldP@ssw0rd!",
  "new_password": "NewP@ssw0rd!"
}
```

### **Responses**

#### ✅ **200 OK**

Password changed successfully.

#### ❌ **400 Bad Request**

```json
{
  "error": "Old password is incorrect"
}
```

---

## **PUT** `/api/auth/users/email/password`

### **Description**

Change the user's email. Requires a valid OTP token to authorize the change.

### Authentication

Required

### **Request Body**

| Field       | Type   | Required | Description            |
| ----------- | ------ | -------- | ---------------------- |
| `otp_token` | string | ✅        | OTP verification token |
| `new_email` | string | ✅        | New email address      |

```json
{
  "otp_token": "tmp_token_abc",
  "new_email": "newuser@example.com"
}
```

### **Responses**

#### ✅ **200 OK**

Email changed successfully.

```json
{
  "email": "newuser@example.com"
}
```

#### ❌ **400 Bad Request**

```json
{
  "error": "OTP token invalid or expired"
}
```

---

