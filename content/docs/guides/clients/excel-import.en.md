---
title: "Excel Specification Import"
linkTitle: "Excel Import"
weight: 50
description: "How to upload a specification from Excel file for automatic request creation"
categories: ["Guides"]
tags: ["Excel", "Smart Match"]
---

If you have a ready specification in Excel format, you can upload it to the platform. The system will automatically recognize items and match them with catalog products.

## Supported Formats

- **XLSX** -- Microsoft Excel 2007 and later (recommended)
- **XLS** -- Microsoft Excel 97--2003
- Maximum file size: **15 MB**
- Maximum number of rows: **500**

## File Requirements

Your Excel file must contain at least the following columns:

| Column | Description | Required |
|--------|-------------|:--------:|
| Name | Product / part name | Yes |
| Quantity | Required quantity | Yes |
| UOM | Unit of measurement (pcs, kg, m) | Recommended |
| Article | Catalog / part number | Recommended |
| Comment | Notes for the item | No |

{{< callout type="info" >}}
The system flexibly recognizes column headers. For example, "Qty", "Quantity", "Amount" will all be recognized as the quantity column.
{{< /callout >}}

## Step-by-Step Guide

### Step 1. Create a new request or open an existing one

Go to the **"Deals"** section and create a new request or open a draft.

### Step 2. Click "Import from Excel"

On the deal page, click the **"Import from Excel"** button in the items section.

<!-- TODO: replace with a screenshot of the "Import from Excel" button in a deal -->
![Deal page -- "Import from Excel" button in the items section](/images/screenshots/placeholder.svg)

### Step 3. Upload the file

Drag the file into the upload area or click **"Choose file"**. Wait for processing to complete.

### Step 4. Review the recognized data

After uploading, the system will display a preview:

<!-- TODO: replace with a screenshot of the Excel import preview -->
![Import preview -- table with recognized items and match percentage](/images/screenshots/placeholder.svg)

For each row, the following will be shown:

- **Recognized name** -- what the system read from the file
- **Matched product** -- the closest catalog product (if found)
- **Match percentage** -- how accurately the product was matched (as a percentage)
- **Quantity and unit of measurement**

### Step 5. Confirm or correct

For each item, you can:

- **Accept** the automatic match if it is correct
- **Change** -- manually select a different product from the catalog
- **Leave as is** -- if no product was found, the manager will match it manually

### Step 6. Save the result

Click **"Confirm import"**. The items will be added to your request.

## How Smart Match Works

The system uses **Smart Match** technology to find correspondences:

1. The product name from your file is compared against the catalog using fuzzy matching
2. Article numbers, synonyms and alternative names are taken into account
3. Results are ranked by similarity score

Items that could not be matched automatically are forwarded to the manager for manual processing.

{{< callout type="warning" >}}
The more accurately you specify article numbers and product names in your specification, the better the automatic matching will work.
{{< /callout >}}

## Tips for File Preparation

- Use **one sheet** in the file -- the system reads the first sheet only
- Place **headers in the first row**
- Do not merge cells
- Avoid empty rows between data
- Include article numbers -- this significantly improves matching accuracy

## Download Template

You can download a ready specification template from the import page. Fill it in and upload it back.

---

**Next step:** [Working with Commercial Quotes](../quotes/)
