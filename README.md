# Shop KEEL CRM — User Guide

Welcome to **Shop KEEL CRM**, your manufacturing sales and production workspace that runs
right inside Manager.io. Everything you enter in the CRM is saved to Manager.io, so your
commercial team and your accounts always work from the same data.

This guide explains what each screen does and how to use the app day to day.

---

## 1. Opening the App

Open Shop KEEL CRM from the **Custom Button** inside Manager.io (usually shown as
*Shop KEEL CRM*). The app loads in a panel over Manager.io.

- It uses your Manager.io login and permissions — you do **not** need a separate password.
- The **Role Switcher** at the top right sets what you can do. Choose the role that matches
  your job (see §2).
- Use the **left sidebar** to move between modules. The **Read Me** and **Developer Details**
  buttons at the bottom of the sidebar open helpful information at any time.

---

## 2. Roles (what each person can do)

Pick your role from the header **Role Switcher**. Buttons you are not allowed to use are
hidden or greyed out automatically.

| Role                 | You can                                                                 |
| -------------------- | ----------------------------------------------------------------------- |
| Admin / GM           | Everything                                                              |
| Sales Manager        | Approve quotes, view all pipelines, convert orders to Sales Orders       |
| Sales Rep            | Manage your leads and opportunities, create draft quotes (cannot approve)|
| Cost Engineer        | Manage products, costs and margins                                      |
| Production Manager   | Move jobs through production stages and sign off quality checks          |
| Financial Controller | Confirm payments and release production                                 |

---

## 3. Dashboard

Your starting screen. It shows at a glance:

- **Open Leads**, **Pipeline Value**, **Pending Quotes**, **Orders in Production**.
- An **Urgent Action Queue** listing items that need attention — for example quotes waiting
  for approval, or jobs waiting to enter production.

Click any item to jump straight to it.

---

## 4. Leads

Use **Leads** to capture and qualify enquiries before they become customers.

- **Add a lead:** click **+ New Lead**, enter the name, an optional code, a score (0–100) and
  notes, then Save.
- **Qualify a lead:** open it and update the lead status as you progress.
- **Convert a lead:** click **Convert**. This creates the customer account and a new
  opportunity in one step. If anything goes wrong, the app tells you and nothing is left half
  done.
- Use the search box and the **All / Leads / Customers** filters to find records quickly.

---

## 5. Customers

The **Customers** list shows all customer accounts with their balance and status.

- Click **View 360** on any customer to open a full profile with tabs for **Profile**,
  **Opportunities**, **Quotes** and **Orders** — one place to see everything about that
  account.

---

## 6. Opportunities (Pipeline Board)

**Opportunities** is a Kanban board with one column per pipeline stage
(Discovery, Meeting, Proposal, Client Feedback, Negotiation, Won, Lost).

- **Add an opportunity:** click **+ New Opportunity**, choose the customer, add a description,
  and Save.
- **Move it forward:** open a card and pick the next stage from the dropdown, or change it
  directly on the card. The board updates immediately.
- Each column shows the number of opportunities and their total value.

---

## 7. Quotes

Create professional quotations and manage revisions and approvals.

- **Create a quote:** click **+ New Quote**, choose the customer, add line items (search for a
  product by code or name, then set quantity, price and any discount), and Save. Totals,
  discount and margin update live.
- **New revision:** when a quote changes, open it and click **New Revision**. The app copies it
  as **Rev 2**, **Rev 3**, etc. and marks the older version as superseded, so you always have a
  clean history.
- **Approve a quote:** Sales Managers and Admin/GM see an **Approve** button on draft quotes.
- **Convert to order:** once approved, click **Convert to Order**, enter the customer's PO
  number, and the app creates a Sales Order and assigns a **Master Reference** that follows the
  job all the way through.

---

## 8. Sales Orders

Shows confirmed orders after they leave the quotes stage.

- Each order lists the date, PO reference, customer, source quote, order amount and its
  **Master Reference**.
- Use **View** to open an order and see its line items and status.

---

## 9. Production & MRP

Track every confirmed order through the factory.

**Stages:** Carpentry (Preparation and Cutting → Shaping → Joinery → Sanding) → Veneering →
Outsource Work (Metal, Glass, Marble, Hardware) → Polish → Upholstery → Final QC → Packing →
Customer Feedback.

- Each department appears as a board column; each order is a card showing its current step.
- **Before production starts**, a **BOM & Design Approved** gate must be ticked. If a product
  is not ready or has no price, it is placed in the **Product Design Team** queue instead.
- **Advance a job:** open its card, complete the current step, then move to the next. To enter a
  new department you must tick its **QC** approval (for example, *QC Approved for Veneering*).
- Steps cannot be skipped — the app blocks them and tells you what is missing.

---

## 10. Product Catalog

Browse the products and materials used in your quotes and orders.

- Search by product code or name.
- See the account, unit and quantity on hand for each item.

---

## 11. Sync Center

If an action ever fails to save (for example a connection timeout), it appears here with a
plain-language reason.

- Where it is safe to try again, click **Retry**.
- Items marked **manual review** need a person to check them before retrying.

A clean Sync Center ("No failed operations") means everything saved successfully.

---

## 12. Audit Trail (Admin / GM only)

A chronological, read-only log of key actions — quotes approved, orders converted, production
stages advanced, QC signed off — with who did it and when. You can export the list if needed.

---

## 13. Everyday Tips

- **Nothing is lost:** the CRM writes straight to Manager.io; there is no separate database to
  back up.
- **Watch the buttons:** while something is saving, the button shows *Processing…* and disables
  itself until it finishes.
- **Empty screens are normal** when you first start — they fill up as you add leads, quotes and
  orders.
- **Wrong role?** Switch it in the header; the screens update instantly to show the right
  permissions.

---

*Shop KEEL CRM — from first enquiry to finished goods, all in one place.*
