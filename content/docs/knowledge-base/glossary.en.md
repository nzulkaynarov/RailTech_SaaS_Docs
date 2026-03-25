---
title: "Glossary"
linkTitle: "Glossary"
weight: 40
description: "Terms, abbreviations and definitions used in the RailTech B2B platform."
categories: ["Knowledge Base"]
tags: ["glossary", "terms"]
---

Reference of terms and abbreviations used in the documentation and code of the RailTech B2B Ecosystem platform.

---

## Business Terms

| Term | Definition |
|------|------------|
| **B2B** | Business-to-Business — sales model between legal entities |
| **Quote** | Commercial quote (Russian: KP) — document with prices and conditions for the client |
| **TIN** | Taxpayer Identification Number (Russian: INN) — unique company identifier in Uzbekistan |
| **KYC** | Know Your Customer — client verification procedure (TIN check, Charter review) |
| **FCA** | Free Carrier — Incoterms delivery basis, price at the manufacturer's factory |
| **DAP** | Delivered at Place — Incoterms delivery basis, delivery to Tashkent |
| **Landed Cost** | Total cost of goods including logistics and customs |
| **VAT** | Value Added Tax (12% in Uzbekistan) |
| **PIM** | Product Information Management — master catalog management |
| **Sourcing** | Process of finding and mapping products from suppliers |
| **Smart Match** | Automatic matching of Excel items with the master catalog via pg_trgm |
| **Pipeline** | Sales pipeline — aggregate of active deals at various stages |
| **UOM** | Unit of Measure — measurement unit (pcs, kg, m, man-hour) |
| **SKU** | Stock Keeping Unit — unique product article number |

---

## Domains

| Term | Definition |
|------|------------|
| **RAILWAY_MACHINERY** | Railway and special equipment domain — strict registry with EquipmentGroup binding |
| **GENERAL_GOODS** | General industrial goods domain — flat catalog with JSONB attributes |
| **SERVICES** | Services domain — simplified pricing without logistics and customs |
| **EquipmentGroup** | Hierarchical equipment directory (Type -> Series) for the Railway domain |

---

## Roles and Access

| Term | Definition |
|------|------------|
| **RBAC** | Role-Based Access Control — role-based access control model |
| **GUEST** | Unregistered/unverified user with minimal rights |
| **CLIENT** | Verified client with access to their own deals |
| **SALES_MANAGER** | RailTech sales manager, works with pipeline and Sourcing |
| **ACCOUNT_MANAGER** | RailTech account manager, account management and contracting |
| **ADMIN** | Super administrator with full access |
| **OWNER** | Workspace role: client company owner |
| **ACCOUNTANT** | Workspace role: client company accountant |
| **MANAGER** | Workspace role: client company procurement specialist |

---

## Company Verification Statuses

| Status | Description |
|--------|-------------|
| **L1 (PROFILE_SUBMITTED)** | Profile submitted — awaiting manager review |
| **L2 (L2_VERIFIED)** | Verified — TIN checked, full platform access |
| **L3 (L3_CONTRACTED)** | Contracted — agreement signed, full bank details provided |

---

## Deal Stages

| Status | Description |
|--------|-------------|
| **DRAFT** | Draft — deal created, items being added |
| **KYC_PENDING** | Awaiting company document verification |
| **CALCULATING** | Manager is calculating prices in Quote Builder |
| **QUOTE_SENT** | Quote issued — client sees the Action Banner |
| **CONTRACT_SIGNED** | Contract signed — deal closed |

---

## Technical Terms

| Term | Definition |
|------|------------|
| **DRF** | Django REST Framework — library for building REST APIs |
| **ASGI** | Asynchronous Server Gateway Interface — protocol for async Django |
| **Django Channels** | Django extension for WebSocket communications |
| **Celery** | Distributed task queue for background processing |
| **pg_trgm** | PostgreSQL extension for fuzzy search using trigrams |
| **JSONB** | Binary JSON data type in PostgreSQL for flexible attributes |
| **WeasyPrint** | Python library for generating PDFs from HTML templates |
| **Zustand** | Lightweight state management library for React |
| **FSM** | Finite State Machine — for validating status transitions |
| **Service Layer** | Architectural pattern: `services.py` layer isolates business logic from Views |
