---
title: "Technical Specification"
linkTitle: "Technical Specification"
weight: 30
description: "Technology stack, database architecture, role model, algorithms and deployment infrastructure."
categories: ["Architecture"]
tags: ["architecture", "PostgreSQL", "Django", "React", "RBAC"]
---

## 1. Technology Stack
### 1.1. Backend (API & Core)
- **Language:** Python 3.11+
- **Framework:** Django 4.2+ / 5.x + Django REST Framework (DRF)
- **Database:** PostgreSQL 15+ (Extensions: `pg_trgm` for fuzzy search, `JSONB` for flexible attributes)
- **Task Queue:** Celery + Redis (Deal specification parsing and bulk catalog import via `pandas` in background)
- **Real-time:** `daphne`, Django Channels (WebSockets) + Redis Channel Layer for instant chat; Polling for Notification Center (Admin Panel).
- **Document Generation:** `WeasyPrint` for rendering PDF from HTML templates.
- **Architecture Pattern:** **Service Layer** (`services.py` layer) isolating business logic from controllers (Views).

### 1.2. Frontend (SPA)
- **Libraries:** React 18+, TypeScript
- **Styling:** Tailwind CSS
- **State Management:** Zustand
    - `authStore.ts`: Session and access rights management.
    - `themeStore.ts`: Theme management.
    - `domainStore.ts`: Active catalog context management (Domains).
    - `taskStore.ts`: Background task monitoring (Excel import/export).
- **Components:** Lucide React, native WebSockets for chat.

---

## 2. Database Architecture (Domain-Driven Taxonomy)

### 2.1. Global Domains
Products (`Product`) are strictly divided by the `domain_type` field (TextChoices):
1.  **RAILWAY_MACHINERY:** Requires binding to hierarchical equipment references.
2.  **GENERAL_GOODS:** General industrial goods (flat catalog, flexible `specifications`).
3.  **SERVICES:** Services (transport, repair). Uses non-standard units of measurement (`uom`).

### 2.2. Equipment Hierarchy (for the RAILWAY domain)
Implemented via `EquipmentGroup` with a self-referencing relationship (recursive Type -> Series hierarchy):
- Fields: `equipment_type`, `series`, and `parent` (`ForeignKey('self')`).

### 2.3. Key ER Models and Financial Fields
1. **`Company`**: `inn` (Unique Index), `verification_status` (L1/L2/L3).
2. **`Product`**: `sku`, `domain_type`, `base_price`, `currency` (FK), `uom` (FK unit of measurement).
3. **`Deal`**: `client`, `manager`, `status`. Access isolation at the `get_queryset` level.
4. **`DealItem`**:
    - `product` (Nullable for unrecognized Excel items).
    - Base fields: `quantity`, `fca_base_price`, `logistics_dap`, `customs_duty`, `margin_percent`.
    - **Physical Computed Fields (DecimalField):** `landed_cost`, `final_price_excl_vat`, `total_price_incl_vat`. Computed via Service Layer before saving.
5. **`Message`**: `deal`, `sender`, `text`, `timestamp`. Routing via Channels.
6. **`Notification`**: `user`, `title`, `message`, `type`, `is_read`. System and user alerts.
7. **`User` (Custom)**: `role`, `workspace_role`, `company` (FK), `company_id` (Write-only field in Serializer for invitation logic).

---

## 3. Role Model, Workspaces and Security
Authorization: JWT. Global policy: `IsAuthenticated`.

### 3.1. Workspace Roles (Within a Single Company)
| Role | Permissions |
| :--- | :--- |
| **OWNER** | Full Workspace control, employee invitations, account management. |
| **ACCOUNTANT** | View and export financial documents, payment status management. |
| **MANAGER** | Create deals, import Excel specifications, chat with RailTech managers. |

### 3.2. System Roles (RBAC)
Automatic QuerySet isolation at the `get_queryset` level.

| Role | Access Rights (QuerySet Isolation) |
| :--- | :--- |
| **GUEST** | Access to public catalog, `Deal` (limit: 1, Draft status). Prices hidden. |
| **CLIENT** | Manage strictly own deals (`filter(client=user)`). View prices, accept quotes (`QUOTE_SENT`), chat (WS). |
| **SALES_MANAGER / ACCOUNT_MANAGER** | Access to assigned or unassigned deals. Excel parsing, Sourcing Hub mapping, Quote Builder. |
| **ADMIN** | Full access to all data. Global analytics (Conversion, Target Margin, Sourcing Speed). |

---

## 4. Algorithms and Integrations

### 4.1. Domain-Aware Smart Match (Celery + pg_trgm)
**Endpoint:** `POST /api/v1/crm/deals/{id}/import_excel/`
1. **Parsing:** Celery worker `process_excel_import` reads Excel via `pandas` in the background.
2. **Auto-matching:** The `smart_match_product` function uses `TrigramSimilarity` (threshold `> 0.3`) on the `name` field.
3. **Manual Sourcing:** Unmatched items remain with `product=null`. The manager maps them manually via endpoint `PUT /api/v1/crm/deal-items/{id}/` in the Sourcing Hub interface.

### 4.2. PDF Export
**Endpoint:** `GET /api/v1/crm/deals/{id}/export_pdf/`
Uses `WeasyPrint` for generating commercial proposals.

### 4.3. Commercial Analytics
**Endpoint:** `GET /api/v1/crm/analytics/`
Returns aggregated metrics:
- `pipeline_by_status`: Deal distribution across the funnel.
- `sourcing_backlog`: Count of items with `product=null`.
- `conversion_rate`: Percentage of successfully closed deals.
- `total_pipeline_value`: Total value of the active pipeline.

---

## 5. Infrastructure and Deployment

### 5.1. Containerization (Docker)
The system is deployed as a set of containerized services:
- **`backend`**: Django/Daphne (Runtime: python:3.11-slim).
- **`frontend`**: React SPA (Served by nginx:alpine with SPA routing via `try_files`).
- **`celery_worker / beat`**: Background tasks and scheduling (runs as non-root `appuser`).
- **`redis`**: Task broker and Channel Layer.
- **`db`**: PostgreSQL 15.
- **`nginx`**: Global gateway (SSL, Gzip, WebSocket Proxy, reverse proxy to React container).

### 5.2. CI/CD Pipeline
Automation via GitHub Actions:
1.  **Stage 1 (Quality):** Linting and static code analysis (Backend + Frontend).
2.  **Stage 2 (Verify):** Automated tests with isolated environment.
3.  **Stage 3 (Build):** Docker image builds.
4.  **Stage 4 (Deploy):** Automated production deployment.

### 5.3. Environment Security
All sensitive data is stored in CI/CD secrets and injected as environment variables during deployment. Database access is restricted by network rules.
