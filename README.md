# Shop KEEL CRM — Manager.io Manufacturing Gateway

A single-file, vanilla HTML/JavaScript **Manufacturing CRM & MRP Gateway** that runs as an
iframe inside Manager.io. It adds a full commercial workflow (Leads → Opportunities → Quotes →
Sales Orders) and an 18-stage production routing board on top of Manager.io, using Manager.io
itself as the only backend and system of record.

- **Frontend:** Vanilla HTML + JavaScript + Tailwind CSS (CDN). No framework, no build step.
- **Backend:** Manager.io API (`/api4/...`) via the `postMessage` iframe bridge. No separate
  server, no database, no `localStorage` for business data.
- **Deployment:** Static hosting (GitHub Pages or Vercel) + a Manager.io Custom Button.

---

## 1. Architecture

```
Manager.io (system of record)
      │  iframe + postMessage (api-request / api-request-response)
      ▼
index.html  ──  Shop KEEL CRM (vanilla JS SPA)
      │
      ├─ Commercial : Leads, Customers, Opportunities (Kanban), Quotes (multi-revision)
      ├─ ERP handoff: Sales Orders + Master Reference
      └─ Production : 18-stage routing, QC gates, JSON state
```

- All reads/writes go through `postMessageWithResponse({ type:'api-request', method, path, body })`.
- List endpoints are paginated with `next_page_token`; the app loops until exhausted.
- **Security:** no API keys are ever stored in the app. The iframe inherits the permissions of
  the currently logged-in Manager.io user.

### Data model (Manager.io native records)

| CRM concept   | Stored as                                  |
| ------------- | ------------------------------------------ |
| Lead          | Customer record + `Lead Status` field      |
| Customer      | Customer record (`Lead Status` = Customer) |
| Opportunity   | Sales Quote record (`CRM State` = Opportunity) |
| Quote         | Sales Quote record (`CRM State` = quote states) |
| Sales Order   | Sales Order record + `Master Reference`    |
| Production     | Sales Order + `Production State` JSON field |

**Master Reference** is the digital thread. It is a unique string (e.g. `M-20261018-143022`)
assigned at order confirmation and stored on the Sales Order, allowing the *Project 360°* view
to trace Lead → Opportunity → Quote → Order → BOM → Production.

---

## 2. Required Manager.io Custom Fields

> **The CRM will not function correctly until these fields exist.**
> Create them in **Manager.io → Settings → Custom Fields**, using the exact **Name** and
> **Type** below. The app auto-detects fields by name **and placement** (case-insensitive), so
> spelling and placement matter.

### On **Customer**

| Placement | Name          | Type   | Suggested values / notes                |
| --------- | ------------- | ------ | --------------------------------------- |
| Customer  | `Lead Status` | Text   | New, Qualified, Converted               |
| Customer  | `Lead Score`  | Number | 0–100                                   |
| Customer  | `CRM Notes`   | Text   | Free text                               |

### On **Sales Quote**

| Placement   | Name              | Type   | Suggested values / notes                                                                 |
| ----------- | ----------------- | ------ | ---------------------------------------------------------------------------------------- |
| Sales Quote | `CRM State`       | Text   | Opportunity, Draft Quote, Approved, Converted, Archived, Superseded                      |
| Sales Quote | `Pipeline Stage`  | Text   | Discovery, Meeting, Proposal, Client Feedback, Negotiation, Won, Lost                    |
| Sales Quote | `CRM Probability` | Number | 0–100                                                                                    |
| Sales Quote | `CRM`             | Text   | Fine workflow label (e.g. Lead, Meeting, Proposal, Quotation, Payment Received, …)       |

### On **Sales Order**

| Placement   | Name                | Type   | Suggested values / notes                                  |
| ----------- | ------------------- | ------ | --------------------------------------------------------- |
| Sales Order | `Master Reference`  | Text   | Assigned at order confirmation (e.g. `M-20261018-143022`) |
| Sales Order | `Production State`  | Text   | **JSON payload** written by the app (see §5)              |

**Dropdown vs Text:** Manager's Dropdown custom fields are exposed by the API as text fields;
either type works. If you use a Dropdown, ensure every value the CRM writes is in the allowed
list (states/stages listed above).

---

## 3. Roles & Permissions

Switch role from the header **Role Switcher**. Permissions are enforced in the UI *and* in logic.

| Role                 | Can do                                                                              |
| -------------------- | ----------------------------------------------------------------------------------- |
| Admin / GM           | Everything                                                                          |
| Sales Manager        | Approve quotes, view all pipelines, convert orders                                  |
| Sales Rep            | Manage assigned leads/opportunities, create Draft quotes (cannot approve)           |
| Cost Engineer        | Edit BOM, product costs and target margins                                          |
| Production Manager   | Advance production stages, sign QC (cannot approve commercial pricing)              |
| Financial Controller | Confirm payments, credit limits, authorise production release                       |

Key rules verified: a **Sales Rep cannot approve quotes or advance production**; a
**Production Manager cannot approve commercial pricing**; the *Confirm Payment* action is only
available to the Financial Controller / Admin.

---

## 4. Modules

- **Executive Dashboard** — KPIs, pipeline visualiser, urgent action queue.
- **Leads** — capture, qualify, 1-click Convert to Customer + Opportunity.
- **Customers** — Account 360 with Opportunity/Quote/Order tabs.
- **Opportunities** — Kanban pipeline board (select-to-move stage).
- **Quotes** — multi-revision engine (Rev N), approval workflow, convert to order.
- **Sales Orders** — PO verification, Master Reference, dispatch to production.
- **Production & MRP** — board across Carpentry, Veneering, Outsource Work, Polish,
  Upholstery, Final QC, Packing, Customer Feedback, with QC gates.
- **Project 360** — full traceability by Master Reference.
- **Product Catalog** — finished goods / raw materials from Manager inventory.
- **Sync Center** — failed API operations with reason + safe retry.
- **Audit Trail** — chronological critical-action log (Admin/GM only).

---

## 5. Production State JSON

Because Manager.io has no native 18-step routing, production state is stored as a JSON string in
the `Production State` custom field on the Sales Order:

```json
{
  "version": 1,
  "masterRef": "M-20261018-143022",
  "orderKey": "<sales-order-uuid>",
  "currentStage": "Carpentry",
  "currentSubStage": "Sanding",
  "bomDesignApproved": false,
  "designQueue": false,
  "qc": {
    "Carpentry": { "approved": true, "by": "Production Manager", "at": "2026-10-18T09:00:00Z" }
  },
  "history": [
    { "stage": "Carpentry/Preparation and Cutting", "at": "2026-10-18T08:00:00Z", "by": "Production Manager" }
  ],
  "updatedAt": "2026-10-18T09:00:00Z"
}
```

Routing sequence: **Carpentry** (Preparation and Cutting → Shaping → Joinery → Sanding) →
**Veneering** (Cut/Paste → Sanding) → **Outsource Work** (Metal, Glass, Marble, Hardware) →
**Polish** (Sanding → Stain/Sealer → Color/Top Coat → Fine Touch) → **Upholstery** (Webbing →
Foam → Fabric Cut → Stitching → Fit) → **Final QC** → **Packing** → **Customer Feedback**.

A pre-production **BOM & Design Approved** gate must be cleared before Carpentry; items that are
unconfigured or lack pricing are routed to the **Product Design Team** queue. Moving into a
department requires the previous department's **QC sign-off**.

---

## 6. Local Development

```powershell
# from the project folder
python -m http.server 8081
```

Then in Manager.io create a **Custom Button** with URL `http://localhost:8081/index.html`
(see §7). The app only talks to Manager.io when opened from inside the iframe; standalone it
shows the UI with empty states.

---

## 7. Deploying

### Option A — GitHub Pages

1. Push this repo to GitHub (branch `main`, root contains `index.html`).
2. Repo → **Settings → Pages** → Source: *Deploy from a branch* → `main` / `/ (root)` → Save.
3. Wait for the build, then your URL is:
   `https://<your-username>.github.io/<your-repo>/index.html`

### Option B — Vercel

1. Go to <https://vercel.com> → **Add New → Project** → import this Git repository.
2. Framework preset: **Other**. Build command: *(none)*. Output directory: `.` (root).
3. Deploy. Vercel gives an `https://<project>.vercel.app/` URL.

### Connecting it to Manager.io (both options)

1. Manager.io → **Settings → Custom Buttons → New Custom Button**.
2. **Name:** `Shop KEEL CRM`
3. **URL:** your hosted `index.html` URL (`https://.../index.html`).
4. Save and open it from inside Manager.io. The CRM loads in an iframe and inherits your
   logged-in permissions.

> **HTTPS note:** GitHub Pages and Vercel serve over HTTPS, which avoids browser secure-context
> restrictions and allows team-wide access.

---

## 8. Handover Notes

- **No secrets** are stored in `index.html` or this repo.
- The app ships with a **fallback field map** (KEEL business UUIDs) and a **dynamic field
  lookup**; the dynamic lookup is authoritative. On a different Manager.io business the UUIDs
  differ — the app resolves them by field **Name + Placement**, so the field table in §2 is what
  matters.
- **Data integrity:** multi-step operations are atomic where possible — e.g. converting a quote
  creates the Sales Order first, then updates the quote; if the second call fails the app alerts
  the user and logs it to the Sync Center rather than reporting success.
- **Audit:** in-session actions are held in memory (reset on reload). Durable history is the
  `history[]` array inside the Production State JSON and the Manager.io records themselves.
