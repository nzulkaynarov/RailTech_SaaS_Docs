---
title: "Security Audit"
linkTitle: "Security Audit"
weight: 60
description: "Platform security audit results and implemented protective measures."
categories: ["Architecture"]
tags: ["security", "audit"]
---

This document describes the results of the RailTech B2B Ecosystem platform security audit and the measures taken to protect user data and business processes.

---

## 1. Authentication and Authorization

### Implemented Measures
- **JWT with short TTL:** Access token — 5 minutes, Refresh token — 1 day
- **WebSocket Authentication:** JWT token is transmitted via the first message after connection establishment (not through query string, preventing token leakage through server logs)
- **RBAC with database-level isolation:** All ViewSets use `get_queryset` override — users can never access other users' data even with direct ID queries
- **Standardized permissions:** Unified permission classes (`IsOwnerOrManager`) across all endpoints

### Planned Improvements
- Migration of Refresh token to HttpOnly cookies
- Addition of CSRF protection for cookie-based authentication

---

## 2. Data Protection

### Implemented Measures
- **File format validation:** Strict checking of uploaded file types and sizes
- **Deal access isolation:** Clients see only their own deals, managers — only assigned ones
- **Immutable financial audit log:** All price and margin changes are recorded in `QuoteItemHistory`
- **FSM deal stage transitions:** State machine validates stage transition sequences, preventing status manipulation
- **Activity log filtering:** System logs do not expose sensitive data of other users

### Architecture Decisions
- All financial calculations are performed exclusively on the server (Django backend)
- Frontend never calculates prices, VAT, margin or Landed Cost
- Monetary fields use `DecimalField` (not `FloatField`) for precision

---

## 3. Infrastructure Security

### Implemented Measures
- **Rate Limiting (nginx):** Separate zones for general API, authentication endpoints and WebSocket connections
- **Security Headers:** Content Security Policy (CSP), X-Frame-Options, X-Content-Type-Options, HSTS preparation
- **WebSocket buffer limits:** `proxy_buffer_size 8k` to protect against abuse
- **Service isolation:** Separation of cache and task queue services
- **Environment secrets:** All sensitive data stored in CI/CD secrets and injected as environment variables during deployment
- **Database access:** Restricted by network rules

### Planned Improvements
- SSL/HTTPS with automatic certificate management
- Performance monitoring integration
- Error monitoring (Sentry or equivalent)

---

## 4. IDOR Protection

All API endpoints verify resource ownership before granting access. The platform uses database-level filtering (`get_queryset`) to ensure:

- **Clients** can only access their own deals, messages, and documents
- **Sales Managers** can only access deals assigned to them or unassigned deals
- **Account Managers** have access similar to Sales Managers with additional contract management
- **Admins** have full access with no queryset filtering

---

## 5. Current Status

- All critical vulnerabilities identified during the audit have been fixed
- Code review process is implemented for security-sensitive changes
- Security-first approach is applied to all new development

---

## 6. Recommendations for Integrators

If you are integrating with the RailTech B2B API, follow these security best practices:

1. **Token Storage:** Store JWT tokens securely. Avoid storing tokens in `localStorage` in production — prefer `sessionStorage` or secure cookie-based approaches
2. **Token Refresh:** Implement automatic access token refresh before expiration using the refresh endpoint
3. **Response Validation:** Always validate API responses and handle error codes properly
4. **HTTPS:** Use HTTPS for all API communications
5. **Minimal Access:** Request only the minimum necessary permissions for your integration
6. **Rate Limits:** Respect API rate limits and implement exponential backoff for retries
