---
title: "API"
linkTitle: "API"
weight: 40
description: "REST API and WebSocket API documentation for RailTech B2B platform."
categories: ["API"]
tags: ["REST API", "JWT", "WebSocket", "authentication"]
---

This section describes the API for the RailTech B2B Ecosystem platform:

- **[Authentication](authentication/)** -- JWT authorization and token management
- **[Endpoints](endpoints/)** -- overview of REST API endpoints
- **[WebSocket](websocket/)** -- WebSocket API for real-time chat

## Quick Start

Example of authorization and obtaining a JWT token in different languages:

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
