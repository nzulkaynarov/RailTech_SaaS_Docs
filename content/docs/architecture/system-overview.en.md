---
title: "System Overview"
linkTitle: "System Overview"
weight: 10
description: "Complete architecture, business logic and technical requirements for the RailTech B2B platform."
categories: ["Architecture"]
tags: ["architecture", "Django", "React"]
---

This document describes the complete architecture, business logic and technical requirements for the universal B2B comprehensive supply platform of RailTech LLC (Uzbekistan, Tashkent). The system automates the entire sales cycle: from lead generation to issuing Commercial Proposals (Quotes) with complex import unit economics.

---

## 1. System Topology (4 Layers)

1. **Layer 1: Landing Page (`railtech.uz`)** -- Static marketing website located in the `/landing` directory.
2. **Layer 2: B2B Catalog (`b2b.railtech.uz`)** -- Public digital storefront with faceted search. The catalog adapts to the selected Domain (Railway, General Goods, Services). Prices are hidden.
3. **Layer 3: Client Cabinet (`cabinet.railtech.uz`)** -- Private portal. Registration by TIN (automatic L2 verification), team management (Workspace Roles), Excel specification import, WebSocket chat, Quote approval (Approve Quote).
4. **Layer 4: CRM / Backoffice (Admin Panel)** -- RailTech staff workspace. Deal verification, Sourcing Hub (product mapping), Quote Builder, Commercial Analytics, Product Catalog Editor, **Bulk catalog import (Celery/Excel)**, **Real-time Notification Center** and **Account Management**.

---

## 2. Business Logic (User Journey)

### 2.1. Data Isolation and Security (Strict Data Isolation)
The system enforces a strict `IsOwnerOrManager` security policy. All API requests are filtered at the database level: Clients (`CLIENT`) see only their own documents, and Managers (`SALES_MANAGER`, `ACCOUNT_MANAGER`) see only their assigned contracts. This prevents competitor data leaks.

### 2.2. Request Formation and Domain-Aware Smart Match
1. **Catalog:** Adding products directly from the database (via the "Add to Request" button). Bulk catalog imports via Excel are available in the Product Catalog Editor.
2. **Excel Import (Asynchronous):** The client uploads a specification. The backend recognizes line items using fuzzy search (`pg_trgm`, Similarity > 0.3) and automatically distributes them across Domains.
3. **Manual Sourcing:** Unrecognized items are sent to the **Sourcing Center** (Layer 4), where managers map them to the master catalog.
4. **Approve Quote:** Once prices are set, the client receives an interactive banner to confirm the order.

### 2.3. Unit Economics and Pricing (Physical Landed Cost)
Quote calculation is automated and stored in physical database columns (`DecimalField`) for analytics:
1. **Procurement (FCA):** Factory price (or base contractor rate for `SERVICES`).
2. **Overheads (DAP + Duty):** Logistics, insurance, and customs costs.
3. **Landed Cost:** `FCA + Logistics DAP + Customs Duty`.
4. **Client Price (Excl VAT):** `Landed Cost * (1 + Margin %)`.
5. **Final Price:** + VAT (12%).
The system generates PDF Commercial Proposals via the `WeasyPrint` library.

### 2.4. Real-time Contextual Chat (WebSockets)
Instead of classic REST polling, the chat runs on **Django Channels (ASGI)**. Conversations are strictly isolated by `deal_id`. Messages are delivered instantly without page reloads.

---

## 3. Database Architecture (Domain-Driven Taxonomy)

### 3.1. Global Domains
The catalog is strictly divided into **3 isolated Domains**:
1. **RAILWAY_MACHINERY (Railway and Special Equipment):** Strict registry. Requires product binding to the hierarchical `EquipmentGroup` reference (supports recursive Type -> Series hierarchy).
2. **GENERAL_GOODS (General Goods):** Flat catalog without strict equipment binding. Flexible attributes.
3. **SERVICES (Services):** Uses custom units of measurement (`uom`). Logistics fields are ignored when calculating Landed Cost.

### 3.2. Role Model and Workspaces
The system supports multi-user workspaces (`CompanyWorkspace`).

**Workspace Roles:**
- **OWNER** (Full access)
- **ACCOUNTANT** (Financial operations)
- **MANAGER** (Procurement)

**System Roles (RBAC):**
- `CLIENT` (Client workspace)
- `SALES_MANAGER` (RailTech sales)
- `ACCOUNT_MANAGER` (RailTech account management)
- `ADMIN` (Super administrator)

---

## 4. Technology Stack
- **Frontend:** React.js (TypeScript), Tailwind CSS, Zustand, Native WebSockets.
- **Backend:** Python 3.11+, Django 4.2/5.x, DRF, Daphne, Django Channels.
- **Database:** PostgreSQL 15+ (pg_trgm).
- **Infrastructure:** Redis (task broker and Channel Layer), Celery (background workers).
