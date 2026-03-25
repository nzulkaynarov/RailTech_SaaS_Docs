---
title: "Quote Builder — Cost Calculation"
linkTitle: "Quote Builder"
weight: 30
description: "How to use Quote Builder for Landed Cost calculation and commercial quote generation"
categories: ["Guides"]
tags: ["Quote Builder", "Landed Cost", "quote", "finance"]
---

Quote Builder is a tool for calculating the final cost of goods, factoring in logistics, customs, margin, and VAT. The result is a ready-made commercial quote for the client.

## Pricing Formula

For goods (railway equipment and general goods):

```
FCA Price (supplier base price)
  + Logistics DAP (delivery to Uzbekistan)
  + Customs Duty
  = Landed Cost (delivered cost of goods)
    x (1 + Margin %)
    = Price excl. VAT
      x 1.12
      = Total Price with VAT (12%)
```

For services (simplified formula):

```
Base Rate x (1 + Margin %) x 1.12 = Total Price with VAT
```

{{< callout type="warning" >}}
All financial calculations are performed on the server. Never calculate final prices manually — use Quote Builder.
{{< /callout >}}

## Working with Quote Builder

### Step 1. Open Quote Builder

In the deal card at the **CALCULATING** stage, navigate to the **"Quote Builder"** tab or click the **"Calculate Cost"** button.

<!-- TODO: replace with screenshot of the Quote Builder interface -->
![Quote Builder — item table with FCA prices and calculations](/images/screenshots/placeholder.svg)

### Step 2. Check Base Prices

For each item, the following is displayed:

| Field | Description |
|-------|-------------|
| Product | Name and article number |
| Supplier | Selected supplier |
| FCA Price | Base price from the supplier |
| Currency | Supplier's currency |
| Rate | Current conversion rate |
| Qty | Number of units |

Make sure each item has a supplier selected and an up-to-date price.

### Step 3. Set Logistics Parameters

Fill in the delivery parameters:

| Parameter | Description |
|-----------|-------------|
| **Delivery Cost** | Total transportation cost (can be per batch or per item) |
| **Delivery Terms** | DAP (Delivered at Place) |
| **Delivery Method** | Rail, road, sea, multimodal |
| **Country of Origin** | Where the goods are shipped from |

<!-- TODO: replace with screenshot of logistics parameters in Quote Builder -->
![Logistics parameters — delivery cost, method, country of origin](/images/screenshots/placeholder.svg)

### Step 4. Set Customs Costs

| Parameter | Description |
|-----------|-------------|
| **Duty Rate** | Percentage of the goods value (depends on the HS code) |
| **Customs Fee** | Fixed processing fee |
| **Certification** | Certification costs (if applicable) |

### Step 5. Set the Margin

Specify the RailTech markup percentage:

- **Standard margin** — applied to all items
- **Individual margin** — can be set separately for each item

{{< callout type="info" >}}
The recommended margin range depends on the product category and deal volume. Consult the sales department head for approval.
{{< /callout >}}

### Step 6. Review the Calculation

Quote Builder automatically calculates and displays:

| Indicator | Formula |
|-----------|---------|
| **Landed Cost** | FCA + Logistics + Customs |
| **Sale Price (excl. VAT)** | Landed Cost x (1 + Margin%) |
| **VAT 12%** | Sale Price x 0.12 |
| **Total with VAT** | Sale Price x 1.12 |
| **Margin (amount)** | Sale Price - Landed Cost |
| **Margin %** | Margin / Sale Price x 100 |

<!-- TODO: replace with screenshot of the final calculation in Quote Builder -->
![Final calculation — Landed Cost, margin, VAT, total with VAT](/images/screenshots/placeholder.svg)

### Step 7. Generate the Quote

Click **"Generate Quote"**. The system will create a commercial quote document.

Before sending to the client, you can:

- Preview the quote
- Download a PDF for internal approval
- Add a comment for the client
- Set the quote validity period

### Step 8. Send the Quote to the Client

Click **"Send to Client"**. The deal moves to the **QUOTE_SENT** stage.

## Currency and Exchange Rates

- Supplier base prices may be in USD, EUR, or UZS
- The system automatically converts to UZS using the current Central Bank rate
- The exchange rate is locked on the date the quote is generated
- To recalculate with a new rate, click **"Update Rates"**

## Important Rules

- Use only `DecimalField` — never round manually
- Verify the exchange rate before generating a quote
- Approve the margin on large deals with management
- All calculations are saved in the deal history for audit purposes

---

**Next step:** [KYC Verification](../kyc-verification/)
