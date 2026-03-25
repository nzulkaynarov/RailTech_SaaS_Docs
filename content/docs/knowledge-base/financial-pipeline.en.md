---
title: "Financial Pipeline"
linkTitle: "Financial Pipeline"
weight: 20
description: "Landed Cost calculation, margin and pricing for commercial quotes."
categories: ["Knowledge Base"]
tags: ["finance", "Landed Cost", "VAT"]
---

{{< callout type="warning" >}}
**Critical Rule:** All financial calculations are performed exclusively on the server (Django backend). The frontend never calculates prices, VAT, margin or Landed Cost. Violating this rule is a critical vulnerability.
{{< /callout >}}

The end-to-end cost calculation is implemented on the backend in the `crm.services` layer. All financial calculations are performed exclusively on the server — the frontend never calculates prices.

The `landed_cost` and final price fields are stored in physical database columns (`DecimalField`) to support aggregation.

---

## Formula for Physical Goods (RAILWAY_MACHINERY, GENERAL_GOODS)

```
FCA Base Price (Factory purchase price)
  + Logistics DAP (Logistics to Tashkent)
  + Customs Duty
  = Landed Cost
    x (1 + Margin %)
    = Final Price (excl. VAT)
      x 1.12
      = Total Price (incl. 12% Uzbekistan VAT)
```

### Step by Step

| Step | Field | Formula |
|------|-------|---------|
| 1 | `fca_base_price` | Net cost at the manufacturer's factory |
| 2 | `logistics_dap` | Logistics and insurance costs to DAP Tashkent |
| 3 | `customs_duty` | Customs duty |
| 4 | `landed_cost` | `fca_base_price + logistics_dap + customs_duty` |
| 5 | `final_price_excl_vat` | `landed_cost * (1 + margin_percent / 100)` |
| 6 | `total_price_incl_vat` | `final_price_excl_vat * 1.12` |

---

## Formula for Services (SERVICES)

For the SERVICES domain, logistics and customs are **ignored**:

```
Base Rate (Contractor rate)
  x (1 + Margin %)
  = Final Price (excl. VAT)
    x 1.12
    = Total Price (incl. 12% Uzbekistan VAT)
```

---

## Technical Details

### Storage
- All monetary and percentage fields use `DecimalField` (not `FloatField`)
- Fields `landed_cost`, `final_price_excl_vat`, `total_price_incl_vat` are physical columns computed via Service Layer before saving
- This ensures accuracy of database-level aggregation (`SUM`, `AVG`)

### Audit
- The `QuoteItemHistory` model stores an immutable audit trail of all financial data changes
- Every price or margin change is recorded for subsequent analysis

### VAT Rate

{{< callout type="info" >}}
Uzbekistan VAT: **12%** (fixed rate). In the future, it is planned to be moved to `PlatformSetting` for admin panel configuration (Sprint 3).
{{< /callout >}}

### Quote Builder UI
- Internal zone (cost): FCA, DAP, Customs columns — gray background
- External zone (client price): Margin, Price, Amount — white background
- Totalling Panel: company's net profit (green) + total with VAT for client

### Document Generation
- Commercial quote PDFs are generated via `WeasyPrint`
- Endpoint: `GET /api/v1/crm/deals/{id}/export_pdf/`
