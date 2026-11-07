# Thapsus suite integration contract (plan)

> Suite document, kept identical in `PROJECT_MULLAH`, `PROJECT_BOSSMAN` and `PROJECT_KASONGO` until the
> shared `thapsus-platform` repository exists (HUMAN_TODO H-S1). Change all three copies in the same change.
> Status: plan for owner approval, 2026-11-07.

The owner's requirement: a client that buys more than one Thapsus product gets products that work with each
other, with no exports and no double entry. That includes Thapsus-HR (`PROJECT_KAREN`) and Thapsus Care
(`ROCHE`), which are already being built. Each product must still work fully on its own.

This contract generalises the link Thapsus Care already built to Thapsus-HR
(`ROCHE/docs/decisions/0126-thapsus-hr-link.md` and `ROCHE/docs/specs/integrations.md` §11). That link set
the pattern: one owner for each kind of data, identifiers on the wire, fetch over the public API, same region
only, off by default.

## 1. Principles

1. **Whole on its own.** Every product keeps its own simple version of the data it needs (Finance has a
   product list, CRM has price books, ERP has customers). A link replaces that local copy with a read-only
   copy of the owner's record; it never removes a feature.
2. **One owner for each kind of data, per tenant.** The defaults are in §4. A tenant may change an owner when it
   connects, and later only with step-up authentication and a full reconciliation. The other product shows the
   field read-only, labelled "Managed in Thapsus Finance" (or the owner's name) with the time of the last sync.
   Reconciliation restores the owner's version and records the drift.
3. **Field-level ownership on shared records.** One record can have owners per field group (for example a
   customer: CRM owns the name, contacts and segmentation; Finance owns credit limit, payment terms, tax
   status and balance; ERP owns delivery preferences).
4. **Same home region only.** A link exists only between workspaces in the same cell (UK with UK, EU with EU,
   US with US). Checked when connecting and on every sync; otherwise nothing is sent. Thapsus Care is UK-only,
   so it links only to UK workspaces.
5. **Identifiers on the bus, data over the API.** Events carry identifiers and the names of changed fields,
   never field values. The receiver fetches the record through the owner's public, versioned API with a
   narrowly scoped credential, as Thapsus-HR's webhooks do today.
6. **Public APIs only.** No product reads another product's database, and there are no joins across
   products. A link uses only what any customer integration could use, plus documented partner scopes.
7. **Additive only.** A product adds partner features without changing behaviour for tenants that have no link.
   Proof: an additions-only API check and byte-identical outputs for unlinked tenants (as Care's §11.9 asks of
   Thapsus-HR).
8. **Allow-listed fields.** Each flow sends only its listed fields; a test fails on any other field.
   Sensitive categories (health, sickness reasons, diversity, immigration, care records) never cross.
9. **Idempotent and replayable.** The source event identifier is the idempotency key; each side stores the
   other's identifier (an `external_ref` row), so retries older than the partner's idempotency window look the
   record up instead of creating a duplicate.
10. **Stale data is said in words.** When the last good sync is older than the tenant's limit, screens that
    depend on it say so, with the time, an icon and words.
11. **Off by default.** Each link is a feature flag per tenant (`suite.link.<other-product>`), switched on by
    an admin with step-up, and audited on both sides.
12. **Units, not money, where the owner applies rates** (Care's rule 11): a source that does not own the price
    sends quantities and codes, and the owner prices them. Where money must cross it goes as integer minor units
    with an ISO 4217 code.

## 2. Suite organisation, identity and linking

Details are in `PLATFORM.md` §2. In short:

- A **suite organisation** in the global directory (europe-west2, no personal data) holds the organisation
  id, its home region and the products and workspaces it has. Every product workspace records its suite
  organisation id.
- **Thapsus ID**, a regional identity service in each cell, gives people one sign-in across the products their
  organisation uses, federated to the customer's own identity provider when it has one. Authorisation stays
  in each product: each product keeps its own roles and permissions.
- **Connecting two products** creates a link record in both workspaces: the partner's workspace id and region,
  the owner choices, a narrowly scoped OAuth 2.0 client for each direction (secrets in Secret Manager), the
  staleness limit and the status. An admin of each product must approve with step-up; both sides audit it.

## 3. Events

- **Transport inside a cell**: a regional Pub/Sub topic per product (`suite-events-<product>`) with a message
  storage policy limited to the cell's region, `enforceInTransit` on, and regional endpoints only. Subscribers in other products of the
  same cell get push delivery to an authenticated endpoint. Nothing crosses cells.
- **Format**: CloudEvents 1.0 (JSON). `type` is `com.thapsus.<product>.<entity>.<verb>.v<major>`, for example
  `com.thapsus.finance.invoice.paid.v1`. `subject` is the record id; `data` holds the tenant id, the suite
  organisation id, record ids and changed field names only.
- **Schema**: each product publishes an AsyncAPI 3 document next to its OpenAPI document. Contract tests in
  every consumer pin a partner's published documents at a commit.
- **Source of events**: every product already writes domain events to a transactional outbox in the same
  transaction as the change. The outbox dispatcher publishes them to Pub/Sub and to customer webhooks
  (the same events, so a customer integration and a suite link see the same thing).
- **Delivery**: at least once; consumers are idempotent by event id; out-of-order delivery is handled by
  fetching the current record; a nightly reconciliation per link catches anything missed.

## 4. Who owns what (defaults when both products are present)

When the owner product is not present, the other product owns its own version of the data, as it does when
sold on its own.

| Kind of data | Default owner | What the others do |
| --- | --- | --- |
| Suite organisation, home region, products held | Global directory | Read |
| People's sign-in, MFA, sessions | Thapsus ID | Products trust it; roles stay in each product |
| Legal entities: name, registration number, country, base currency, fiscal calendar, tax registrations | Finance | HR maps its employing entities to them and keeps employer-only fields (PAYE reference, employer settings); ERP and CRM read |
| Chart of accounts, tax codes, currencies and exchange rates, accounting dimensions (cost centre, department as a dimension, project as a dimension, custom dimensions) | Finance | Others read and tag their documents with them |
| Organisation structure: departments, teams, reporting lines, work locations | HR | Finance mirrors departments as a dimension; CRM uses teams for sharing and territories; ERP reads |
| Employees: name, work contact, job, department, cost centre, manager, start and leaving dates, employee number | HR | Finance: payees for expenses and payroll, approvers; CRM: users and teams; ERP: workers, labour costing; Care: as in Care's ADR-0126 |
| Leavers | HR | Every product ends access at the end of the leaving date (Thapsus ID ends sessions in all products) |
| Employee bank details (for expense reimbursement and, later, payroll) | HR, in its encrypted vault | Finance reads them through a separate, narrow partner scope, audited on both sides; without HR, Finance holds them in its own vault |
| Pay rates, pay periods, pay inputs (hours, absences, starters, leavers) | HR (care shifts and pay units from Care, as ADR-0126 and ADR-0131) | Finance's UK payroll (later phase) reads pay inputs and returns results |
| Payroll results: gross-to-net, payslips, RTI submissions, payroll journal | Finance (UK payroll, later phase), or the customer's external payroll | HR publishes payslips to employees; Finance posts the payroll journal |
| Customers (accounts) and their contacts | CRM | Finance owns credit limit, credit hold, payment terms, tax status and balance on the same record; ERP owns ship-to addresses, delivery and packing preferences |
| Leads, opportunities, activities, campaigns, marketing consent and preferences | CRM | Finance and ERP read consent only if they send marketing (they do not by default) |
| Quotes | CRM | Accepted quote becomes an ERP sales order, or a Finance invoice when there is no ERP |
| Suppliers | Finance: identity, tax ids, bank details (with verified change control), payment terms | ERP owns procurement attributes (approved status, lead times, catalogues, scorecards) |
| Items and services, units of measure, variants, GTINs | ERP | CRM reads the catalogue; Finance reads items for invoice lines |
| Price lists, discounts, promotions | ERP | CRM reads for quotes; with no ERP, CRM owns price books |
| Stock availability (available to promise) | ERP | CRM reads |
| Sales orders, shipments, returns | ERP | CRM reads status; Finance receives billing events |
| Purchase requisitions, purchase orders, goods receipts | ERP | Finance receives them for three-way match and the received-not-invoiced accrual |
| Invoices, credit notes, supplier bills, payments, journals, the general ledger | Finance, always: one ledger per legal entity | CRM and ERP read invoice and payment status; Care sends its funder and self-funder invoices as source documents |
| Projects, tasks, budgets | ERP | Finance uses the project as a dimension; CRM links deals to projects |
| Project time | ERP (or HR when the tenant chooses HR timesheets as the source) | Finance receives labour cost postings |
| Fixed assets (financial record, depreciation) | Finance | ERP maintenance assets and HR's equipment register link by reference |
| Expense claims and mileage | Finance | HR supplies employees and approval chains |
| Care funding, care billing, residents' money, care records | Care | Finance receives invoices, credit notes and receipts as postings; residents' money stays in Care with control-account totals posted to Finance; nothing about a person's care ever leaves Care |
| Commissioner contracts (councils, NHS) | Care | CRM may hold the commissioner as an account for relationship management, with no care data |

## 5. Flows between products

Each flow names its trigger, direction, fields and fallback. Detailed field allow-lists live in each
product's integration spec when the flow is built.

| Pair | Flow | Direction | Trigger |
| --- | --- | --- | --- |
| HR ↔ Finance | Employees as payees and approvers; cost centre assignment | HR → Finance | `employee.*` events, nightly reconciliation |
| | Payroll journal (from any payroll) | Payroll → Finance | Pay run closed |
| | Pay inputs to Thapsus payroll (later phase) | HR → Finance | Pay period closed (HR's existing period snapshot) |
| | Payslips and year-end documents | Finance → HR | Pay run finalised; HR publishes them as today |
| | Expense approvers from the reporting line | HR → Finance | On change |
| HR ↔ CRM | Users, teams, managers; leavers lose access | HR → CRM | `employee.*` events |
| | Commission results as a pay input | CRM → HR | Commission period approved (later phase) |
| HR ↔ ERP | Workers, skills and certifications (for shop-floor and field-service eligibility), labour rates | HR → ERP | Events and nightly |
| | Clock events and job time | ERP → HR or HR → ERP | Owner choice per tenant |
| Finance ↔ CRM | Customer financial fields, invoice and payment status, overdue flags | Finance → CRM | `invoice.*`, `payment.*`, `customer.credit_changed` |
| | Accepted quote → invoice (no ERP) | CRM → Finance | `quote.accepted` |
| | Subscriptions and renewals → recurring invoices | CRM → Finance | Contract activated or changed |
| Finance ↔ ERP | Accounting events → ledger postings (§6) | ERP → Finance | Every stock, purchase, sales, production and project transaction |
| | Supplier and customer financial master | Finance → ERP | Events |
| | Credit check on order entry | ERP → Finance (read) | Order entry |
| CRM ↔ ERP | Catalogue, price lists, availability | ERP → CRM | Events plus on-demand read |
| | Quote → sales order | CRM → ERP | `quote.accepted` |
| | Order, shipment and return status | ERP → CRM | Events |
| Care ↔ Finance | Funder and self-funder invoices, credit notes, receipts, remittances | Care → Finance | Invoice issued, payment recorded |
| | Payment status back | Finance → Care | `payment.*` |
| | Residents' money control totals | Care → Finance | Monthly reconciliation signed |
| Care ↔ ERP | Purchase requisitions for consumables and catering; stock at locations; facilities maintenance work orders | Care ↔ ERP | Optional link, later phase |
| Care ↔ CRM | Commissioner and referrer relationships (no care data) | Care → CRM | Optional link, later phase |
| Care ↔ HR | As built in Care's ADR-0126 and ADR-0131 | Both | Existing |

## 6. The posting contract (any product → Finance)

Finance holds the only general ledger. Other products never choose ledger accounts. They send **accounting
events** and Finance's configurable **posting profiles** turn them into journal entries. This works the
way posting groups work in Dynamics 365 Business Central, and it lets a customer change account mapping in
Studio without code.

- `POST /v1/ledger/accounting-events` with an `Idempotency-Key`. Body: source product, source document type
  and id, legal entity, event type (for example `inventory.receipt`, `inventory.issue.cogs`,
  `purchase.receipt.grni`, `sales.shipment`, `production.wip.issue`, `project.cost.labour`,
  `care.invoice.issued`), accounting date, currency and minor-unit amounts per line, quantities, item and
  party ids, dimensions, tax details and a deep link back to the source document.
- Finance validates the event against its schema, the period (open, or rejected with `periodClosed`), the
  entity and the dimensions. It applies the posting profile, posts a balanced, immutable journal and answers
  with the journal id. Posted entries are never edited: corrections are reversing events.
- **Drill-down both ways.** Every journal line links to its source document in the source product, and the
  source document shows its journal.
- **Sub-ledger reconciliation.** A nightly job compares each source's totals (for example the inventory
  valuation) with the matching control accounts and raises a task on any difference.
- **Before Finance is ready** (the owner chose to build all three products in parallel), ERP builds against a
  contract-tested mock of this API, generated from the Finance OpenAPI and AsyncAPI documents, as Care does
  with Thapsus-HR. The mock is re-pinned whenever Finance publishes a new version.
- **Without Thapsus Finance**, the same accounting events are mapped to journals for Xero, QuickBooks
  Online, Sage and Business Central through a connector, or exported as a journal file. This is the route for
  a customer who buys ERP alone.

## 6a. The payroll contract (HR → Finance, later phase)

The owner chose a UK payroll module in Finance in a later phase. HR keeps owning pay inputs (its period
snapshot, which is append-only and hashed). A new HR payroll connector, "Thapsus Finance payroll", sends the
closed period's snapshot to Finance, which calculates, files Real Time Information returns to HMRC, returns
payslips and year-end documents to HR to publish, and posts the payroll journal. HR still never calculates
pay. Its existing Xero, CSV and SFTP connectors stay for customers who use other payroll software.

## 7. Cross-product experience

- **App switcher and deep links** from the shared shell (`DESIGN_SYSTEM.md` §6).
- **Embedded summary cards**: a product shows a small read-only card from another product inside its own
  pages. The owning product returns a small server-driven card (title key, fields, actions, deep link) that the
  shared shell renders with shared components, so no product embeds another product's code. Cards are fetched over
  the API with the viewer's permissions (for example the invoices and balance on a CRM
  account, or the deal on an ERP sales order). The card links to the full record in the owning product.
- **Suite inbox**: approvals and tasks from every product in one list, each opening in its own product.
- **Suite search** (later): a federated query to each product's search endpoint in the same cell, merged and
  permission-filtered by each product.
- **Suite analytics** (later): a per-cell analytics store fed by the event stream, with a shared semantic
  layer, for cross-product reports (for example revenue per salesperson against payroll cost).

## 8. Tests every link needs

- Contract tests against the partner's pinned OpenAPI and AsyncAPI documents (mock in CI).
- Ownership: an edit to a field the other product owns is refused, and reconciliation restores it.
- Region: a link across cells is refused before anything is sent, for every region pair.
- Allow-lists: each outbound flow sends exactly its listed fields.
- Idempotency and replay: duplicate and out-of-order events change nothing.
- Staleness: screens say so after the limit.
- No change for unlinked tenants: additions-only API check and byte-identical outputs.

## 9. Capability negotiation and versioning

Products release independently. `GET /v1/suite/capabilities` returns, for each partner feature, `available`,
`planned` or `off`, so a product can build against a partner feature before it ships (Care's pay link reads
`planned` today) and switch on only when the partner reports `available`. Breaking changes to a partner
contract need a new major version, run side by side for at least six months.
