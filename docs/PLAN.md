# Development plan — Thapsus Finance (PROJECT_MULLAH)

Status: plan for owner approval, 2026-11-07. Scope: `FEATURES.md`. Research and dates: `RESEARCH.md`. Suite context:
`suite/SUITE_PLAN.md` (stages and owner decisions D-1 to D-15), `suite/INTEGRATION.md` (who owns what; the posting
contract).

## 1. Shape of the plan

- **UK first, all cells defined** (D-1). The UK release is a complete UK product: ledger, sales, purchases, banking,
  VAT with Making Tax Digital, CIS, reporting and close, expenses, assets and leases. EU and US packs follow on the same
  ledger, driven by the e-invoicing and tax deadlines in `RESEARCH.md` §2.7.
- **Companies before sole traders.** The target is SMB to mid-market companies (D-2). HMRC has closed new product
  approvals for MTD for Income Tax in 2026–27, so sole-trader quarterly updates are not a launch feature; they are
  reconsidered for the practice edition when HMRC opens the next window.
- **The ledger first, because ERP depends on it** (D-7, D-12). Finance publishes its accounting-events contract
  (OpenAPI and AsyncAPI) in Phase 0, so ERP, CRM and Care can build against a contract-tested mock while Finance builds
  the real thing.
- **Releases.** **F1** UK core · **F2** groups and revenue · **F3** UK year-end and practice edition · **EU** pack ·
  **US** pack · **UK e-invoicing** (Peppol, mandatory from 1 April 2029) · **Payroll** (UK, D-4).
- **Every phase ends** with `pnpm verify` green, docs updated, this file ticked with evidence, and a commit. Nothing is
  ticked without a test or check that proves it, or a HUMAN_TODO entry for a human-only step.

## 2. Phases and suite stages

| Phase | Name | Suite stage | Release |
| --- | --- | --- | --- |
| 0 | Foundations and the published posting contract | 0 | — |
| 1 | Ledger core | 1 | F1 |
| 2 | Sales, receivables and credit control | 1 | F1 |
| 3 | Purchases, payables and payment runs | 1 | F1 |
| 4 | Banking and reconciliation | 1–2 | F1 |
| 5 | UK tax: VAT, Making Tax Digital, CIS | 2 | F1 |
| 6 | Reporting, budgets and period close | 2 | F1 |
| 7 | Expenses, fixed assets and leases | 2 | F1 |
| 8 | Multi-entity, intercompany, consolidation and treasury | 3 | F2 |
| 9 | Revenue recognition, subscriptions and light projects | 4 | F2 |
| 10 | UK year-end accounts, CT600 and the practice edition | 4 | F3 |
| 11 | E-invoicing engine and the EU pack | 4 | EU |
| 12 | US pack | 4 | US |
| 13 | UK e-invoicing and Peppol accreditation | 4–5 | UK e-invoicing |
| 14 | Optional AI features | 3–4 | Each release |
| 15 | UK payroll | 4–5 | Payroll |
| 16 | Suite links live | 3 | All |
| 17 | Scale and launch readiness | 2–5, continuous | All |

## 3. Phases

### Phase 0 — Foundations and the published posting contract

- Monorepo on `@thapsus-suite/*` (`apps/web`, `apps/api`, `apps/worker`, `packages/ledger`, `packages/rules`,
  `packages/db`); `pnpm verify` with every gate in CLAUDE.md plus a **ledger-invariants** gate.
- Tenancy with forced RLS and the isolation suite, Thapsus ID sign-in, permissions catalogue
  (`finance.<module>.<action>`), audit with hash chain, outbox, i18n, the suite shell with Finance's accent, Studio
  wired to Finance records, the encrypted vault for bank details and tax identifiers.
- Money library (integer minor units, ISO 4217, rounding modes from rule packs), exchange-rate store (rate types,
  source, date), shared time utilities with each entity's time zone.
- Rule-pack framework for tax (shared with Thapsus-HR's), with the UK pack skeleton.
- **The accounting-events contract**: event types, schema, idempotency, errors (`periodClosed`, `dimensionInvalid`,
  `unbalanced`), published as OpenAPI and AsyncAPI with a mock server, so ERP and Care can start (`suite/INTEGRATION.md` §6).
- OpenTofu cell and global roots from the platform modules; UK staging after the owner approves `COSTS.md`.
- Fictional seed company (a UK wholesaler with two entities, VAT-registered, using CIS for one subcontractor) and a
  synthetic mid-market generator (20 entities, 5 million journal lines a year, 200,000 invoices, 1 million bank lines).

**Acceptance:** isolation and residency suites pass for every region pair; the contract is published and its mock
passes its own contract tests; full company export (including configuration) works from day one (EU Data Act).

### Phase 1 — Ledger core (F1)

- Legal entities with base currency, fiscal calendar, time zone, tax registrations.
- Chart of accounts with account types mapped to statutory lines; templates as rule-pack data (UK FRS 102 and FRS 105
  formats first; US GAAP and IFRS; EU charts such as SKR03/SKR04 and PCG in the EU pack).
- Dimensions: typed and unlimited (cost centre, department, location, project, customer, supplier, item, employee,
  funder, plus custom), hierarchies, effective-dated values, defaults from master data, **validation rules in Studio**
  ("accounts 6000–6999 need a project"), and dimension-level access.
- Statistical accounts (headcount, floor area, beds, bed-days) for allocations and KPIs.
- Journals: manual with attachments, import with preview, recurring, reversing and auto-reversing, allocations (fixed,
  statistical, step-down), templates, approval (maker-checker) by threshold.
- Invariants in the database (ADR-0004): balanced per currency, immutable once posted, corrections by reversal,
  periods `open`, `soft-closed`, `closed` with lock dates per entity and module, posting into a soft-closed period only
  with permission and reason.
- Multi-currency: daily rates with source, realised and unrealised gains and losses, period-end revaluation
  (auto-reversing), rate override with audit.
- **Accounting-events API live**: posting profiles (configurable in Studio), sub-ledger control accounts that only
  system postings may hit, drill-down both ways, nightly sub-ledger reconciliation.
- Opening balances and migration from Xero, QuickBooks Online and Sage (chart mapping, history import, reconciliation
  report).
- Trial balance, GL detail, journal reports.

**Acceptance:** property-based tests (trial balance always balances; reversal twice is identity; closed periods
refuse writes; rounding differences land in the named account); ERP's and Care's mocks replaced in their contract
tests by the real API in a test harness; migration of a fictional Xero export reconciles to the penny.

### Phase 2 — Sales, receivables and credit control (F1)

- Customer financial fields (terms, credit limit, tax ids, Peppol id, delivery channel, language, currency), owned by
  Finance even when CRM owns the customer.
- Light products and services list (when there is no ERP), quotes and pro-formas, invoices and credit notes on the
  **EN 16931-aligned invoice model** (ADR-0007), repeating invoices, deposits, prepayments and retentions, gap-free
  numbering per series.
- Studio templates for invoices, credit notes and statements; multi-language; per-entity branding.
- Customer statements; reminder and dunning sequences per customer group; statutory late-payment interest and
  compensation as rule-pack data (a change to UK law is a data release).
- Customer portal (view, pay, download, dispute) and payment links through partners: card (hosted fields only),
  Direct Debit, pay by bank including UK commercial variable recurring payments.
- Cash application with suggested matches; credit limits and holds (events to CRM and ERP); bad-debt write-off with
  VAT bad-debt relief; collections worklists with promises to pay and disputes; aged receivables and DSO.

**Acceptance:** golden tests for invoice totals, tax rounding per line and per document, credit-note allocation, and
late-payment interest; gap-free numbering under concurrency; card numbers never reach Thapsus (tested).

### Phase 3 — Purchases, payables and payment runs (F1)

- Supplier master with verified bank-detail change control (step-up, a second approver, a cooling-off period,
  Confirmation of Payee where available), tax ids, CIS status, Peppol id, structured addresses (SWIFT and ISO 20022
  require them).
- Document inbox per tenant (email address, upload, phone camera), bill entry, structured e-invoices preferred over
  scans, duplicate detection.
- Light purchase orders and two- and three-way matching with tolerances (ERP supplies PO and receipt facts when linked).
- Approval workflows in Studio (amount, dimension, category, supplier risk), delegation, mobile approval, separation
  of duties.
- Payment runs (by due or discount date, approvals, multi-currency, partial payments), bank files (UK Faster Payments
  and Bacs Standard 18, ISO 20022 pain.001), payment initiation through a regulated partner, remittance advices,
  early-payment discounts, supplier statement reconciliation, aged payables, UK payment-practices reporting data.

**Acceptance:** a supplier bank-detail change cannot be used in a payment run until approved and cooled off (tested);
payment files validate against their schemas; duplicate bills are caught in golden scenarios.

### Phase 4 — Banking and reconciliation (F1)

- Bank feeds through a regulated aggregator per cell (UK first; GoCardless Bank Account Data takes no new sign-ups,
  so it is not planned).
- Statement import: CSV, OFX, QIF, MT940, camt.053, BAI2.
- Bank rules (conditions to account, tax code, dimensions, contact) with test-on-history preview; reconciliation with
  suggested matches (one-to-one, one-to-many, many-to-one, partial); cash coding; reconciliation report proving book to
  bank; payment-processor clearing (Stripe, GoCardless, PayPal payouts net of fees); card and petty-cash accounts;
  transfers between accounts.

**Acceptance:** a year of fictional bank lines reconciles; the reconciliation report ties to the ledger; feeds run
against the aggregator's sandbox.

### Phase 5 — UK tax: VAT, Making Tax Digital, CIS (F1)

- VAT codes and the 9-box return from rule-pack data; schemes (cash accounting, flat rate, annual accounting);
  domestic reverse charge; postponed import VAT; bad-debt relief; fuel scale charges.
- **Partial exemption** (standard method, de minimis tests, annual adjustment): rare in competitors and essential for
  care providers, charities and property businesses.
- **Making Tax Digital for VAT**: obligations, submission and liabilities through HMRC's API with fraud-prevention
  headers; digital links throughout; HMRC recognition (HUMAN_TODO M-3).
- **CIS**: subcontractor verification, deductions, monthly CIS300 returns including nil returns (reinstated April
  2026), statements, gross payment status.

**Acceptance:** golden tests for every VAT scheme and partial-exemption method; MTD sandbox end to end with valid
fraud-prevention headers; CIS golden tests.

**Release F1 gate:** a design-partner company runs three months of fictional books end to end in staging (sales,
purchases, banking, VAT return, close); an accountant reviews the outputs.

### Phase 6 — Reporting, budgets and period close (F1)

- P&L, balance sheet, cash-flow statement (indirect), trial balance, GL detail, aged AR and AP, tax reports, with
  drill-down from every figure to the source document (in Finance or another product).
- Report builder (rows, columns, formulas, dimensions, comparatives, budgets), management packs with commentary and
  scheduled distribution, role dashboards, Excel and Google Sheets export, an OData feed.
- Statutory layouts (Companies Act formats, FRS 102 and 105, IFRS including IFRS 18 from 2027, US GAAP).
- Budgets by account, dimension and period with versions, approval and locking; budget against actual.
- Close management: checklist templates with owners and dependencies; balance-sheet reconciliations with preparer and
  reviewer; accrual and prepayment schedules with automatic amortisation and reversal; flux analysis with explanation
  thresholds; an audit-ready close pack.

**Acceptance:** reports agree with the trial balance on the synthetic tenant within their latency budget; IFRS 18
layout golden test; close checklist dependencies enforced.

### Phase 7 — Expenses, fixed assets and leases (F1 complete)

- Expense claims with phone receipt capture, multi-line and multi-currency; mileage at statutory rates (rule-pack
  data, never literals); policy rules; approval from the HR reporting line when linked; reimbursement in the payment
  run (employee bank details read from HR's vault through a narrow scope, `suite/INTEGRATION.md` §4); card-statement
  import and receipt matching (no card issuing).
- Fixed-asset register (straight-line, reducing balance, units of production; disposals; revaluation), capitalisation
  from bill lines, multiple depreciation books (book and tax; UK capital allowances feed CT600 in Phase 10).
- **Lessee lease accounting** under FRS 102 section 20 (from 1 Jan 2026), IFRS 16 and ASC 842: right-of-use asset,
  liability, schedules, modifications, remeasurement, disclosures. A mid-market essential in the UK now, not an extra.

**Acceptance:** lease golden tests against worked examples for each standard; depreciation golden tests; mileage
rates come only from the pack.

### Phase 8 — Multi-entity, intercompany, consolidation and treasury (F2)

- One tenant with many entities sharing masters; automatic due-to and due-from on cross-entity transactions; paired
  intercompany invoices; intercompany reconciliation with mismatch reports; shared services (pay or receive on behalf).
- Consolidation with eliminations (including unrealised profit on intercompany stock from ERP), currency translation
  (current-rate method, CTA), non-controlling interest and indirect holdings (enterprise), several consolidation
  structures, a group close dashboard, drill-down from consolidated figures.
- Treasury basics: bank-account register, cash positioning across entities and currencies, loan schedules with
  interest accruals and covenant tracking.

**Acceptance:** consolidation golden scenarios (wholly and partly owned, three currencies) tie to hand-calculated
results.

### Phase 9 — Revenue recognition, subscriptions and light projects (F2)

- Deferred revenue and expense schedules from invoice and bill lines.
- Subscriptions (plans, proration, upgrades, renewals, MRR and ARR) and usage ingestion; or connection to Stripe
  Billing and Chargebee where the customer uses them.
- A contract revenue engine for IFRS 15, ASC 606 and FRS 102 section 23 (five-step model from 1 Jan 2026):
  performance obligations, allocation, variable consideration, modifications, contract assets and liabilities.
- Light projects for customers without ERP: projects as a dimension, time from HR, billing by time and materials or
  milestones, profitability. Deep project accounting stays in ERP.

**Acceptance:** revenue golden tests from the standards' illustrative examples.

### Phase 10 — UK year-end accounts, CT600 and the practice edition (F3)

- Accounts production: micro (FRS 105), small (FRS 102 section 1A), abridged and filleted, with iXBRL tagging on the
  FRC taxonomy and Companies House software filing (software-only filing from April 2028; HMRC's free filing service
  has closed).
- CT600 with iXBRL computations and capital allowances.
- Practice edition for accountants and bookkeepers: free accountant access, a multi-client dashboard (reconciliation
  status, VAT due, overdue filings, exceptions), client templates pushed to many clients, wholesale billing.

**Acceptance:** generated iXBRL validates against the FRC taxonomy and Companies House test service; CT600 passes
HMRC's test service (HUMAN_TODO M-6).

### Phase 11 — E-invoicing engine and the EU pack

- Syntaxes from the EN 16931 model: Peppol BIS Billing 3.0 (UBL), CII, XRechnung, Factur-X and ZUGFeRD, FatturaPA,
  KSeF FA(3); receive and validate all of them.
- Peppol send and receive through a certified access-point partner (ADR-0007), plus France's approved-platform
  connection, Poland's KSeF API and Italy's SDI through partners.
- Country order, by mandate dates: Germany (receive now; issue from 1 Jan 2027 above €800,000, everyone from 1 Jan
  2028), Belgium (live), France (receive and large issuers live; SMEs issue from 1 Sep 2027), Poland (live), then
  Ireland, Netherlands, Spain (Verifactu and B2B, with dates under review), Italy, Portugal, Croatia, Greece, Romania.
- EU VAT per country, OSS and IOSS, intra-EU reverse charge and triangulation, EC sales lists where still required;
  SAF-T exports (Portugal, Poland JPK, Romania D406, Austria, Lithuania, Luxembourg FAIA); Germany's GoBD (process
  documentation, data access for audits, DATEV export, SKR03 and SKR04) and France's FEC; retention per country as
  data (Germany 8 years for vouchers, 10 for books).
- SEPA credit transfer, direct debit and instant with Verification of Payee; EU aggregator in the EU cell; EU languages
  needed by the first EU customers.
- Plan for ViDA intra-EU digital reporting from 1 July 2030.

**Acceptance:** each syntax validates against its official schematron or schema; Peppol partner sandbox send and
receive; per-country golden tests; coverage published per country.

### Phase 12 — US pack

US GAAP charts; sales and use tax through a tax engine (exemption certificates, nexus monitoring with state
thresholds as data, use-tax accrual on bills); 1099-NEC and 1099-MISC at the $2,000 threshold for payments from 2026,
TIN collection, e-filing through IRS IRIS (FIRE closes 19 Nov 2026), state filing; ACH NACHA files with
fraud-monitoring hooks; positive pay; US aggregator in the US cell (Plaid, MX or Finicity); ASC 842 and ASC 606 already
in Phases 7 and 9; SOX support (segregation-of-duties conflict reports, access reviews, approval evidence) and a SOC 1
Type II report (HUMAN_TODO).

### Phase 13 — UK e-invoicing and Peppol accreditation

UK e-invoicing is mandatory for VAT-registered businesses from 1 April 2029 on Peppol, with HMRC as the UK Peppol
Authority (announced roadmap of 28 Oct 2026; technical consultation spring 2027; readiness testing summer 2028).
Finance either keeps its partner or becomes a certified access point and SMP (decision by mid-2027), meets the UK
Peppol Authority's requirements, and passes readiness testing in summer 2028.

### Phase 14 — Optional AI features

Off by default, through the suite AI gateway, drafts only (`suite/AI.md`, ADR-0009). In order of value: bank-match
suggestions (per bank account opt-in, undo, explanation); bill, receipt and statement capture; coding suggestions
with the VAT impact shown; duplicate and anomaly flags; supplier bank-detail fraud alerts; cash application; cash-flow
forecast explanations; close assistant that prepares journals into an approval queue; variance commentary drafts;
collections email drafts; plain-language questions over governed reports with a snapshot of the data used;
contract-to-revenue and lease extraction into draft schedules; VAT return review before a person submits; migration
mapping assistant; a configuration copilot that drafts Studio changes in a sandbox; an MCP server with read and
draft-only tools. **Never**: credit scoring of natural persons (EU AI Act high-risk) or scoring of employees from
expense behaviour.

### Phase 15 — UK payroll (D-4)

Consumes Thapsus-HR's closed pay-period snapshot through HR's "Thapsus Finance payroll" connector, or a CSV import:
gross to net (PAYE, National Insurance, student and postgraduate loans), statutory payments (from HR's packs), pension
auto-enrolment with provider integrations, RTI (FPS and EPS) to HMRC, year-end (P60), payslips and year-end documents
returned to HR for publishing, the payroll journal, net-pay payment runs and liability payments. HMRC RTI recognition
and payroll golden tests against HMRC's test scenarios before release. Rates are rule-pack data.

### Phase 16 — Suite links live

HR (employees as payees and approvers, cost-centre assignment, legal entities and dimensions to HR); CRM (customer
financial fields, invoice and payment status, credit holds, quote to invoice when there is no ERP, subscriptions);
ERP (the live accounting-events API, credit checks, supplier and customer financial fields); Care (UK only: funder and
self-funder invoices, credit notes and receipts as accounting events with cost centres per location; payment status
back; residents' money control totals). All per `suite/INTEGRATION.md` with its test list.

### Phase 17 — Scale and launch readiness

Load tests against the mid-market targets (300 concurrent users per tenant; trial balance for a year of 5 million
lines under 2 seconds; a payment run of 10,000 lines under 1 minute; p95 under 300 ms for common reads); threat model;
ASVS L2 review; penetration test; SOC 1 and SOC 2 evidence; accessibility audit; restore and zone-failure drills;
staging in each region before its production cell; runbooks.

## 4. Dependencies

| Needs | From | By phase |
| --- | --- | --- |
| Platform kit (core, db, auth, Studio, shell, vault) | `thapsus-platform` | 0 |
| Employees, approvers, bank details for reimbursement | Thapsus-HR partner scopes (`PROJECT_KAREN/docs/SUITE_INTEGRATION_PLAN.md` S.5, S.6) | 7 (mock), 16 |
| Customer master | CRM | 2 (local), 16 |
| PO and receipt facts | ERP | 3 (local light POs), 16 |
| HR period snapshot connector | Thapsus-HR S.7 | 15 |

## 5. Risks specific to Finance

| Risk | Mitigation |
| --- | --- |
| Ledger errors | Tests first; database-enforced invariants; property-based tests; nightly reconciliations; accountant review in pilots |
| HMRC recognition and Peppol accreditation lead times | Start early (HUMAN_TODO M-3, M-7); build against sandboxes; partner access point until accreditation |
| EU mandates moving (Spain, Poland penalties, Belgium e-reporting) | Country packs as data with dates; re-check each quarter; publish coverage per country |
| Payment fraud through changed bank details | Bank-detail change control, Confirmation or Verification of Payee, fraud alerts, second approver |
| Competing with Xero and QuickBooks on price | Lead on mid-market depth (dimensions, multi-entity, partial exemption, leases, close) and the suite; transparent pricing |
