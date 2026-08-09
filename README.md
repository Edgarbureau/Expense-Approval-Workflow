# Expense Approval & Receipt Processing Automation

An n8n workflow that automates an employee expense claim from submission to approval to accounting entry — no manual data entry after the form is submitted.

## The Problem

Manually, this process looks like: employee messages a receipt → manager reviews and replies informally → someone re-types it into accounting software → someone else updates a spreadsheet separately to track it. Every handoff is a place things get delayed or lost.

## What It Does

1. Employee submits an expense (vendor, amount, category, receipt) via a **Google Form**.
![Google Form](screenshots/Google%20Form%201.png)
![Google Form](screenshots/Google%20Form%202.png)

2. The submission lands in a **Google Sheet**, which tracks every expense's status in real time.

3. The manager gets an **email** with full details and one-click Approve / Reject (with reason) links — no login required.
![Manager Email](screenshots/Manager%20Email.png)

4. One click, and the system takes over:
   - **Approved** → a Bill is created in **Xero**, Finance is notified on **Slack**, the Sheet is updated
   - **Rejected** → the employee is automatically emailed the reason, the Sheet is updated

5. Everything is tracked centrally — status, dates, and reasons — with no manual reconciliation needed.
![Approved, Pending or Declined](screenshots/Approved%2C%20Pending%20or%20Declined.png)

## The Full Workflow

![Full Workflow](screenshots/Full%20Workflow.png)

The workflow has two independent branches instead of one long-running process:
- **Branch 1** fires when a form is submitted → notifies the manager.
- **Branch 2** fires only when the manager clicks a decision link → routes to Approved or Rejected, and handles everything from there.

Keeping these separate means the workflow doesn't sit idle "waiting" for a decision — the approval can come minutes or days later with no difference in behavior.

![Pending Approval or Rejection](screenshots/Pending%20Approval%20or%20Rejection.png)

## Result in Xero

Approved expenses are recorded as Bills (Accounts Payable) automatically, with the correct vendor, amount, and account category.

![Receipt in Xero](screenshots/Reciept%20in%20Xero.png)

## Tools Used

Google Forms · Google Sheets · Gmail · Slack · Xero · n8n

## A Few Things Worth Knowing

- Webhook query parameters land in `$json.query`, not `$json` directly.
- Once data passes through an "Update Row" node, later nodes need explicit references (`$('Node Name').item.json[...]`) to reach the original form data.
- Xero needs a **Bill**, not an Invoice, for money the business owes — Invoices are for money owed *to* the business.
- Xero Contact IDs are GUIDs, found in the contact's URL in the Xero app — not simple reference numbers.

## Possible Extensions

- Dynamic Xero contact matching by vendor name
- AI-based receipt data extraction instead of manual form entry
- A separate scheduled workflow for weekly expense summaries

---
*Part of a self-directed portfolio building workflow automations, transitioning from clinical nursing into data analytics and automation.*
