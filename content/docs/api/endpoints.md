---
title: "API Endpoints"
linkTitle: "Endpoints"
weight: 20
description: "Обзор REST API endpoints платформы RailTech B2B."
categories: ["API"]
tags: ["REST API", "endpoints", "RBAC"]
---

Все endpoints доступны по базовому URL `/api/v1/`. Авторизация: JWT Bearer token (если не указано иное).

---

## Accounts (`/api/v1/accounts/`)

| Метод | Endpoint | Описание | Доступ |
|-------|----------|----------|--------|
| POST | `/register/` | Регистрация компании по ИНН | Public |
| POST | `/login/` | Получение JWT-токенов | Public |
| POST | `/token/refresh/` | Обновление access token | Public |
| GET | `/me/` | Профиль текущего пользователя | Authenticated |
| GET/PUT | `/users/` | Управление пользователями Workspace | Owner/Admin |

---

## Catalog (`/api/v1/catalog/`)

| Метод | Endpoint | Описание | Доступ |
|-------|----------|----------|--------|
| GET | `/products/` | Список товаров (фасетный поиск) | Public (цены скрыты для Guest) |
| GET | `/products/{id}/` | Детали товара | Public |
| GET | `/categories/` | Дерево категорий | Public |
| GET | `/equipment-groups/` | Иерархия техники (ЖД-домен) | Public |
| GET | `/products/count/` | Количество товаров по доменам | Public |
| POST | `/products/import/` | Массовый импорт каталога (Excel) | Admin |

---

## CRM (`/api/v1/crm/`)

### Сделки

| Метод | Endpoint | Описание | Доступ |
|-------|----------|----------|--------|
| GET | `/deals/` | Список сделок (RBAC-фильтрация) | Authenticated |
| POST | `/deals/` | Создание сделки | Client/Manager |
| GET | `/deals/{id}/` | Детали сделки | Owner/Manager |
| POST | `/deals/{id}/import_excel/` | Импорт Excel-спецификации (Celery) | Client/Manager |
| GET | `/deals/{id}/export_pdf/` | Экспорт КП в PDF (WeasyPrint) | Manager/Admin |
| POST | `/deals/{id}/send-quote/` | Отправка КП клиенту | Manager/Admin |
| POST | `/deals/{id}/approve-quote/` | Подтверждение КП клиентом | Client |
| POST | `/deals/{id}/reject-quote/` | Отклонение КП | Client |

### Позиции сделки

| Метод | Endpoint | Описание | Доступ |
|-------|----------|----------|--------|
| GET | `/deal-items/` | Список позиций сделки | Owner/Manager |
| PUT | `/deal-items/{id}/` | Обновление позиции (маппинг в Sourcing) | Manager/Admin |
| PATCH | `/deal-items/{id}/` | Частичное обновление (цены, маржа) | Manager/Admin |

### Аналитика

| Метод | Endpoint | Описание | Доступ |
|-------|----------|----------|--------|
| GET | `/analytics/` | Агрегированные метрики | Manager/Admin |

Возвращаемые метрики:
- `pipeline_by_status` -- распределение сделок по воронке
- `sourcing_backlog` -- количество позиций без привязки к товару
- `conversion_rate` -- процент успешно закрытых сделок
- `total_pipeline_value` -- суммарная стоимость пайплайна

---

## Notifications (`/api/v1/notifications/`)

| Метод | Endpoint | Описание | Доступ |
|-------|----------|----------|--------|
| GET | `/` | Список уведомлений пользователя | Authenticated |
| PATCH | `/{id}/` | Пометить как прочитанное | Authenticated |
| POST | `/mark-all-read/` | Прочитать все уведомления | Authenticated |

---

## Фоновые задачи

| Метод | Endpoint | Описание | Доступ |
|-------|----------|----------|--------|
| GET | `/api/v1/tasks/{task_id}/status/` | Статус Celery-задачи (DB-backed) | Owner of task |

Задачи выполняются через Celery + Redis:
- `process_excel_import` -- парсинг и Smart Match Excel-спецификации
- `generate_quote_pdf` -- генерация PDF коммерческого предложения
- `bulk_catalog_import` -- массовый импорт товаров в каталог

---

## Примеры запросов

### Создание сделки

{{< tabs >}}
  {{< tab name="cURL" >}}
```bash
curl -X POST https://api.railtech.uz/api/v1/crm/deals/ \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Запрос на запчасти ТЭ33А",
    "priority": "standard",
    "comment": "Нужны запчасти для планового ТО"
  }'
```
  {{< /tab >}}
  {{< tab name="Python" >}}
```python
import requests

resp = requests.post(
    "https://api.railtech.uz/api/v1/crm/deals/",
    headers={"Authorization": f"Bearer {token}"},
    json={
        "title": "Запрос на запчасти ТЭ33А",
        "priority": "standard",
        "comment": "Нужны запчасти для планового ТО"
    }
)
deal = resp.json()
print(f"Deal ID: {deal['id']}, Stage: {deal['stage']}")
```
  {{< /tab >}}
  {{< tab name="JavaScript" >}}
```javascript
const resp = await fetch("https://api.railtech.uz/api/v1/crm/deals/", {
  method: "POST",
  headers: {
    "Authorization": `Bearer ${token}`,
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    title: "Запрос на запчасти ТЭ33А",
    priority: "standard",
    comment: "Нужны запчасти для планового ТО"
  })
});
const deal = await resp.json();
console.log(`Deal ID: ${deal.id}, Stage: ${deal.stage}`);
```
  {{< /tab >}}
{{< /tabs >}}

**Ответ (201 Created):**
```json
{
  "id": 42,
  "title": "Запрос на запчасти ТЭ33А",
  "stage": "DRAFT",
  "priority": "standard",
  "created_at": "2026-03-21T10:00:00Z"
}
```
