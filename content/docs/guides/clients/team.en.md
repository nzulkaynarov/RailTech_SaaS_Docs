---
title: "Team Management"
linkTitle: "Team"
weight: 80
description: "How to invite employees and manage roles in your company workspace"
categories: ["Guides"]
tags: ["team", "RBAC"]
---

The Workspace allows multiple employees of your company to work on the platform. Each person can be assigned a role with specific access rights.

## Workspace Roles

| Role | Description | Rights |
|------|-------------|--------|
| **Owner** | Company account creator | Full access, team management, account deletion |
| **Procurement Manager** | Employee working with deals | Deal creation, catalog browsing, manager chat |
| **Accountant** | Finance department employee | Viewing quotes, access to documents and invoices |

{{< callout type="info" >}}
Owner is the only role that can manage team composition and company settings.
{{< /callout >}}

## Inviting Employees

### Step 1. Open Team Settings

Go to **"Company Settings" -> "Team"**.

<!-- TODO: replace with screenshot of "Company Settings -> Team" section -->
![Team settings — employee list with roles](/images/screenshots/placeholder.svg)

### Step 2. Click "Invite Employee"

Click the **"+ Invite"** button in the upper right corner.

### Step 3. Fill in the Details

| Field | Description |
|-------|-------------|
| Email | Employee's corporate email |
| Name | Full name of the invitee |
| Role | Select a role from the list |

### Step 4. Send the Invitation

Click **"Send Invitation"**. An email with a joining link will be sent to the specified address.

<!-- TODO: replace with screenshot of employee invitation form -->
![Invitation form — email, name, role selection fields](/images/screenshots/placeholder.svg)

## Accepting an Invitation

The invited employee:

1. Receives an email with the invitation
2. Follows the link
3. Creates a password (if no account exists) or logs into an existing account
4. Automatically joins the company workspace

{{< callout type="warning" >}}
The invitation link is valid for **7 days**. After expiration, the invitation must be resent.
{{< /callout >}}

## Managing Employees

### Changing a Role

1. Open **"Company Settings" -> "Team"**
2. Find the employee in the list
3. Click on the current role
4. Select a new role from the dropdown

### Removing an Employee

1. Open the team list
2. Click the **"Remove"** icon next to the employee
3. Confirm the action

After removal, the employee loses access to all company data on the platform.

{{< callout type="warning" >}}
A removed employee will not be able to access the workspace. Their created deals will remain visible to other team members.
{{< /callout >}}

## Access Control

Each employee sees only the data they have access to:

- **Procurement Manager** sees all company deals and can create new ones
- **Accountant** sees commercial quotes, invoices and financial documents but cannot create deals
- **Owner** can see and edit everything

## Tips

- Assign the **"Procurement Manager"** role to employees who directly work with requests
- Use the **"Accountant"** role for colleagues who only need access to documents
- Regularly review the participant list and remove terminated employees

---

**Previous step:** [Chat with Manager](../chat/)
