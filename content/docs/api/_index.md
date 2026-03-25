---
title: "API"
linkTitle: "API"
weight: 40
description: "Документация REST API и WebSocket API платформы RailTech B2B."
categories: ["API"]
tags: ["REST API", "JWT", "WebSocket", "аутентификация"]
---

Раздел описывает API платформы RailTech B2B Ecosystem:

- **[Аутентификация](authentication/)** -- JWT-авторизация и управление токенами
- **[Endpoints](endpoints/)** -- обзор REST API endpoints
- **[WebSocket](websocket/)** -- WebSocket API для real-time чата

## Быстрый старт

Пример авторизации и получения JWT-токена на разных языках:

{{< tabs >}}
  {{< tab name="cURL" >}}
```bash
curl -X POST https://api.railtech.uz/api/v1/accounts/login/ \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "secret"}'
```
  {{< /tab >}}
  {{< tab name="Python" >}}
```python
import requests
resp = requests.post("https://api.railtech.uz/api/v1/accounts/login/", json={
    "email": "user@example.com",
    "password": "secret"
})
token = resp.json()["access"]
```
  {{< /tab >}}
  {{< tab name="JavaScript" >}}
```javascript
const resp = await fetch("https://api.railtech.uz/api/v1/accounts/login/", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ email: "user@example.com", password: "secret" })
});
const { access } = await resp.json();
```
  {{< /tab >}}
{{< /tabs >}}
