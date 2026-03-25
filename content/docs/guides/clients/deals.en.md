---
title: "Creating a Request (Deal)"
linkTitle: "Creating a Request"
weight: 40
description: "How to submit a procurement request through the platform"
categories: ["Guides"]
tags: ["deals", "requests"]
---

A request (deal) is your procurement inquiry for equipment. After you create a request, the RailTech manager will find the best offers and prepare a commercial quote.

{{< callout type="info" >}}
To create requests, you must complete [KYC verification](../kyc/).
{{< /callout >}}

## Methods for Creating a Request

There are two methods:

1. **Manual** -- add items from the catalog one by one
2. **Excel import** -- upload a ready specification (see [Excel Import](../excel-import/))

## Creating a Request Manually

### Step 1. Go to "My Deals"

In your account, open the **"Deals"** section in the sidebar menu.

<!-- TODO: replace with a screenshot of the "My Deals" section -->
![Deals section -- list of requests with current status](/images/screenshots/placeholder.svg)

### Step 2. Click "Create Request"

Click the **"+ New Request"** button in the top right corner.

### Step 3. Fill in basic information

| Field | Description | Required |
|-------|-------------|:--------:|
| Request title | Brief description of your need | Yes |
| Priority | Standard / Urgent | No |
| Comment | Additional notes or requirements | No |
| Desired delivery date | When you need the equipment | No |

### Step 4. Add items

Click **"Add item"** and choose one of the options:

- **From catalog** -- find a product via search and specify the quantity
- **Custom item** -- describe the product in text if it is not found in the catalog

<!-- TODO: replace with a screenshot of adding items to a request -->
![Adding items -- catalog search, quantity specification](/images/screenshots/placeholder.svg)

For each item, specify:

- Product name or select from the catalog
- Quantity
- Unit of measurement
- Comment (optional) -- for example, "equivalent acceptable"

### Step 5. Submit the request

Click **"Submit request"**. The deal will move to processing.

## Deal Stages

After submission, the request goes through several stages:

| Stage | What happens |
|-------|--------------|
| **Draft** | You are composing the request; it has not been submitted yet |
| **KYC Pending** | If KYC has not been completed, the deal awaits verification |
| **Sourcing** | The manager is finding suppliers and prices |
| **Calculating** | The manager is calculating delivery costs and final pricing |
| **Quote Sent** | A commercial quote has been sent to you |
| **Contract Signed** | You have approved the quote and signed the contract |
| **Closed Won** | The deal has been completed successfully |

<!-- TODO: replace with a screenshot of the deal stages funnel -->
![Deal stages -- visual funnel from "Draft" to "Closed Won"](/images/screenshots/placeholder.svg)

## Tracking Status

In the **"My Deals"** section, you can see all your requests with their current status. Click on a request to view its details.

On the deal page, you have access to:

- **Item list** -- what you requested
- **Current stage** -- where the deal is in processing
- **Commercial quote** -- once the manager has prepared it
- **Chat with manager** -- for discussing details
- **Change history** -- all actions related to the deal

## Editing a Request

You can edit a request while it is in the **"Draft"** stage:

- Add and remove items
- Change quantities
- Update comments

{{< callout type="warning" >}}
After submitting a request, changes can only be made through your manager. Message them in the [deal chat](../chat/).
{{< /callout >}}

## Tips

- Provide as much detail as possible: article numbers, drawings, acceptable equivalents
- For large specifications (10+ items), use [Excel import](../excel-import/)
- If you cannot find a product in the catalog, describe it in text and the manager will source it

---

**Next step:** [Excel Specification Import](../excel-import/)
