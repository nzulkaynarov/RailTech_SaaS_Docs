---
title: "Authentication"
linkTitle: "Authentication"
weight: 10
description: "JWT authorization, token management and security policies."
categories: ["API"]
tags: ["JWT", "authentication", "REST API", "security"]
---

The platform uses JWT (JSON Web Tokens) for authorization of all API requests. Global policy: `IsAuthenticated` — all endpoints except the public catalog require a valid token.

---

## Obtaining a Token

### Login

Authentication examples in different languages:

{{< tabs >}}
  {{< tab name="cURL" >}}
```bash
curl -X POST https://api.railtech.uz/api/v1/accounts/login/ \
  -H "Content-Type: application/json" \
  -d '{"email": "user@company.uz", "password": "secure_password"}'
```
  {{< /tab >}}
  {{< tab name="Python" >}}
```python
import requests

resp = requests.post("https://api.railtech.uz/api/v1/accounts/login/", json={
    "email": "user@company.uz",
    "password": "secure_password"
})
data = resp.json()
access_token = data["access"]
refresh_token = data["refresh"]
```
  {{< /tab >}}
  {{< tab name="JavaScript" >}}
```javascript
const resp = await fetch("https://api.railtech.uz/api/v1/accounts/login/", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    email: "user@company.uz",
    password: "secure_password"
  })
});
const { access, refresh } = await resp.json();
```
  {{< /tab >}}
{{< /tabs >}}

**Response (200 OK):**
```json
{
  "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "user": {
    "id": 1,
    "email": "user@company.uz",
    "role": "CLIENT",
    "workspace_role": "OWNER",
    "company": {
      "id": 1,
      "name": "Company Name",
      "verification_status": "L2_VERIFIED"
    }
  }
}
```

### Token Refresh

```
POST /api/v1/accounts/token/refresh/
Content-Type: application/json

{
  "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

**Response (200 OK):**
```json
{
  "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

---

## Using the Token

All requests to protected endpoints must include the header:

```
Authorization: Bearer <access_token>
```

---

## Registration

```
POST /api/v1/accounts/register/
Content-Type: application/json

{
  "email": "owner@newcompany.uz",
  "password": "secure_password",
  "company_name": "New Company LLC",
  "inn": "123456789"
}
```

Upon registration:
1. A `User` object is created with the `GUEST` role
2. A `Company` object is created with `PROFILE_SUBMITTED` status
3. The user receives `workspace_role: OWNER`
4. After KYC verification by a manager, the role is upgraded to `CLIENT`

---

## Security

{{< callout type="warning" >}}
**Token Expiration:** Access token expires after **5 minutes**, Refresh token after **1 day**. Always implement automatic access token refresh via the refresh endpoint before expiration.
{{< /callout >}}

### Current State
- JWT tokens are stored in `localStorage` on the frontend
- Access token: short TTL (default 5 minutes)
- Refresh token: long TTL (default 1 day)

### Planned Improvements
- Migration of refresh token to HttpOnly cookies (Sprint 1)
- Addition of CSRF protection for cookie-based auth

### WebSocket Authorization
- JWT token is transmitted via the first WebSocket message (not via query string)
- This prevents token leakage through server logs and referrer headers

### Rate Limiting (nginx)
- `api_general`: general API request limit
- `api_auth`: enhanced limit on authentication endpoints
- `ws_connect`: limit on WebSocket connection establishment
