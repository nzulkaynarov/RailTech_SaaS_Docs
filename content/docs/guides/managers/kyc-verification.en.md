---
title: "KYC Document Verification"
linkTitle: "KYC Verification"
weight: 40
description: "How to verify and approve client company documents"
categories: ["Guides"]
tags: ["KYC", "CRM"]
---

KYC verification is a mandatory document review procedure for clients before granting access to prices and deals. The manager reviews uploaded documents and makes a decision.

## When Verification Is Needed

Verification is required when:

- A new client has registered and uploaded documents
- A client has updated documents after a previous rejection
- A deal is blocked at the **KYC_PENDING** stage

You will receive a notification about new verification requests.

## Verification Process

### Step 1. Open the KYC Requests Section

In the admin panel, navigate to the **"KYC Verification"** or **"Verification Requests"** section.

<!-- TODO: replace with screenshot of the KYC requests list -->
![KYC requests — list of companies pending verification with dates and statuses](/images/screenshots/placeholder.svg)

### Step 2. Select a Request

The request list is sorted by date. New requests are marked with **"Under Review"** status. Click on a request to open it.

### Step 3. Verify Company Data

Compare the form data against the uploaded documents:

| What to Check | What to Look For |
|---------------|-----------------|
| **TIN** | Does the TIN in the documents match the one provided during registration |
| **Company Name** | Full legal name must match the registration documents |
| **Legal Address** | Address from the charter or registration certificate |
| **Director** | Director's full name in the charter and power of attorney |
| **Bank Details** | Settlement account, MFO (bank code), bank name |

### Step 4. Review Documents

Open each uploaded document and verify:

- **Registration certificate:** document is readable, TIN number matches, document is current
- **Charter:** first and last pages are uploaded, stamped and signed
- **TIN certificate:** number matches the one provided
- **Power of attorney (if applicable):** currently valid, signed by the director, with company seal

<!-- TODO: replace with screenshot of uploaded KYC document viewer -->
![KYC document review — scans of registration certificate, charter, TIN certificate](/images/screenshots/placeholder.svg)

{{< callout type="warning" >}}
Pay special attention to document expiration dates. Expired documents are grounds for rejection.
{{< /callout >}}

### Step 5. Make a Decision

#### Approve Verification

If all documents are in order:

1. Click **"Approve"**
2. The company status changes to **"Verified"**
3. The client receives a notification
4. Blocked deals automatically proceed to the next stage

#### Request Revision

If documents need corrections:

1. Click **"Request Revision"**
2. **You must specify the reason** in the comment — what exactly needs to be corrected
3. The client receives a notification with your comment

Common reasons for requesting revision:

- Unreadable scan (blurry, cropped)
- Required document not uploaded
- Data mismatch (TIN, company name)
- Expired document

#### Reject Verification

In exceptional cases (suspected fraud, forged documents):

1. Click **"Reject"**
2. Provide a detailed reason
3. Inform the administrator

{{< callout type="info" >}}
If in doubt, discuss the request with an administrator or department head before rejecting.
{{< /callout >}}

## Processing Timeline

- **Recommended timeframe:** within 1 business day
- Requests older than 2 days are marked with a yellow indicator
- Requests older than 5 days are marked with a red indicator (require immediate attention)

## Verification Log

All KYC actions are recorded in the log: who reviewed, when, and what decision was made. This ensures process transparency and auditability.

---

**Next step:** [Sourcing Hub](../sourcing/)
