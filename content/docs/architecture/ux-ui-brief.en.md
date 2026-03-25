---
title: "UX/UI Brief"
linkTitle: "UX/UI Brief"
weight: 50
description: "Design concept, visual paradigm and UI/UX requirements for all platform layers."
categories: ["Architecture"]
tags: ["UI/UX", "design"]
---

This brief describes the visual and behavioral requirements for the interfaces of the 4-layer RailTech B2B digital ecosystem, taking into account the Domain-Driven Architecture.

---

## 1. General Visual Paradigm (Design System)

### 1.1. Color Palette
*   **Primary:** Strict industrial blue (`slate-900` / `#0f172a`) and accent blue (`blue-600` / `#2563eb`). Symbolizes reliability and technology.
*   **Background:** Light gray (`slate-50` / `#f8fafc`) to reduce visual load when working with large data tables. Pure white (`white` / `#ffffff`) for working blocks (cards, grids).
*   **Semantic Colors:**
    *   *Warning/Hold:* `amber-500` (e.g., "Awaiting calculation", "Made to order").
    *   *Success:* `emerald-600` / `green-500` (e.g., "In stock", "Contract signed").

### 1.2. Typography and Components
*   **Font:** *Inter* (Google Fonts) — a modern sans-serif with excellent readability for numbers and B2B technical terms.
*   **Component Styling:** Rounded corners (`rounded-xl` / `rounded-2xl` for dashboards), light shadows (`shadow-sm`) for layer separation. Isolation of functional zones (Split-screens).
*   **Icons:** *Lucide (lucide-react)* — outline icons with 2px stroke width.

---

## 2. Layer 2: B2B Catalog (`b2b.railtech.uz`)

The catalog's main goal is to simplify the search for complex equipment and consumables while hiding prices from unauthorized users.

### 2.1. Adaptive Filter (Domain-Adaptive Sidebar)
*   The sidebar dynamically reconfigures depending on the selected Domain (Railways / Goods / Services).
*   **For Railway Domain:** Strict tree by `EquipmentGroup` (Type -> Locomotive Series) + Filter by manufacturer.
*   **Collapsible Blocks:** Using accordions for hiding long filter lists.
*   **Quick Actions:** "In stock" / "Made to order" checkboxes.

### 2.2. Product Card
*   **Grid View:** Designed for browsing. Photo (with availability status badge in the corner), GOST/Drawing reference, Manufacturer, prominent "Add to Request" button.
*   **Table View:** Designed for wholesale procurement specialists (dense UI). Thumbnail photo (48x48), GOST, Stock availability, quick "Add to Request" button.
*   **Empty State:** If no products match the filters, a hint about Excel integration is shown: "RailTech offers a 'Request by Specification' feature...".

---

## 3. Layer 3: Client Cabinet (`cabinet.railtech.uz`)

The procurement specialist's workspace for managing requests and documents.

### 3.1. Layout and Navigation
*   **Left Sidebar:** Dark theme (`slate-900`). Smart structure (Dashboard, Requests, Quotes, Contracts, Documents). Unread notification indicator (red badge) or KYC pending indicator (pulsing yellow `animate-pulse`). Collapses to a narrow strip (`w-16`).
*   **Dashboard:** Statistics block with 4 cards (In Progress, Quotes, Contracts, Annual Amount). Visual alerts about profile status. "Activity History" table in the center.

### 3.2. Deal View (Split-screen)
Solves the context loss problem. In `no_deals` status, two giant CTA cards are displayed ("Import Excel" and "Browse Catalog").
*   **Left Zone (70%):** Request specification (Excel + PIM). Product separation by Domains with visual blocks. At `QUOTE_SENT` status, an **Amber Action Banner** appears with the highlighted quote amount and "Accept Quote" / "Ask in Chat" buttons.
*   **Right Zone (30%):** Sticky module with Chat (WebSockets) and PDF Documents tabs.

### 3.3. FAB (Floating Action Button)
*   Floating button in the bottom-right corner. On click, opens a support menu: "Write to Manager", "Call", "Telegram Support Bot".

---

## 4. Layer 4: CRM / Backoffice (Admin Panel)

Environment for RailTech employees. Direct access to powerful tools and calculators. The manager's workspace must not lag when rendering 500+ rows.

### 4.1. CRM Layout (Admin Deal View)
*   **Analytics Dashboard:** Set of KPI cards (Status Pipeline, Pipeline Value, Conversion).
*   **Sidebar:** Thin pinned sidebar (`w-16`), expanding (`hover:w-64`) on cursor hover.
*   **KYC / Client Card:** In the left column — quick counterparty card with "Confirm KYC" button.
*   **Sourcing Hub:** Bento Grid of all unrecognized products requiring manager expertise, with a built-in modal window for searching the Master Catalog.

### 4.2. Quote Builder (Landed Cost Calculator)
The most complex UI module of the platform (Data Grid).
*   **Sticky Headers.** Allow scrolling through hundreds of specification rows (including unrecognized ones from Excel).
*   **Visual Separator (Column Background Color):**
    *   *Internal Zone (Cost):* "Purchase (FCA)", "Customs+Logistics (DAP)" columns highlighted with gray background (`bg-slate-50`).
    *   *External Zone (Client Price):* "Margin (%)" column and final "Price", "Amount" aligned and clearly highlighted with white background.
*   **Totalling Panel:** Dark filled block (`bg-slate-900`) at the bottom of the screen. Shows the company's net profit in `UZS` (in green), amortizing the margin, and the final "Total Payable with 12% VAT" amount (for the client).
*   **Domain Segregation:** The table must visually group items (Railway row, General Goods row, "Services" row — services hide Customs/DAP inputs since margin is calculated from the base rate).
