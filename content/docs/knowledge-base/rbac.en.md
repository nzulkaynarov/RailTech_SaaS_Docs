---
title: "Role Model (RBAC)"
linkTitle: "RBAC"
weight: 30
description: "System roles, Workspace Roles and database-level access control."
categories: ["Knowledge Base"]
tags: ["RBAC", "security", "roles"]
---

Access to the ecosystem is strictly regulated at the database and API levels. Data isolation is ensured by custom permissions (`IsOwnerOrManager`) and `get_queryset` overrides in all ViewSets: users can never access other users' data even with direct ID queries.

---

## External Perimeter (Client Dashboard)

### 1. GUEST (Visitor / Lead)
- **Rights:** B2B catalog browsing (prices hidden), creation of 1 `Deal` entity (Draft status).
- **Restrictions:** No price viewing until Charter upload (L2 KYC). No new deals, no PDF document downloads.

### 2. CLIENT (Verified Client)
- **Rights:** Unlimited request creation, WebSocket chat access, Excel specification upload, price viewing, order confirmation (Accept Quote, `QUOTE_SENT` status).
- **Isolation:** Sees strictly their own deals (`Deal.objects.filter(client=user)`).

---

## Internal Perimeter (CRM / Backoffice)

### 3. SALES_MANAGER
- **Area of Responsibility:** Top of the funnel, request processing and Sourcing.
- **Rights:** KYC verification, `Deal` processing, async Excel parsing (Celery), Sourcing Grid work (for unrecognized products), Quote Builder, quote issuance (WeasyPrint PDF generation).
- **Isolation:** Sees their assigned + unassigned deals (`Q(manager=user) | Q(manager__isnull=True)`).

### 4. ACCOUNT_MANAGER
- **Area of Responsibility:** Farming and account management.
- **Rights:** Same as Sales Manager (including Sourcing and Quote Builder), plus converting approved quotes to Contracts, delivery status control, attaching closing documents.

### 5. ADMIN (Super Administrator)
- **Area of Responsibility:** Platform orchestration.
- **Rights:** Full CRUD access (no `get_queryset` filtering). Directory management (Domains, `EquipmentGroup` hierarchy), employee rights assignment, access to global financial analytics, user lifecycle management (blocking, invitations).

---

## Workspace Roles (Within a Single Company)

| Role | Rights |
| :--- | :--- |
| **OWNER** | Full Workspace control, employee invitations, account management. |
| **ACCOUNTANT** | Viewing and exporting financial documents, payment status management. |
| **MANAGER** | Deal creation, Excel specification import, chat with RailTech managers. |

---

## Access Summary Table

| Role | Deals | Catalog | Users | Admin |
|------|-------|---------|-------|-------|
| Guest | No | Read | No | No |
| Client | Own only | Read | Own profile | No |
| Sales Manager | Assigned / Unassigned | Read + Write | No | No |
| Admin | All | Full | Full | Full |

---

## Notifications (Notification Center)

- **GUEST/CLIENT:** Receive notifications about their deal statuses and system alerts.
- **MANAGER/SALES:** Receive notifications about newly assigned deals, client messages and background task completion (Excel import).
- **ADMIN:** Receives notifications about new company registrations requiring KYC verification and critical system failures.
