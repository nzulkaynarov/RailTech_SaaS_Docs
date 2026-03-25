---
title: "Catalog Domains"
linkTitle: "Catalog Domains"
weight: 10
description: "Three global product domains: Railway Machinery, General Goods and Services."
categories: ["Knowledge Base"]
tags: ["domains", "catalog"]
---

The RailTech platform catalog is strictly divided into **3 isolated Domains**. Each product (`Product`) has a `domain_type` field (TextChoices) that determines its behavior in the system.

---

## 1. RAILWAY_MACHINERY (Railway and Special Equipment)

**Purpose:** Strict registry of railway equipment and spare parts.

| Parameter | Value |
|-----------|-------|
| EquipmentGroup FK | **Required** |
| JSONB Attrs | Partial |
| Strict hierarchy | Yes |

### Features
- Requires linking the product to a hierarchical `EquipmentGroup` directory
- Supports recursive hierarchy: Type -> Series (e.g., Diesel locomotive -> TE33A)
- The `EquipmentGroup` model uses `ForeignKey('self')` for recursion
- Fields: `equipment_type`, `series`, `parent`
- Strict filters by equipment type and manufacturer are available in the catalog
- On save, the presence of `equipment_groups` is validated via M2M signal

### Product examples
- Traction motor ED-118A for TE33A diesel locomotive
- Wheelset GOST 11018-2000
- Locomotive brake pad

---

## 2. GENERAL_GOODS (General Goods)

**Purpose:** Flat catalog of industrial goods without strict binding to equipment.

| Parameter | Value |
|-----------|-------|
| EquipmentGroup FK | No |
| JSONB Attrs | **Yes** |
| Strict hierarchy | No |

### Features
- Does not require binding to `EquipmentGroup`
- Uses flexible JSONB attributes (`specifications`) for storing characteristics
- Filtering by categories and arbitrary attributes
- Supports a flat category structure

### Product examples
- Power cable VVGng 3x2.5
- Bearing 6310-2RS
- Industrial oil I-40A

---

## 3. SERVICES (Services)

**Purpose:** Service works with their own pricing model.

| Parameter | Value |
|-----------|-------|
| EquipmentGroup FK | No |
| JSONB Attrs | No |
| Strict hierarchy | No |

### Features
- Uses custom units of measure (`uom`): man-hours, trips, work sets
- **Simplified price calculation:** logistics (`logistics_dap`) and customs (`customs_duty`) fields are ignored
- Formula: `Base Rate * (1 + Margin %) * 1.12`
- Quote Builder hides Customs/DAP inputs

### Service examples
- Locomotive overhaul
- Oversized cargo transport
- Diagnostics and defectoscopy

---

## Domain validation

On every `Category` and `Product` save, the system validates `domain_type`:

- **RAILWAY_MACHINERY**: validates the link to `EquipmentGroup` (post-save M2M signal)
- **GENERAL_GOODS**: allows JSONB attributes, does not require `EquipmentGroup`
- **SERVICES**: prohibits logistics fields in Landed Cost calculations

Violation of domain rules blocks the record from being saved.
