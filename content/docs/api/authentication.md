---
title: "Аутентификация"
linkTitle: "Аутентификация"
weight: 10
description: "JWT-авторизация, управление токенами и политики безопасности."
categories: ["API"]
tags: ["JWT", "аутентификация", "REST API", "безопасность"]
---

Платформа использует JWT (JSON Web Tokens) для авторизации всех API-запросов. Глобальная политика: `IsAuthenticated` -- все endpoints кроме публичного каталога требуют валидный токен.

---

## Получение токена

### Вход в систему

Примеры авторизации на разных языках:

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

**Ответ (200 OK):**
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

### Обновление токена

```
POST /api/v1/accounts/token/refresh/
Content-Type: application/json

{
  "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

**Ответ (200 OK):**
```json
{
  "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

---

## Использование токена

Все запросы к защищенным endpoints должны содержать заголовок:

```
Authorization: Bearer <access_token>
```

---

## Регистрация

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

При регистрации:
1. Создается объект `User` с ролью `GUEST`
2. Создается объект `Company` со статусом `PROFILE_SUBMITTED`
3. Пользователь получает `workspace_role: OWNER`
4. После KYC-верификации менеджером роль повышается до `CLIENT`

---

## Безопасность

{{< callout type="warning" >}}
**Срок действия токенов:** Access token истекает через **5 минут**, Refresh token -- через **1 день**. Всегда реализуйте автоматическое обновление access token через refresh endpoint до истечения срока.
{{< /callout >}}

### Текущее состояние
- JWT-токены хранятся в `localStorage` на frontend
- Access token: короткий TTL (по умолчанию 5 минут)
- Refresh token: длинный TTL (по умолчанию 1 день)

### Планируемые улучшения
- Миграция refresh token в HttpOnly cookies (Sprint 1)
- Добавление CSRF-защиты для cookie-based auth

### WebSocket авторизация
- JWT-токен передается через первое сообщение WebSocket-соединения (не через query string)
- Это предотвращает утечку токена через серверные логи и referrer headers

### Rate Limiting (nginx)
- `api_general`: общий лимит запросов к API
- `api_auth`: усиленный лимит на endpoints аутентификации
- `ws_connect`: лимит на установление WebSocket-соединений
