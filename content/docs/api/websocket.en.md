---
title: "WebSocket API"
linkTitle: "WebSocket"
weight: 30
description: "WebSocket API for real-time deal chat."
categories: ["API"]
tags: ["WebSocket", "chat", "Django Channels"]
---

The platform chat runs on **Django Channels (ASGI)** via WebSockets and Redis Channel Layer. Each dialog is strictly bound to a specific deal (`deal_id`).

---

## Connection

```
ws://host/ws/chat/{deal_id}/
```

### Authentication

The JWT token is transmitted via the **first message** after connection establishment (not via query string — this prevents token leakage through logs).

Connection and authentication examples:

{{< tabs >}}
  {{< tab name="JavaScript" >}}
```javascript
const ws = new WebSocket(`wss://api.railtech.uz/ws/chat/${dealId}/`);

ws.onopen = () => {
  // First message — authentication
  ws.send(JSON.stringify({
    type: "authenticate",
    token: accessToken
  }));
};

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  if (data.type === "auth_success") {
    console.log("Authenticated as", data.username);
  } else if (data.type === "chat_message") {
    console.log(`${data.sender_name}: ${data.message}`);
  }
};
```
  {{< /tab >}}
  {{< tab name="Python" >}}
```python
import asyncio
import websockets
import json

async def chat(deal_id, token):
    uri = f"wss://api.railtech.uz/ws/chat/{deal_id}/"
    async with websockets.connect(uri) as ws:
        # First message — authentication
        await ws.send(json.dumps({
            "type": "authenticate",
            "token": token
        }))
        auth = json.loads(await ws.recv())
        print(f"Auth: {auth['type']}")

        # Sending a message
        await ws.send(json.dumps({
            "type": "chat_message",
            "message": "Hello!"
        }))

asyncio.run(chat(42, "your_jwt_token"))
```
  {{< /tab >}}
{{< /tabs >}}

**Successful authentication response:**
```json
{
  "type": "auth_success",
  "user_id": 1,
  "username": "user@company.uz"
}
```

**Error response:**
```json
{
  "type": "auth_error",
  "message": "Invalid token"
}
```

---

## Sending a Message

```json
{
  "type": "chat_message",
  "message": "Hello, could you clarify availability for item 5?"
}
```

---

## Receiving a Message

```json
{
  "type": "chat_message",
  "message": "Hello, could you clarify availability for item 5?",
  "sender_id": 1,
  "sender_name": "John Smith",
  "timestamp": "2026-03-20T10:30:00Z"
}
```

---

## Access Isolation

The server-side Consumer (`ChatConsumer`) verifies access level upon connection:

- **CLIENT**: can only connect to chats of their own deals (`Deal.client == user`)
- **SALES_MANAGER / ACCOUNT_MANAGER**: only to chats of assigned deals (`Deal.manager == user`)
- **ADMIN**: access to all chats

An attempt to connect to another user's deal returns an error and terminates the connection.

---

## Room (Channel Group)

Each deal has its own isolated room:

```
chat_{deal_id}
```

Messages are delivered only to participants of a specific deal via Redis Channel Layer.

---

## Data Model

All messages are saved in the `Message` model:

| Field | Type | Description |
|-------|------|-------------|
| `deal` | FK(Deal) | Binding to the deal |
| `sender` | FK(User) | Message author |
| `text` | TextField | Message text |
| `timestamp` | DateTimeField | Send time (auto_now_add) |

---

## Nginx Configuration

WebSocket connections are proxied through nginx:

- Proxy pass: `http://backend/ws/`
- `proxy_http_version 1.1`
- `Upgrade` and `Connection` headers
- `proxy_buffer_size 8k` — buffer limit for abuse protection
- Rate limiting: `ws_connect` zone for connection frequency limiting
