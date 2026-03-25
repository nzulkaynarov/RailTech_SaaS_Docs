---
title: "Interface Architecture"
linkTitle: "Interface Architecture"
weight: 40
description: "UI/UX architecture of the 4 system layers: Landing, Catalog, Client Cabinet, CRM."
categories: ["Architecture"]
tags: ["architecture", "React", "UI/UX"]
---

## Layer 1: Landing Page (`railtech.uz`)
- **Type:** Static marketing website.
- **Purpose:** SEO traffic, company presentation as a unified supplier for industry (Equipment, Materials, Services), transition to the B2B portal.

## Layer 2: B2B Catalog (`b2b.railtech.uz`)
- **Type:** Public SPA (React). Prices are hidden.
- **Interfaces:**
  - *Faceted Search (Domain-Adaptive):* The sidebar with filters dynamically changes depending on the selected Domain. For Railway, strict filters by `EquipmentGroup` appear (Diesel locomotives / Electric locomotives / Rolling stock). For general goods — filters by categories and attributes.
  - *Multi-View:* Switching between "Compact Grid" and "Table" (Data Grid) views. Empty catalog states explicitly suggest the possibility of uploading Excel specifications.
  - *Quick Order:* Fast addition of products and services to the request via the "Add to Request" button.

## Layer 3: Client Cabinet (`cabinet.railtech.uz`)
- **Type:** Private SPA (React).
- **Interfaces:**
  - *Dashboard:* Interactive KPI widgets (acting as filters for the activity history table).
  - *Navigation:* Collapsible Sidebar for expanding the workspace. For L1 profiles, a bright progress bar and a call to upload the Company Charter are displayed.
  - *Deal View (Details):* Split-screen (70/30). Left side — specification with visual grouping of items by Domains. In empty state, large CTAs "Import Excel" and "Go to Catalog" are offered. If the deal status is `QUOTE_SENT`, the client sees an Action Banner with the "Accept Quote" button. Right side — contextual chat (WebSockets) and PDF documents area.

## Layer 4: Admin Panel / CRM
- **Type:** Protected SPA (React + Tailwind CSS + Lucide React) / Custom Django Admin (Unfold).
- **Interfaces:**
  - *Sidebar:* Dark theme, expands on hover (Hover-expand).
  - *KYC Pane:* Counterparty card with a verification button.
  - *Quote Builder:* Complex table with unit economics calculation (Purchase -> Overhead -> Cost -> Price). Visual separation of Railway equipment, general goods and services items. Internal margin is hidden from external exports.
  - *Sourcing Hub:* Module for manual mapping of imported but unrecognized items from Excel (`product=null`).
  - *Commercial Analytics:* Dashboards with Conversion Rate, Pipeline Volume, and deal statuses.
