---
title: "WebSocket API"
linkTitle: "WebSocket"
weight: 30
description: "WebSocket API для real-time чата по сделкам."
categories: ["API"]
tags: ["WebSocket", "чат", "Django Channels"]
---

Чат платформы работает на **Django Channels (ASGI)** через WebSockets и Redis Channel Layer. Каждый диалог жестко привязан к конкретной сделке (`deal_id`).

---

## Подключение

```
ws://host/ws/chat/{deal_id}/
```

### Аутентификация

JWT-токен передается через **первое сообщение** после установления соединения (не через query string -- это предотвращает утечку токена через логи).

Примеры подключения и аутентификации:

{{< tabs items="JavaScript,Python" >}}
  {{< tab >}}
```javascript
const ws = new WebSocket(`wss://api.railtech.uz/ws/chat/${dealId}/`);

ws.onopen = () => {
  // Первое сообщение -- аутентификация
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
  {{< tab >}}
```python
import asyncio
import websockets
import json

async def chat(deal_id, token):
    uri = f"wss://api.railtech.uz/ws/chat/{deal_id}/"
    async with websockets.connect(uri) as ws:
        # Первое сообщение -- аутентификация
        await ws.send(json.dumps({
            "type": "authenticate",
            "token": token
        }))
        auth = json.loads(await ws.recv())
        print(f"Auth: {auth['type']}")

        # Отправка сообщения
        await ws.send(json.dumps({
            "type": "chat_message",
            "message": "Добрый день!"
        }))

asyncio.run(chat(42, "your_jwt_token"))
```
  {{< /tab >}}
{{< /tabs >}}

**Ответ при успешной аутентификации:**
```json
{
  "type": "auth_success",
  "user_id": 1,
  "username": "user@company.uz"
}
```

**Ответ при ошибке:**
```json
{
  "type": "auth_error",
  "message": "Invalid token"
}
```

---

## Отправка сообщения

```json
{
  "type": "chat_message",
  "message": "Добрый день, прошу уточнить наличие по позиции 5"
}
```

---

## Получение сообщения

```json
{
  "type": "chat_message",
  "message": "Добрый день, прошу уточнить наличие по позиции 5",
  "sender_id": 1,
  "sender_name": "Иванов И.И.",
  "timestamp": "2026-03-20T10:30:00Z"
}
```

---

## Изоляция доступа

Серверный Consumer (`ChatConsumer`) проверяет уровень доступа при подключении:

- **CLIENT**: может подключиться только к чатам своих сделок (`Deal.client == user`)
- **SALES_MANAGER / ACCOUNT_MANAGER**: только к чатам назначенных сделок (`Deal.manager == user`)
- **ADMIN**: доступ ко всем чатам

Попытка подключения к чужой сделке возвращает ошибку и разрывает соединение.

---

## Комната (Channel Group)

Каждая сделка имеет свою изолированную комнату:

```
chat_{deal_id}
```

Сообщения доставляются только участникам конкретной сделки через Redis Channel Layer.

---

## Модель данных

Все сообщения сохраняются в модели `Message`:

| Поле | Тип | Описание |
|------|-----|----------|
| `deal` | FK(Deal) | Привязка к сделке |
| `sender` | FK(User) | Автор сообщения |
| `text` | TextField | Текст сообщения |
| `timestamp` | DateTimeField | Время отправки (auto_now_add) |

---

## Nginx конфигурация

WebSocket-соединения проксируются через nginx:

- Proxy pass: `http://backend/ws/`
- `proxy_http_version 1.1`
- `Upgrade` и `Connection` headers
- `proxy_buffer_size 8k` -- ограничение буфера для защиты от злоупотреблений
- Rate limiting: зона `ws_connect` для ограничения частоты подключений
