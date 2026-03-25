---
title: "API Endpoints"
linkTitle: "Endpoints"
weight: 20
description: "Overview of REST API endpoints for the RailTech B2B platform."
categories: ["API"]
tags: ["REST API", "endpoints", "RBAC"]
---

All endpoints are available at the base URL `/api/v1/`. Authorization: JWT Bearer token (unless otherwise specified).

---

## Accounts (`/api/v1/accounts/`)

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| POST | `/register/` | Company registration by TIN | Public |
| POST | `/login/` | Obtain JWT tokens | Public |
| POST | `/token/refresh/` | Refresh access token | Public |
| GET | `/me/` | Current user profile | Authenticated |
| GET/PUT | `/users/` | Workspace user management | Owner/Admin |

---

## Catalog (`/api/v1/catalog/`)

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/products/` | Product list (faceted search) | Public (prices hidden for Guest) |
| GET | `/products/{id}/` | Product details | Public |
| GET | `/categories/` | Category tree | Public |
| GET | `/equipment-groups/` | Equipment hierarchy (Railway domain) | Public |
| GET | `/products/count/` | Product count by domains | Public |
| POST | `/products/import/` | Bulk catalog import (Excel) | Admin |

---

## CRM (`/api/v1/crm/`)

### Deals

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/deals/` | Deal list (RBAC filtering) | Authenticated |
| POST | `/deals/` | Create a deal | Client/Manager |
| GET | `/deals/{id}/` | Deal details | Owner/Manager |
| POST | `/deals/{id}/import_excel/` | Import Excel specification (Celery) | Client/Manager |
| GET | `/deals/{id}/export_pdf/` | Export quote as PDF (WeasyPrint) | Manager/Admin |
| POST | `/deals/{id}/send-quote/` | Send quote to client | Manager/Admin |
| POST | `/deals/{id}/approve-quote/` | Client quote approval | Client |
| POST | `/deals/{id}/reject-quote/` | Quote rejection | Client |

### Deal Items

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/deal-items/` | Deal items list | Owner/Manager |
| PUT | `/deal-items/{id}/` | Update item (Sourcing mapping) | Manager/Admin |
| PATCH | `/deal-items/{id}/` | Partial update (prices, margin) | Manager/Admin |

### Analytics

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/analytics/` | Aggregated metrics | Manager/Admin |

Returned metrics:
- `pipeline_by_status` — deal distribution across the pipeline
- `sourcing_backlog` — number of items without product binding
- `conversion_rate` — percentage of successfully closed deals
- `total_pipeline_value` — total pipeline value

---

## Notifications (`/api/v1/notifications/`)

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/` | User notification list | Authenticated |
| PATCH | `/{id}/` | Mark as read | Authenticated |
| POST | `/mark-all-read/` | Mark all notifications as read | Authenticated |

---

## Background Tasks

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/api/v1/tasks/{task_id}/status/` | Celery task status (DB-backed) | Task owner |

Tasks are executed via Celery + Redis:
- `process_excel_import` — parsing and Smart Match of Excel specification
- `generate_quote_pdf` — PDF commercial quote generation
- `bulk_catalog_import` — bulk product import to catalog

---

## Request Examples

### Creating a Deal

{{< tabs >}}
  {{< tab name="cURL" >}}
```bash
curl -X POST https://api.railtech.uz/api/v1/crm/deals/ \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Request for TE33A spare parts",
    "priority": "standard",
    "comment": "Parts needed for scheduled maintenance"
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
        "title": "Request for TE33A spare parts",
        "priority": "standard",
        "comment": "Parts needed for scheduled maintenance"
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
    title: "Request for TE33A spare parts",
    priority: "standard",
    comment: "Parts needed for scheduled maintenance"
  })
});
const deal = await resp.json();
console.log(`Deal ID: ${deal.id}, Stage: ${deal.stage}`);
```
  {{< /tab >}}
{{< /tabs >}}

**Response (201 Created):**
```json
{
  "id": 42,
  "title": "Request for TE33A spare parts",
  "stage": "DRAFT",
  "priority": "standard",
  "created_at": "2026-03-21T10:00:00Z"
}
```
