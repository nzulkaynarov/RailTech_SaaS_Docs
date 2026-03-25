---
title: "Business Logic"
linkTitle: "Business Logic"
weight: 20
description: "Business process architecture: KYC, Smart Match, Landed Cost financial model, chat and notifications."
categories: ["Architecture"]
tags: ["architecture", "Landed Cost", "Smart Match", "KYC"]
---

The RailTech LLC ecosystem automates the full cycle of B2B comprehensive industrial supply for enterprises in the Uzbekistan market.

## 1. Acquisition Funnel and KYC (Automated Trust)

1.  **Level: OWNER / CLIENT (Registration by TIN)**
    *   **Entry:** Email, password, company TIN and company name.
    *   **Process:** Upon registration the system creates a `Company` with status `PROFILE_SUBMITTED` (Profile submitted for review).
    *   **Verification:** A RailTech manager must manually verify the TIN and activate the company (`L2_VERIFIED`) through the CRM panel. After this the user receives the **Owner** role and full access.
    *   **Capabilities:** After verification -- unlimited requests, access to real-time chat (WebSockets), catalog price visibility, team management.
2.  **Level: Workspace Roles (Teamwork)**
    *   **Owner:** Can invite employees and manage access permissions.
    *   **Manager:** Creates requests and handles deal correspondence.
    *   **Accountant:** Works with closing documents and invoices.
3.  **Level: Contracting (Partner Status)**
    *   **Trigger:** Agreement on terms (clicking "Accept Quote") and PDF signing. Status: `L3_CONTRACTED`.
    *   **Data:** Full banking details (account number, bank code), legal address.

## 2. Demand Formation (Domain-Aware Smart Match)

*   **Catalog Order:** Direct addition of items from the PIM database through the portal interface. Bulk catalog replenishment by administrators via Excel import in the Admin Panel.
*   **Rendering Optimization:** Catalog metadata (e.g., total product count per domain) is delivered via lightweight `getProductsCount` API requests, reducing database load.
*   **Asynchronous Specification Import (Excel/Celery):**
    *   The user uploads a single specification file. The task is submitted to the Redis/Celery queue.
    *   **Algorithm (pg_trgm):** The worker matches lines with `Trigram Similarity > 0.3` and distributes them across Domains:
        *   Railway Domain (Railway/Machinery), General Goods, Services.
    *   Unrecognized items are saved with `product_id=null` and status "Searching for supplier". Managers process them in a dedicated **Sourcing Grid** interface.

### 2.1. Deal Status Funnel (Deal Statuses)
The system strictly guides each deal through defined stages; metrics are collected in the `AnalyticsDashboard`:
*   `DRAFT` (Draft)
*   `KYC_PENDING` (Awaiting document verification)
*   `CALCULATING` (Manager assembles prices in Quote Builder or searches in Sourcing Grid)
*   `QUOTE_SENT` (Quote issued -- client sees Action Banner "Accept Quote")
*   `CONTRACT_SIGNED` (Contract signed)

## 3. Financial Model (Landed Cost & Quote Builder)

End-to-end cost calculation is implemented on the backend in the `crm.services` layer. The `landed_cost` and final price fields are stored in physical `DecimalField` columns to support database aggregation:

1.  **FCA Basis (Procurement):** Net factory price (or base contractor rate).
2.  **Overhead Costs (DAP Tashkent):** Logistics, insurance and customs costs (`customs_duty`). *For the SERVICES domain, logistics and customs are ignored.*
3.  **Landed Cost:** `FCA + Logistics DAP + Customs Duty`.
4.  **Margin Application:** `Landed Cost * (1 + Margin %) = Final Price Excl VAT`.
5.  **Taxation:** `Final Price Excl VAT * 1.12 = Total Price Incl 12% VAT`.

**Document Generation:** Professional PDF generation is performed on-the-fly via the `WeasyPrint` library.

## 4. Contextual Communications (Real-time Deal Chat)

*   **Technology:** Django Channels (ASGI) and WebSockets via Redis. Replaces classic REST polling.
*   **Context Binding:** Conversations are strictly isolated within room `chat_{deal_id}`.
*   **Isolation:** The server-side Consumer (`ChatConsumer`) verifies access level: clients see only their own deals, managers see only their assigned deals.

## 5. Notification System (Notification Center)

*   **Centralized Hub:** All important system events (new registrations, manager assignments, Celery task status updates) generate a `Notification` object.
*   **Interface:** The admin panel features a notification panel with an unread message indicator and quick navigation to the relevant object (deal or user).
*   **Delivery:** The current implementation uses optimized polling; a future upgrade to WebSocket push notifications is planned.
