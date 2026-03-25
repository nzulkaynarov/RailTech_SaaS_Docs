---
title: "Working with the Sourcing Hub"
linkTitle: "Sourcing Hub"
weight: 50
description: "How to process unrecognized items and manage manual matching"
categories: ["Guides"]
tags: ["Sourcing", "Smart Match", "CRM"]
---

Sourcing Hub is the processing center for items that Smart Match could not automatically match with the catalog. Here, managers manually find products, create new product cards, or reject irrelevant requests.

## How Items Enter the Sourcing Hub

When a client uploads an Excel specification, Smart Match attempts to match each row with the catalog. Items with a similarity score **below 50%** are automatically routed to the Sourcing Hub as **UnrecognizedRow** entries.

Common reasons:

- Product is not in the catalog
- Client uses non-standard naming
- Typos or abbreviations in the specification
- Product is from a different industry (not typical for the catalog)

## Sourcing Hub Interface

### Unrecognized Items List

Open the **"Sourcing Hub"** section in the CRM sidebar menu.

<!-- TODO: replace with screenshot of the Sourcing Hub list -->
![Sourcing Hub — list of unrecognized items with original text and status](/images/screenshots/placeholder.svg)

Each item displays the following:

| Column | Description |
|--------|-------------|
| **Original Text** | What the client wrote in the Excel file |
| **Deal** | Which deal the item belongs to |
| **Client** | The ordering company |
| **Date** | When the specification was uploaded |
| **Status** | New / In Progress / Matched / Rejected |
| **Closest Matches** | Best matching options from the catalog (if any) |

### Filters

Use filters for efficient navigation:

- **By status** — show only new items
- **By deal** — items for a specific deal
- **By date** — within a date range
- **By client** — items from a specific client

## Processing Unrecognized Items

### Option 1: Match with an Existing Product

If the product exists in the catalog but was not recognized:

1. Open the unrecognized item
2. In the **"Search Catalog"** field, enter the product name or article number
3. Select the correct product from the results
4. Click **"Match"**

<!-- TODO: replace with screenshot of manual matching in the Sourcing Hub -->
![Manual matching — catalog search and product linking](/images/screenshots/placeholder.svg)

{{< callout type="info" >}}
When matching, the system remembers the "original text to catalog product" link. In the future, similar items will be recognized automatically.
{{< /callout >}}

### Option 2: Create a New Product Card

If the product is not in the catalog:

1. Click **"Create Product"**
2. Fill in the new product card:
   - Name
   - Article number
   - Category (select from the hierarchy)
   - Domain (Railway Equipment / General Goods / Services)
   - Equipment Group (required for railway equipment)
   - Technical specifications
3. Save the product card
4. Match the unrecognized item with the new product

{{< callout type="warning" >}}
When creating a product in the "Railway Equipment" domain, you must specify the Equipment Group (EquipmentGroup). The product cannot be saved without it.
{{< /callout >}}

### Option 3: Reject the Item

If the item is irrelevant (client error, duplicate, not a product):

1. Click **"Reject"**
2. Specify the rejection reason
3. The client and the deal manager will see the comment

## Bulk Processing

To speed up work with large specifications:

1. Select multiple items using checkboxes
2. Click **"Bulk Action"**
3. Choose an action:
   - **Match with one product** — if several rows refer to the same product
   - **Reject selected** — for irrelevant items

## Domain Rules

When matching or creating products, follow the domain rules:

| Domain | Equipment Group | JSONB Attributes | Strict Hierarchy |
|--------|:--------------:|:----------------:|:----------------:|
| Railway Equipment | Required | Partial | Yes |
| General Goods | No | Yes | No |
| Services | No | No | No |

## Reports

The Sourcing Hub reports section includes:

- **Top unrecognized items** — which products are most frequently unrecognized
- **Processing statistics** — how many items were processed over a given period
- **Smart Match efficiency** — percentage of automatic matches

Use this data to expand the catalog: if the same item is frequently unrecognized, add it to the catalog.

## Tips

- Process the Sourcing Hub daily — do not let items accumulate
- When creating products, use standard names from GOST or TU specifications
- Add synonyms to the product card after matching
- Consult the procurement department when unsure about classification

---

**Previous step:** [KYC Verification](../kyc-verification/)
