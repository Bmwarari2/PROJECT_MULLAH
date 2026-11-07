# Research — Thapsus Finance

> Research dossier prepared on 2026-11-07 for the plan in `PLAN.md` and `FEATURES.md`. Every regulatory fact carries its
> source and status (in force, adopted with a date, announced, proposed, unverified). Facts are re-checked when the matching
> phase starts; anything marked unverified must be checked against the primary source before it becomes rule-pack data.
> Where this dossier and the plan differ, the plan records the reason (for example, residents' money stays in Thapsus Care
> with control totals posted to Finance, `suite/INTEGRATION.md` §4, where §4.4 here suggests a client-money ledger in Finance).


- **Prepared:** 7 November 2026
- **Scope:** commercial, multi-tenant accounting and finance SaaS for UK, EU and US companies, interoperating with Thapsus-HR, Thapsus Care, BOSSMAN (CRM) and KASONGO (ERP, which will use MULLAH's ledger as its finance core).
- **Method:** web search plus fetches of official pages (gov.uk, IRS, ECB, FRC, Nacha, Federal Reserve, impots.gouv.fr, European Commission, vendor pricing pages). Secondary sources (Big Four, law firms, VAT trackers, vendor blogs) were used where no official page was found; these are labelled.
- **Limits:** the shared web-search budget for this session ran out near the end of the research. Items I could not confirm are marked **unverified** and collected in the final section. Competitor feature marks in section 1 come from vendor documentation knowledge plus the searches below. They are indicative, not a contractual comparison. Check them before you use them in marketing.

**Status labels used for regulation**

| Label | Meaning |
| --- | --- |
| **In force** | Law or binding rule that applies today (7 Nov 2026) |
| **Adopted, applies from <date>** | Enacted or formally decided, not yet applying |
| **Announced / planned** | Government announcement, roadmap or policy paper; legislation not yet passed |
| **Proposed / draft** | Bill, draft law or consultation |
| **Unverified** | I could not confirm it from an official or reliable source |

---

## 0. Executive summary

### 0.1 Ten findings that shape the build order

1. **UK e-invoicing is confirmed for 1 April 2029** for all VAT-registered businesses, B2B and B2G, on **Peppol**. HMRC will become the UK Peppol Authority. There is **no real-time reporting** in the 2029 design. Software providers are expected to complete "any required accreditation process with the UK Peppol Authority" by 1 April 2029. A technical consultation is due in spring 2027 and readiness testing in summer 2028. Status: **announced** (HMRC/DBT roadmap, 28 Oct 2026); primary legislation still to pass. ([gov.uk roadmap](https://www.gov.uk/government/publications/e-invoicing-implementation-roadmap/roadmap-for-e-invoicing-implementation))
2. **Companies House software-only accounts filing moved to April 2028** (from April 2027). From then all accounts must be filed as **iXBRL through commercial software**, and web and paper accounts filing closes. On **23 Oct 2026** the government **paused** the requirement for small and micro companies to file a profit and loss account, and **abridged and filleted accounts stay**. ([gov.uk, 23 Oct 2026](https://www.gov.uk/government/news/companies-house-update-to-accounts-filing-from-april-2028)) HMRC's free joint accounts-and-CT600 filing service **has closed** ([gov.uk](https://www.gov.uk/file-your-company-accounts-and-tax-return)). Year-end accounts plus CT600 is a real gap that SMEs now have to fill with software.
3. **EU domestic e-invoicing is already live or close** in the markets that matter: Belgium (Peppol B2B since 1 Jan 2026), Poland (KSeF since 1 Feb and 1 Apr 2026), France (all businesses receive, and large and mid-size businesses issue, since **1 Sep 2026**; SMEs issue from **1 Sep 2027**), Germany (everyone must receive now; issuing applies from **1 Jan 2027** above €800k turnover and **1 Jan 2028** for all), Croatia (1 Jan 2026), Greece (phase 2 from 2 Nov 2026), Italy (SDI since 2019) and Romania (RO e-Factura since 2024). **ViDA** makes intra-EU digital reporting mandatory from **1 July 2030**. MULLAH cannot launch in the EU without an EN 16931-based e-invoicing engine.
4. **Spain is a moving target.** Verifactu applies in law from **1 Jan 2027** (companies) and **1 Jul 2027** (others). On 5 Oct 2026 Hacienda **announced** a further delay to **Oct 2028**, but no instrument has been published. The B2B e-invoicing regulation (**RD 238/2026**, in force 20 Apr 2026) starts its 12- or 24-month clocks only when a Ministerial Order is published; secondary sources give Oct 2027 and Oct 2028.
5. **US 1099 thresholds changed.** Under the One Big Beautiful Bill Act, 1099-NEC and 1099-MISC are needed only at **$2,000+ for payments made after 31 Dec 2025**, inflation-indexed from 2027. The 1099-K threshold went back to **$20,000 and 200 transactions** retroactively ([IRS](https://www.irs.gov/instructions/i1099mec), [IRS 1099-K FAQ](https://www.irs.gov/newsroom/form-1099-k-faqs-third-party-filers-of-form-1099-k)). **FIRE closes on 19 Nov 2026**, so IRIS (portal or A2A API) is the only route from filing season 2027 ([IRS IR-2026-99](https://www.irs.gov/newsroom/irs-reminder-information-return-e-file-system-transitioning-to-a-new-platform)).
6. **FRS 102 Periodic Review 2024** applies to periods beginning on or after **1 Jan 2026**. It moves UK GAAP lessees onto the balance sheet (an IFRS 16-style model) and introduces a five-step revenue model. A lease module and contract-revenue schedules become **mid-market essentials in the UK**, not enterprise extras. **IFRS 18** (new P&L structure) applies from **1 Jan 2027** (EU endorsed Feb 2026).
7. **Open banking diverges by region.** UK **commercial VRP** went live on **2 June 2026** (UK Payments Initiative, Wave 1), which enables pay-by-bank collections. The EU **PSD3/PSR** texts were agreed (provisional deal 27 Nov 2025; final texts published by the Council in April 2026), but I could not confirm Official Journal publication; application is expected around late 2027 to 2028. **FIDA** is stalled and reportedly set for withdrawal. The **US CFPB 1033 rule is enjoined** and being rewritten. Use aggregators, not regulatory APIs, in the US. **GoCardless Bank Account Data no longer accepts new sign-ups**.
8. **EU payments rails:** the Instant Payments Regulation made SCT Inst and **Verification of Payee** mandatory for euro-area PSPs from **9 Oct 2025** (non-euro PSPs: receive 9 Jan 2027, send and VoP 9 Jul 2027). SWIFT CBPR+ stops accepting **unstructured postal addresses after 14 Nov 2026** (a possible extension is unverified). Supplier master data and payment files must hold structured addresses now.
9. **The AI regulatory clock moved.** The EU **Digital Omnibus** (Reg. (EU) 2026/1744, in force 27 Jul 2026 per secondary sources) moves Annex III high-risk obligations, such as **credit scoring of natural persons** and **worker monitoring**, to **2 Dec 2027**. The Article 50 transparency duties have applied since **2 Aug 2026**. The UK replaced Art 22 GDPR with **Arts 22A–22D** (in force 5 Feb 2026 per secondary sources). Every leader now ships agents that **prepare work for human approval** (Xero JAX auto-reconcile is opt-in per bank account; Business Central Payables and Sales Order agents are human-in-the-loop). That is the pattern to copy.
10. **The competitive bar for SMB now includes AI.** Xero JAX auto-reconciliation became generally available in 2026, and Intuit reports more than 3 million customers using its agents. AI-native ledgers are funded and moving upmarket: Rillet raised a $100m Series C at a $1bn valuation in Aug 2026 with 600+ customers, and Campfire ships multi-book, leases and close. In the mid-market, Business Central list prices rose to **$80 (Essentials) and $110 (Premium) per user per month**, and its agents are billed on top in Copilot Credits.

### 0.2 Surprises

- The UK pause on small-company P&L filing (23 Oct 2026) reversed a decision made only in June 2026.
- HMRC chose **not** to add real-time reporting to UK e-invoicing in 2029. Its separate e-invoicing digital reporting consultation is exploratory.
- Poland's KSeF penalties may slip to 1 Jan 2028. A government bill (Sejm print 3245) was sent to the Sejm on 20 Oct 2026 but is not law.
- Construction Industry Scheme nil returns are back from April 2026.
- HMRC has closed production-credential requests for **new** 2026–27 MTD Income Tax quarterly-update products because "the market window has closed". A new entrant has to wait for the next window.
- Germany cut retention for accounting vouchers (Buchungsbelege) to **8 years** under BEG IV, but banks and insurers keep 10. GoBD (14 Jul 2025) now says that for e-invoices **the structured XML part alone** may be archived.

### 0.3 Recommended build order (derived from the deadlines)

| Order | Capability | Why now |
| --- | --- | --- |
| 1 | Immutable double-entry ledger with append-only, hash-chained audit, gapless document numbering per series, multi-entity, dimensions and multi-currency | Required by GoBD, Verifactu, Portugal and SAF-T exports; KASONGO depends on it |
| 2 | UK launch pack: MTD VAT (with fraud-prevention headers), CIS, bank feeds (TrueLayer/Yapily), invoicing, AP, payment runs (Faster Payments/Bacs), FRS 102 lease and revenue schedules | UK is the home market; FRS 102 changes apply to 2026 year-ends |
| 3 | E-invoicing core: EN 16931 semantic model, UBL and CII syntaxes, Peppol through a certified access-point partner; receive in Germany and Belgium, send in Belgium | Belgium is live; Germany issues from 1 Jan 2027 |
| 4 | US pack: sales-tax engine integration, 1099 via IRIS A2A, ACH/NACHA files with fraud-monitoring hooks, Plaid/MX/Finicity feeds | FIRE is gone; the 1099 threshold has changed |
| 5 | France PA connection, Poland KSeF API, Germany XRechnung/ZUGFeRD issuing, Spain Verifactu (watch the delay) | Dates fall from 1 Jan 2027 to Oct 2028 |
| 6 | UK year-end: accounts production plus iXBRL (FRC taxonomy) to Companies House, CT600 with iXBRL computations | Software-only filing from April 2028 |
| 7 | Enterprise: consolidation with NCI and CTA, multi-book, ASC 606 and IFRS 15 engine, close management, SOX evidence | Needed for KASONGO customers and mid-market |
| 8 | UK Peppol accreditation as an access point or via partner, before summer 2028 readiness testing | UK mandate 1 Apr 2029 |
| 9 | ViDA DRR (intra-EU reporting) | 1 Jul 2030 |

---

## 1. Feature catalogue benchmarked against market leaders

### 1.0 Competitor set and abbreviations

| Code | Product | Segment | Notes |
| --- | --- | --- | --- |
| XR | Xero | Micro/SMB, accountant-led | Unlimited users; tiered by features; strong UK/AU/NZ; JAX AI |
| QBO | QuickBooks Online (Simple Start to Plus) | Micro/SMB | User caps per plan; Intuit Intelligence agents |
| QBA | QuickBooks Online Advanced | Upper SMB | 25 users, custom roles, workflows, revenue recognition, Spreadsheet Sync |
| IES | Intuit Enterprise Suite | Lower mid-market, multi-entity | AI-driven, construction and services focus; quote-based |
| SI | Sage Intacct | Mid-market finance (US/UK) | Dimensions, consolidation, revenue management, AI agents 2026 |
| SA / S50 | Sage Accounting (cloud) / Sage 50 | UK SMB | Strong UK tax (VAT, CIS) |
| NS | Oracle NetSuite (financials) | Mid-market to enterprise | OneWorld multi-entity, multi-book, ARM, SuiteBilling, NetSuite Next agents |
| BC | Microsoft Dynamics 365 Business Central | SMB/mid-market ERP | Dimensions, intercompany, Copilot and agents |
| FA | FreeAgent | UK micro, sole traders, contractors | Free via NatWest/RBS/Ulster/Mettle; strong MTD ITSA and Self Assessment |
| ZB | Zoho Books | SMB, global | Cheap; custom modules; workflow rules; Zoho ecosystem |
| OD | Odoo Accounting | SMB/mid-market, open-core ERP | Multi-company, Studio (no-code), Peppol access point |
| DG | Digits | AI-native SMB ledger | "Autonomous General Ledger" (Mar 2025), agents |
| RL | Rillet | AI-native ERP for venture-backed and mid-market | Real-time GL, ASC 606 from contracts |
| CF | Campfire | AI-native ERP for startups moving up | Ember AI, multi-entity, revenue, leases, multi-book |

Tier labels for MULLAH: **Core** = launch (all paid plans), **Advanced** = higher SMB/mid-market tier, **Enterprise** = top tier or add-on. "Who has it" lists products with a native or first-party capability; "(partner)" means it usually needs a marketplace add-on.

### 1.1 Chart of accounts and dimensions

| Feature | Tier | Who has it | Notes for MULLAH |
| --- | --- | --- | --- |
| Hierarchical chart of accounts with account types and sub-types mapped to statutory lines (UK Companies Act formats, US GAAP, IFRS) | Core | All | Store the mapping as data per jurisdiction pack, not code |
| Jurisdiction CoA templates (UK, US, DE SKR03/SKR04, FR PCG, ES PGC, PL, IT) | Core (UK/US), Advanced (EU) | XR, QBO, BC, NS, SI, OD (fiscal localisations), ZB | Germany expects SKR03/04 for DATEV-compatible exports |
| Unlimited configurable dimensions (department, location, project, fund, funder, cost centre, product line, custom) | Core (up to 10), Advanced (unlimited) | SI (standard plus user-defined dims), NS (dept/class/location plus custom segments), BC (2 global plus 6 shortcut, unlimited dims), OD (analytic plans), ZB (reporting tags), QBO (classes/locations: 40 on Plus, unlimited on Advanced), XR (**2 active tracking categories, ~100 options recommended**, still unchanged in 2026) | Xero's two-category limit is a recurring user complaint (open idea threads), so dimension depth is a clear differentiator |
| Dimension hierarchies and roll-ups | Advanced | SI, NS, BC, OD | |
| Dimension validation rules (e.g. account 6xxx requires Project; Funder required for income) | Advanced | SI (Smart Rules), NS (segment rules), BC (default dimension "code mandatory/same/no code") | Enforced at posting; configurable without code |
| Default dimensions from master data (customer, supplier, item, employee from Thapsus-HR) | Core | BC, SI, NS | |
| Dimension-level access security (users see only their entities, locations or funds) | Enterprise | SI (entity/location restrictions), NS (subsidiary restrictions), BC (security filters) | Pair with PostgreSQL RLS for tenant isolation; dimension security is a second, in-tenant layer |
| Effective-dated dimension values (active from and to; reorganisations) | Advanced | NS, SI (partial) | Matches Thapsus effective-dating rules |
| Statistical accounts (headcount, floor area, beds, occupancy) | Advanced | SI, NS | Drives allocations and KPIs; care homes need bed-days |
| Cash and accrual reporting bases from the same ledger | Core | XR, QBO, FA, SI (cash/accrual books), ZB | |
| Multi-book / multi-GAAP (local GAAP, IFRS, tax book, management book) | Enterprise | NS (Multi-Book Accounting), SI (multiple reporting books), CF (multi-book added Jun 2026), OD (partial), BC (extensions) | Key for EU subsidiaries of UK/US groups |
| Account mapping to tax return boxes (VAT boxes, sales-tax categories) | Core | All | |

### 1.2 General ledger and journals

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Manual journals with attachments, notes and dimensions | Core | All | |
| Journal import (CSV/Excel/Sheets) with validation and preview | Core | All | |
| Recurring journals (schedules, templates, end dates) | Core | XR (repeating), QBO, SI, NS, BC (recurring general journals), ZB, OD | |
| Reversing and auto-reversing journals (accruals) | Core | XR (auto-reversing), QBO, SI, NS, BC, OD | |
| Journal approval workflow (maker-checker, thresholds) | Advanced | SI, NS, BC, QBA (workflow automation), ZB, OD; XR has no manual-journal approval (unverified for 2026) | Needed for SOX customers |
| Allocations: fixed %, statistical-driver, step-down, recurring | Advanced | SI (dynamic allocations), NS (allocation schedules, statistical allocations), BC (allocation accounts), CF | Care groups allocate central overhead by bed-days |
| Period lock dates (soft lock for staff, hard lock for all; per entity and module) | Core | XR (lock dates), QBO (closing date), SI (module close), NS (period close, AR/AP/all locks), BC (posting date ranges), ZB (transaction locking), FA | |
| Posting into closed periods by special permission with reason and audit | Advanced | NS, SI, BC | |
| Immutable posted entries; corrections only by reversal | Core | BC (no deletion of posted entries), NS (configurable), SI; **XR and QBO allow edits and deletes with history** | GoBD and Verifactu-style compliance needs immutability; build it in from day one |
| Continuous, real-time sub-ledger to GL posting (no batch) | Core | RL, CF, DG (AI-native); NS, BC post in real time | Event-sourced posting via the outbox fits the Thapsus architecture |
| Opening balances and conversion from Xero/QBO/Sage (CoA mapping, history import) | Core | All (often via partners such as Movemybooks) | AI mapping assist (section 3) |
| Journal templates and standard narratives | Core | Most | |
| Year-end close entries (retained earnings roll-forward) | Core | All | |

### 1.3 Multi-entity, intercompany and consolidation

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Many organisations under one login (separate books) | Core | XR, QBO, FA, ZB, SA | Accountants and small groups |
| True multi-entity ledger with shared masters (CoA, customers, suppliers, items) | Advanced | SI, NS (OneWorld), BC (multiple companies), OD (multi-company), IES, CF, RL | Must be routable to a dedicated database for very large tenants (Thapsus rule) |
| Automatic intercompany due-to/due-from on cross-entity transactions | Advanced | SI, NS, BC (intercompany partners), OD (inter-company rules), IES, CF | |
| Paired intercompany invoices (AR in one entity creates AP in the other) | Advanced | NS, BC, OD, SI | |
| Intercompany reconciliation and matching with mismatch reports | Enterprise | NS, SI; BC (basic) | |
| Consolidation with automatic eliminations | Advanced | SI (Consolidation; affiliate-entity dimension GA in 2025 R2), NS, BC (business-unit consolidation), IES, CF, RL; **XR and QBO need add-ons** (Syft, Fathom, Spotlight) | |
| Currency translation (current-rate method; P&L at average, equity at historical; CTA posting) | Advanced | SI (CTA automation for IFRS and US GAAP), NS (consolidated exchange rates), BC, CF | |
| Non-controlling (minority) interest, partial ownership, indirect holdings | Enterprise | SI (Advanced Ownership Consolidation), NS (often partner), BC (no native support, unverified) | |
| Multiple consolidation structures (statutory, management, regional) | Enterprise | SI, NS | |
| Shared services: pay or receive on behalf of another entity, with automatic intercompany entries | Advanced | SI, NS, CF | |
| Group close dashboard (status by entity) | Advanced | NS, SI, CF | |
| Drill-down from consolidated figure to entity transaction | Advanced | SI, NS, CF | |

### 1.4 Multi-currency and revaluation

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Foreign-currency invoices, bills, bank accounts and contacts | Core | XR (Comprehensive and above), QBO, ZB (Professional and above), FA, SA (Plus), SI, NS, BC, OD | Store money as integer minor units plus ISO 4217 code (Thapsus rule) |
| Automatic daily FX rates with recorded source and rate type | Core | XR (XE), BC (exchange rate service), NS, SI | |
| Rate types (spot, month-end, average, historical, budget) | Advanced | NS, SI, BC | Needed for translation and budgets |
| Realised gains and losses on settlement | Core | All with multi-currency | |
| Unrealised revaluation at period end (auto-reversing), by account and entity | Core | XR, QBO, SI, NS, BC, OD | |
| Additional reporting currency | Advanced | BC, NS (multi-book), SI | |
| Rate override per transaction with audit | Core | Most | |

### 1.5 Sales and accounts receivable

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Customer master (terms, credit limit, tax IDs, Peppol participant ID, delivery channel, language, currency) | Core | All | BOSSMAN (CRM) is the likely golden source for prospects; MULLAH owns billing accounts (section 4) |
| Quotes and estimates converted to invoices or orders | Core | XR, QBO, ZB, FA, SA, OD, BC, NS, SI | |
| Invoices, pro-formas and credit notes with allocation | Core | All | Gapless numbering per series (ES, IT, PT, PL) |
| Repeating invoices | Core | All | |
| Deposits, prepayments and retentions (construction) | Advanced | XR (prepayments), QBO, BC, NS, SI, SA (CIS-related) | |
| Progress and milestone billing from projects | Advanced | XR Projects (Ultimate), QBA, IES, SI, NS, BC (Jobs) | |
| Subscription billing (plans, proration, upgrades, MRR/ARR) | Advanced | ZB (Zoho Billing), SI, NS (SuiteBilling), RL, CF | |
| Usage-based (metered) billing | Enterprise | NS, SI, RL, CF | |
| Customer statements (open item and activity) | Core | All | |
| Automated reminders and dunning (multi-step, per-customer overrides, statutory late-payment interest) | Core | XR (reminders), QBO, FA, ZB, SA, SI, NS (Collections Management), BC (reminders), OD (follow-up levels) | UK: Late Payment of Commercial Debts (Interest) Act 1998 interest and fixed compensation; figures belong in rules data |
| Collections workspace (worklists, promises to pay, disputes, notes, AI-drafted emails) | Advanced | NS, BC (collections features), SI (partner); XR/QBO through add-ons (Chaser, Upflow) | |
| Customer portal (view and pay invoices, statements, disputes, documents) | Advanced | ZB, OD, NS (Customer Center), QBO (basic); XR online invoice only | |
| Payment links and pay-now (card, pay by bank including UK cVRP, Direct Debit, ACH) | Core | XR (Stripe, GoCardless, pay by bank), QBO (QuickBooks Payments), FA, ZB, SA, OD | Stay out of PCI scope by using hosted fields (section 2.4) |
| Cash application (match receipts and remittances to invoices) | Advanced | BC (payment reconciliation journal), NS, SI, DG; XR via JAX reconciliation | |
| Credit limits, credit holds and order blocks | Advanced | BC, NS, SI, OD, ZB | CRM needs credit status (section 4.2) |
| Bad-debt write-off with VAT bad-debt relief (UK) | Core | XR, SA, FA, QBO UK | |
| Structured e-invoice output (Peppol BIS 3.0, XRechnung, Factur-X/ZUGFeRD, KSeF FA(3), FatturaPA) | Core in mandated countries | BC (e-documents framework), OD (Peppol access point), NS (SuiteApps), SI/XR/QBO (partners, varies by country) | Section 2.2 |
| Invoice designer, branding, multi-language documents | Core | All | |
| Aged receivables and DSO | Core | All | |

### 1.6 Purchases and accounts payable

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Supplier master (verified bank details, tax IDs, W-9/W-8, 1099 flag, CIS status, Peppol ID) | Core | All | Bank-detail changes are the top AP fraud vector; see Confirmation of Payee and VoP (section 2.4) |
| Bill capture: inbox email, upload, mobile, OCR or AI extraction | Core | XR (Hubdoc and automated bill entry from Grow up), QBO, FA (Smart Capture), ZB, SI (AP Automation), NS (Bill Capture), BC (Payables Agent), DG | Prefer structured e-invoice data over OCR when present |
| Purchase orders and goods-received notes | Advanced | XR, QBO (Plus), ZB, BC, NS, SI, OD | KASONGO will own full procurement; MULLAH needs "light POs" |
| Two-way and three-way matching with tolerances | Advanced | NS, BC, SI, OD, IES | |
| Approval workflows (amount, dimension or category thresholds; delegation; mobile approve) | Advanced | SI, NS, BC, QBA, ZB; XR (basic) | |
| Supplier portal (onboarding, bank-detail self-service with verification, invoice submission, status) | Enterprise | NS (Vendor Center), OD (portal), SI (partners) | |
| Payment runs (selection by due or discount date, approvals, multi-currency, partial payments) | Core | XR (batch payments), QBO (Bill Pay), FA, SI, NS, BC (suggest vendor payments), OD | |
| Bank payment files (UK Bacs Standard 18, bank bulk CSVs, SEPA pain.001, NACHA, ISO 20022) | Advanced | BC, NS, SI, OD; XR (UK batch files for major banks) | |
| Pay directly from the app through a payments partner (open banking, ACH) | Advanced | QBO (Bill Pay), XR (US via Melio, acquisition reported, unverified in this session), SA (partners) | Payment initiation needs an FCA/EU-authorised PISP partner |
| Remittance advices (email, PDF) | Core | All | |
| Early-payment discounts | Advanced | BC, NS, SI, OD | |
| Duplicate bill detection | Core | Most (rule-based), DG/XR/QBO (AI) | |
| Supplier statement reconciliation | Advanced | Partners (AI tools); NS (partial) | AI candidate |
| 1099 tracking, TIN collection and e-filing | Core (US) | QBO (free 1099 e-file with Advanced and Bill Pay Elite), XR (US partner), SI, NS, BC, ZB | Must use IRIS from 2027 |
| CIS deductions, CIS300 monthly returns, subcontractor verification, statements | Core (UK construction) | XR (CIS add-on £5/month), SA, S50, FA, QBO UK | 2026 changes (section 2.1) |
| Payment practices reporting (UK large companies) | Enterprise | Rare natively | Data byproduct of AP; easy differentiator |

### 1.7 Banking

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Bank feeds (direct bank APIs and aggregators) | Core | All | Regional aggregator choice (section 2.4) |
| Statement import (CSV, OFX, QIF, MT940, camt.053, BAI2) | Core | All (format coverage varies) | camt.053 is the ISO 20022 statement standard |
| Bank rules (conditions to account, tax, dimensions, contact) | Core | XR, QBO, FA, ZB, SA, BC, NS, SI | |
| Reconciliation with suggested matches (1:1, 1:n, n:1, partials) and auto-reconcile | Core | XR (JAX auto-reconcile GA 2026, opt-in per bank account), QBO (AI reconciliation on Plus), BC (Copilot matching), SI, NS, DG | |
| Cash coding (bulk-code unmatched lines) | Core | XR (cash coding) | |
| Reconciliation report proving book-to-bank balance per statement | Core | All | Auditors ask for it |
| Payment-processor clearing (Stripe/PayPal/GoCardless payouts net of fees, multi-currency) | Core | XR, QBO (native feeds or A2X partner) | |
| Credit card and petty cash accounts | Core | All | |
| Positive-pay export (US cheque fraud) | Advanced | NS, SI, BC (extensions) | |
| Lockbox import (US) | Enterprise | NS, SI | |

### 1.8 Cash management and forecasting

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Short-term cash forecast from AR/AP due dates, recurring items and bank balances | Core | XR (30/60/90/180 days by plan), QBO (cash flow planner), ZB, FA, SI, NS, BC | |
| Scenario modelling (what-if) | Advanced | QBO (scenario planning on Plus), NS, SI (Planning) | |
| 13-week driver-based cash forecast | Advanced | NS/SI (planning modules), partners (Float, Fathom) | |
| ML cash forecast (opt-in) | Advanced | BC (Azure AI cash-flow forecast), XR (JAX), DG | Label as an estimate |
| Multi-entity cash position across banks and currencies | Advanced | SI, NS, CF | |

### 1.9 Expenses and mileage (HR overlap)

Thapsus-HR excludes corporate cards and spend management, and per the brief does not run expense claims. **MULLAH should own expense claims**, with Thapsus-HR supplying the employee, manager hierarchy, cost centre and payee bank details.

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Expense claims with receipt capture (PWA camera), multi-line, multi-currency | Core | XR (Grow and above, user limits by plan), FA, ZB (Zoho Expense), SA, SI, NS, OD, BC (Expense Agent preview, 2026) | |
| Mileage claims at statutory rates (HMRC approved mileage rates; IRS standard mileage rate) with distance lookup | Core | XR, FA, QBO, ZB | Rates are statutory figures and belong in the rules pack, never in code |
| Approval routing from the HR org chart (manager, cost-centre owner, finance) | Core | SI, NS, ZB, XR (basic) | Event from Thapsus-HR keeps approvers current |
| Policy rules (limits, receipt required above an amount, VAT reclaim rules, per diems) | Advanced | ZB, SI, NS | |
| Reimbursement in the AP payment run (employee as payee) | Core | XR, NS, SI, BC | |
| Card transaction import as feed and match receipts (no card issuing) | Core | All | Card issuing is out of scope |

### 1.10 Fixed assets and leases

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Fixed asset register (straight-line, reducing balance, units; disposals; revaluation) | Core | XR, QBA, FA, ZB, SI, NS (Fixed Asset Management), BC, OD, S50 | |
| Capitalise from bill lines | Advanced | NS, BC, SI, XR | |
| Multiple depreciation books (book vs tax; UK capital allowances; US MACRS) | Enterprise | NS, BC (depreciation books), SI | Capital allowances feed CT600 |
| Lessee lease accounting: right-of-use asset, liability, schedules, modifications, remeasurement, disclosures (IFRS 16, ASC 842, **FRS 102 s20 from 1 Jan 2026**) | Advanced | NS (lease accounting SuiteApp), CF (added Jun 2026), SI and BC (partners), XR/QBO (none native) | UK change makes this an Advanced, not Enterprise, feature |
| Lessor accounting | Enterprise | NS (partial) | |

### 1.11 Revenue recognition, subscription and usage billing

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Deferred revenue and expense schedules from invoice or bill lines | Advanced | QBA (revenue recognition), SI (Revenue Management), NS, BC (deferral templates), OD, RL, CF; XR (add-ons) | |
| Full ASC 606 / IFRS 15 / FRS 102 s23 engine (contracts, performance obligations, SSP allocation, variable consideration, modifications, contract assets and liabilities) | Enterprise | NS (Advanced Revenue Management), SI (Contract Revenue Management), RL (from contracts), CF (Revenue Automation) | FRS 102's new five-step model widens demand in the UK |
| Subscription management (plans, proration, renewals, MRR/ARR) | Advanced | ZB (Billing), SI, NS (SuiteBilling), RL, CF | Or integrate Stripe Billing/Chargebee |
| Usage ingestion and rating | Enterprise | NS, SI, RL, CF | |
| SaaS metrics (ARR, NRR, churn, cohort) | Advanced | RL, CF, SI (dashboards) | |
| Unbilled receivables and contract-asset reporting | Enterprise | NS, SI, RL | |

### 1.12 Budgets, forecasts and FP&A

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Budgets by account × dimension × period, with versions | Core (single), Advanced (versions) | XR (budget manager; native budgeting tool announced for H2 2026), QBO (Plus and above), ZB (Premium and above), SI, NS, BC, OD | |
| Budget vs actual and variance reports | Core | All with budgets | |
| Budget approval and locking | Advanced | SI, NS, BC | |
| Rolling and driver-based forecasts; headcount planning (from Thapsus-HR) | Enterprise | SI (Planning), NS (Planning and Budgeting / EPM), BC (partners) | |
| Excel and Google Sheets round-trip | Advanced | QBA (Spreadsheet Sync), BC (Excel add-in), SI, NS | |
| Encumbrance (budget check on POs) | Enterprise | NS, BC (partial), SI | Public sector and care funders |

### 1.13 Tax

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| UK VAT codes (standard, reduced, zero, exempt, outside scope), 9-box return, **MTD VAT submission** with fraud-prevention headers | Core | XR, QBO, SA, S50, FA, ZB, BC, NS, SI, OD | MTD VAT applies to all VAT-registered businesses (VAT Notice 700/22) |
| VAT schemes: cash accounting, flat rate, annual accounting | Core | XR, FA, SA, QBO | |
| Domestic reverse charge (construction, specified goods) and postponed import VAT | Core | XR, SA, FA, QBO | |
| VAT bad-debt relief and fuel scale charges | Core | SA, XR (partial), FA | |
| **Partial exemption** (standard method, de minimis tests, annual adjustment) | Advanced | Rare natively; mostly spreadsheets | Differentiator for care, charities, property, finance (care fees are largely VAT-exempt) |
| Capital Goods Scheme | Enterprise | Rare | |
| EU VAT: country returns, EC Sales List where still required, OSS/IOSS, intra-EU reverse charge, triangulation | Advanced (per country) | BC, NS, SI, OD (localisations), XR (limited) | ViDA changes from 2028–2030 |
| US sales and use tax via engine (Avalara, Vertex, Stripe Tax, TaxJar, Anrok), exemption certificates, nexus monitoring, use-tax accrual on bills | Core (US) | QBO (native automated sales tax plus Sales Tax agent 2026), XR (Avalara partner), SI/NS/BC (Avalara/Vertex connectors), ZB | Section 2.3 |
| Withholding tax (US backup withholding; 1042-S; EU WHT on services and royalties) | Advanced | NS, SI, BC, OD | |
| 1099-NEC/MISC with $2,000 threshold (payments from 2026), TIN matching, IRIS A2A e-file, state filing | Core (US) | QBO, SI, NS, BC, ZB, XR (partner) | |
| CIS (UK construction) | Core (UK) | XR (add-on), SA, S50, FA, QBO UK | |
| UK corporation tax: CT600 with iXBRL computations; capital allowances | Advanced | XR (Xero Tax; small-business add-on £120 a year), FA, practice software (IRIS, TaxCalc, BTCSoftware) | HMRC's free joint filing service has closed |
| US federal and state income tax returns | Out of scope | Not in accounting ledgers; export a trial balance to tax-prep software | |
| Tax content as versioned, sourced data (rates, thresholds, box mappings) | Core | Platform principle | Mirrors the `packages/rules` approach in Thapsus-HR |

### 1.14 Period close

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Close checklist (templates, owners, dependencies, due dates, sign-off) | Advanced | NS (period close checklist and close management), SI (Close Analytics early adopter 2026 R1), CF, RL, DG; XR (month-end agent in XeroForce, early access) | |
| Balance-sheet account reconciliations (workpapers, auto-match sub-ledger to GL, thresholds, preparer and reviewer) | Advanced | NS (account reconciliation), CF, SI (partners such as FloQast and BlackLine) | |
| Accruals and prepayments automation (schedules, auto-amortisation, auto-reversal) | Advanced | XR (month-end agent drafts prepayment and amortisation journals), SI, NS (amortisation schedules), BC (deferrals), CF (automated accruals Jun 2026), RL | |
| Lock dates and close status per entity and module | Core | All (variously) | |
| Flux and variance analysis with explanation thresholds | Advanced | CF, SI (Close Analytics), NS | AI commentary candidate |
| Audit-ready close pack (evidence, sign-offs, reconciliations, journals) | Advanced | NS, CF | |

### 1.15 Financial reporting

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| P&L, balance sheet, cash-flow statement (indirect), trial balance, GL detail, aged AR/AP, VAT and sales-tax reports | Core | All | |
| Drill-down from every figure to source document | Core | All (depth varies) | |
| Custom report builder (rows, columns, formulas, dimensions, comparatives, budgets) | Advanced | XR (custom layouts; Syft analytics), QBA (custom report builder), SI (Financial Report Writer), NS (Financial Report Builder), BC (financial reporting definitions), ZB, OD | |
| Management packs (multi-page, commentary, charts, scheduled distribution) | Advanced | XR (Analytics Plus/Syft), SI, NS; partners (Fathom, Spotlight) | |
| Consolidated reports with entity columns and eliminations | Advanced | SI, NS, BC, IES, CF | |
| Dashboards and KPIs (configurable, per role) | Core | All | |
| Statutory layouts (Companies Act formats, FRS 102/105, IFRS incl. **IFRS 18 from 2027**, US GAAP) | Advanced | NS, SI, BC (localised), practice software | |
| Scheduled export to Excel, PDF, CSV, Google Sheets; live Excel connectors (OData, add-in) | Core (export), Advanced (live) | BC (OData, Excel add-in), QBA (Spreadsheet Sync), NS (SuiteAnalytics Connect), SI | |
| Natural-language Q&A over financial data | Advanced (AI, opt-in) | XR (JAX), QBO (Intuit Intelligence), SI (Finance Intelligence agent, early adopter), CF (Ember), RL | |

### 1.16 Audit trail and controls

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Change history per record (who, when, before and after) | Core | XR (history and notes), QBO (audit log), SI, NS (system notes), BC (change log), OD | |
| Append-only, hash-chained ledger and audit log (tamper-evident) | Core | Few SaaS ledgers expose it; Spain Verifactu and Portugal require chained records for invoices | Satisfies Thapsus append-only audit rule |
| Auditor role (read-only, time-boxed) and evidence export | Core | All | |
| Customer-exportable audit log | Core | NS, SI, BC | |
| Tax-audit data exports: GoBD data access (DE), FEC (FR), SAF-T (PT, PL JPK, NO, LT, RO D406, AT, LU FAIA, BG) | Advanced (per country) | BC, NS, SI, OD (localisations) | Section 2.2 |
| Segregation-of-duties rules and conflict reports | Enterprise | NS, SI, BC (partial) | SOX |
| User access reviews and SOX evidence (approvals, change logs) | Enterprise | NS, SI | Pair with a SOC 1 Type II report |

### 1.17 Statutory filing

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| UK accounts production: micro (FRS 105), small (FRS 102 s1A), filleted and abridged, with iXBRL tagging (FRC taxonomy) and Companies House software filing | Advanced | XR (Xero Tax for accountants; add-on for SMEs), FA (end-of-year filing), practice software | Mandatory software filing from April 2028; FRC publishes the 2027 taxonomy suite in Nov 2026 |
| HMRC CT600 with iXBRL accounts and computations | Advanced | XR, FA, practice software | |
| Companies House identity-linked filing (authentication codes, agent ACSP) | Advanced | Practice software | ECCTA identity verification (section 2.1) |
| US: Forms 1099/1096 via IRIS; state 1099 filing; sales-tax returns through engine | Core (US) | QBO, SI, NS, ZB | |
| EU: annual-accounts publication (e.g. DE Unternehmensregister, E-Bilanz XBRL to German tax office), via partners such as DATEV | Enterprise / partner | BC/NS/SI via localisation partners | |

### 1.18 Project and job costing

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Projects with budgets, time, expenses, supplier costs, billing (time and materials, fixed, milestones), profitability | Advanced | XR (Projects on Ultimate), QBO (Plus), QBA/IES (advanced project financials, construction), SI, NS, BC (Jobs), ZB, FA, OD | |
| WIP and percentage-of-completion revenue | Enterprise | BC (WIP methods), NS, SI, IES | |
| Construction pack: retentions, applications for payment, CIS, AIA G702/G703 (US) | Advanced (vertical) | IES, SI (Construction), QBA (construction financials) | |
| Timesheets | Advanced | XR, QBO, SI, NS, OD | Thapsus-HR time and attendance can feed hours |

### 1.19 Light inventory (for customers without KASONGO)

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Tracked items, on-hand quantity, weighted-average or FIFO cost, COGS on sale, adjustments, reorder points | Advanced | XR (tracked inventory, average cost), QBO (Plus, FIFO), ZB (Professional and above), FA, BC, NS, OD | |
| Multi-location, lots and serials, landed cost, assemblies | Out of scope (KASONGO) | BC, NS, OD, ZB (Elite) | Hand off to KASONGO |

### 1.20 Payroll journal import

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Payroll journal import by API, CSV or SFTP with mapping (pay elements to GL, departments) | Core | All (via payroll integrations) | Thapsus-HR hands pay inputs to payroll engines; the payroll engine's posted results come back to MULLAH |
| Liabilities clearing (PAYE/NIC, pension, US federal and state withholdings) and net-pay payment runs | Advanced | XR, SA, QBO, BC, NS, SI | |
| Cost allocation by employee split (projects, cost centres, funders) from HR | Advanced | SI, NS | Care providers allocate staff cost to services and funders |

### 1.21 Accountant and bookkeeper practice features

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Free accountant user and invite | Core | All | |
| Multi-client dashboard (bank rec status, VAT due, overdue filings, exceptions) | Core (practice edition) | XR (Xero HQ), QBO (QuickBooks Online Accountant), SA (Sage for Accountants), FA (practice dashboard), ZB (Zoho Practice), DG | |
| Client templates (CoA, rules, report packs) pushed to many clients | Advanced | XR, QBOA, FA | |
| Practice management (jobs, deadlines, time, billing, workpapers) | Separate product / partner | XR (Xero Practice Manager, Workpapers), QBOA, Karbon, TaxDome, IRIS | Possibly a later Thapsus product; integrate first |
| Year-end accounts and tax for clients | Advanced | Section 1.17 | |
| Wholesale and partner billing | Commercial | XR, QBO, FA, DG (outcome-based firm pricing: client billable only when 95%+ of transactions are zero-touch) | |

### 1.22 Document inbox and e-invoicing

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Per-tenant inbound email address and upload; AI classification (bill, receipt, statement, remittance, contract) | Core | XR (Hubdoc), QBO, FA, ZB, SI, NS, BC, DG | |
| Structured e-invoice receipt and validation (Peppol BIS Billing 3.0, XRechnung, ZUGFeRD/Factur-X, KSeF FA(3), FatturaPA, UBL/CII) | Core where mandated | BC, OD, NS (partners) | Germany: all businesses must already be able to receive |
| E-invoice sending through a Peppol access point (partner or own) | Core in BE/DE/FR/PL; UK from 2029 | OD (own AP), BC (connectors), others via partners | |
| Country archiving rules (original format, immutable, retention period) | Core | Varies | GoBD: XML part suffices for e-invoices |
| Link documents to transactions; full-text search (PostgreSQL FTS plus pg_trgm) | Core | All | |

### 1.23 Spend controls (without cards)

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Purchase requests and approvals with budget check | Enterprise | NS, SI, BC | |
| Approval matrix by amount, dimension, category and supplier risk | Advanced | SI, NS, BC, ZB, QBA | |
| Supplier onboarding controls (sanctions screening, bank verification, dual approval of bank-detail changes) | Advanced | NS, SI (partners) | Strong anti-fraud value |
| Card issuing and card spend management | Out of scope | Integrate with Pleo, Spendesk, Ramp or Brex feeds | Consistent with Thapsus-HR exclusions |

### 1.24 Treasury basics

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Bank account register (entity, signatories, mandates, purpose) | Advanced | NS, SI, BC | |
| Cash positioning across entities and currencies | Advanced | NS (Cash 360), SI, CF | |
| Loan and debt schedules (amortisation, interest accruals, covenants tracking) | Advanced | NS, SI (partners) | |
| Intercompany loans with interest | Enterprise | NS, SI | |
| FX exposure report | Enterprise | NS | Hedging and TMS out of scope (Kyriba-class) |

### 1.25 Credit control

| Feature | Tier | Who has it | Notes |
| --- | --- | --- | --- |
| Aged-debt worklists, chase sequences (email, SMS, letters), promises to pay, disputes | Advanced | NS (Collections Management), BC, ZB; XR/QBO via Chaser, Upflow, Satago | |
| Statutory late-payment interest and compensation (UK, EU Late Payment Directive) | Advanced | Rare natively | Figures as rules data; UK reform pending (section 2.1) |
| Credit limits and risk indicators for business customers; bureau integration (Creditsafe, Experian, D&B) | Enterprise | NS, SI, BC (partners) | Avoid automated credit decisions about natural persons (EU AI Act high-risk; UK Art 22A) |

---

## 2. Regulatory and filing landscape (as of 7 November 2026)

### 2.1 United Kingdom

| Topic | What applies | Status and dates | Source |
| --- | --- | --- | --- |
| **MTD for VAT** | All VAT-registered businesses must keep digital records and file VAT returns via "functional compatible software" using HMRC's API; data transfers inside the electronic account must use **digital links** (no copy and paste); submissions must go through the API | **In force** (notice updated 1 Apr 2022) | [VAT Notice 700/22](https://www.gov.uk/government/publications/vat-notice-70022-making-tax-digital-for-vat/vat-notice-70022-making-tax-digital-for-vat) |
| MTD fraud-prevention headers | Software must collect and send defined header data with every MTD API call (VAT and ITSA); direction under reg. 22 of the 2019 ancillary-metadata regulations | **In force** | [gov.uk direction](https://gov.uk/government/publications/direction-under-regulation-22-of-the-delivery-of-tax-information-through-software-ancillary-metadata-regulations-2019-si-2019360) |
| **MTD for Income Tax** (sole traders and landlords) | Qualifying income (gross self-employment plus property) **> £50,000** (2024–25) → from **6 Apr 2026**; **> £30,000** (2025–26) → from **6 Apr 2027**; **> £20,000** (2026–27) → from **6 Apr 2028** | £50k and £30k stages: **in force / adopted**. £20k stage: **announced**; gov.uk says "the government is introducing legislation" (guidance updated 26 Mar 2026), though HMRC's SALF manual already lists £20,000 | [gov.uk eligibility](https://www.gov.uk/guidance/find-out-if-and-when-you-need-to-use-making-tax-digital-for-income-tax); [gov.uk news](https://www.gov.uk/government/news/sole-traders-and-landlords-earning-more-than-30000-urged-to-act-now) |
| HMRC software recognition (MTD) | Developer Hub: sandbox testing, **minimum functionality standards**, production approvals checklist, terms of use, fraud-prevention headers; listed via HMRC "Software Choices". HMRC is **no longer accepting production-credential requests for new 2026–27 ITSA quarterly-update products** (market window closed) | **In force** | [HMRC ITSA end-to-end service guide](https://developer.service.hmrc.gov.uk/guides/income-tax-mtd-end-to-end-service-guide/) |
| **UK e-invoicing** | From **1 Apr 2029** VAT-registered persons, including public bodies, must issue and receive VAT invoices as e-invoices (B2B, B2G). B2C out of scope. Cross-border not required. **Peppol** is the interoperability framework; HMRC becomes the UK Peppol Authority, which "will not receive copies of VAT invoices". Access points certified via OpenPeppol plus UK-specific requirements; software providers to complete any required accreditation by 1 Apr 2029. Milestones: technical consultation spring 2027; key requirements Oct 2027–Apr 2028 (including business-identifier decision); software-provider requirements spring 2028; readiness testing summer 2028; secondary legislation autumn 2028 to early 2029. Digital-exclusion exemption modelled on MTD VAT. Penalties based on existing VAT invoicing penalties | **Announced** (roadmap 28 Oct 2026); primary legislation "as part of Finance Bill 2026" | [Roadmap](https://www.gov.uk/government/publications/e-invoicing-implementation-roadmap/roadmap-for-e-invoicing-implementation); [consultation response](https://www.gov.uk/government/consultations/promoting-electronic-invoicing-across-uk-businesses-and-the-public-sector/outcome/promoting-electronic-invoicing-across-uk-businesses-and-the-public-sector-consultation-response); [digital reporting consultation](https://www.gov.uk/government/consultations/e-invoicing-digital-reporting/e-invoicing-digital-reporting-consultation-on-the-future-use-of-e-invoicing-digital-reporting) |
| E-invoicing digital reporting | Separate HMRC consultation on whether Peppol could give government access to selected invoice data; "No decisions have been taken" | **Consulted / exploratory** | Same as above |
| **Companies House accounts reforms (ECCTA 2023)** | From **April 2028**: all companies file accounts in **iXBRL via commercial software**; web and paper accounts filing closed; strengthened audit-exemption statement; component parts filed together; limit on shortening accounting reference periods. **Paused** (23 Oct 2026): mandatory P&L filing for small and micro companies. **Retained**: abridged and filleted accounts | **Adopted, applies from April 2028** (moved from April 2027 on 9 Jun 2026; P&L element paused 23 Oct 2026) | [gov.uk 23 Oct 2026](https://www.gov.uk/government/news/companies-house-update-to-accounts-filing-from-april-2028); [withdrawn 9 Jun 2026 notice](https://www.gov.uk/government/news/companies-house-to-bring-in-changes-to-accounts-filing-from-april-2028) |
| Companies House identity verification | Directors and PSCs must verify identity; filings by agents through Authorised Corporate Service Providers (ACSP) | **In force** (mandatory from 18 Nov 2025, with transition for existing directors; date from memory, not re-fetched) | [Changes to UK company law](https://changestoukcompanylaw.campaign.gov.uk/changes-to-accounts/) |
| HMRC joint filing service (CATO) | "The service to file your accounts and Company Tax Return has closed"; users directed to approved software suppliers | **In force** (closure widely reported as 31 Mar 2026; gov.uk page shows closed, no date) | [gov.uk](https://www.gov.uk/file-your-company-accounts-and-tax-return) |
| **iXBRL taxonomies (FRC)** | 2026 FRC taxonomy suite published 18 Nov 2025 (FRS 102, FRS 101, IFRS, Charities); draft **2027 suite** published 27 Jul 2026 (adds FRS 103, IFRS 18, FRS 102 amendments; consultation closed 20 Sep 2026; final due Nov 2026) | **In force** (2026 suite); **draft** (2027) | [FRC 2026 suite](https://www.frc.org.uk/news-and-events/news/2025/11/frc-publishes-2026-uk-taxonomy-suite/); [FRC draft 2027](https://www.frc.org.uk/news-and-events/news/2026/07/draft-2027-taxonomy-suite-published/) |
| **FRS 102 Periodic Review 2024** | Effective for periods beginning on or after **1 Jan 2026**: Section 20 leases (lessee on-balance-sheet model based on IFRS 16), Section 23 revenue (five-step model like IFRS 15), other amendments; supplier-finance disclosures from 1 Jan 2025; transition paras 1.36–1.68; FRC Factsheet 9 | **In force** | [KPMG/EY/PKF summaries](https://www.pkfsmithcooper.com/news-insights/frs-102-changes-effective-from-1st-january-2026/); [EY PDF](https://www.ey.com/content/dam/ey-unified-site/ey-com/en-uk/technical/ifrs-technical-resources/documents/ey-key-changes-to-ifrs-102-arising-from-the-periodic-review.pdf) |
| **CIS** | From **6 Apr 2026**: certain payments to public bodies removed from CIS (SI 2026/289); **nil returns reinstated**; Finance Act 2026 lets HMRC cancel gross payment status "at any time" in fraud cases. Adding VAT to the GPS compliance test: **proposed only**. VAT domestic reverse charge for construction continues | **In force** (Apr 2026 items); VAT-test change **proposed** | [SI 2026/289](https://www.legislation.gov.uk/uksi/2026/289/made); [gov.uk CIS simplification](https://www.gov.uk/government/publications/simplifying-and-improving-construction-industry-scheme-administration/simplification-and-administrative-improvements-to-the-construction-industry-scheme); [Finance Act 2026](https://www.legislation.gov.uk/ukpga/2026/11/enacted) |
| **Record keeping** | Company tax records: **6 years** from end of the last company financial year they relate to (longer if enquiry or late return). Companies Act: private company accounting records 3 years, public company 6 years (tax rule usually governs). VAT records containing the VAT account: 6 years | **In force** | [gov.uk company records](https://www.gov.uk/running-a-limited-company/company-and-accounting-records); [HMRC CH14600](https://www.gov.uk/hmrc-internal-manuals/compliance-handbook/ch14600); [CH15400](https://www.gov.uk/hmrc-internal-manuals/compliance-handbook/ch15400) |
| **Late payment reform** | Bill to cap B2B payment terms at 60 days (falling to 45 after five years), make statutory interest (8% over base) mandatory and give the Small Business Commissioner investigation and fining powers | **Proposed** (bill in Parliament from May 2026; Royal Assent not confirmed) | [Mayer Brown](https://www.mayerbrown.com/en/insights/publications/2026/03/uk-government-response-to-late-payment-consultation); [ICAEW](https://www.icaew.com/insights/viewpoints-on-the-news/2026/may-2026/uk-government-ploughs-ahead-with-late-payments-bill); [SBC](https://www.smallbusinesscommissioner.gov.uk/new-powers-for-the-small-business-commissioner-as-part-of-government-package-to-tackle-late-payments/) |

**Build implications (UK):** MTD VAT recognition and fraud headers at launch. MTD ITSA only if MULLAH targets sole traders, and the next HMRC product window must be checked. Peppol send and receive before summer 2028 readiness testing. iXBRL accounts and CT600 before April 2028. FRS 102 lease and revenue schedules now. CIS with nil returns. Payment terms and interest rules as data that can change when the late-payment bill passes.

### 2.2 European Union

#### 2.2.1 VAT in the Digital Age (ViDA)

- **Legal acts:** Council Directive (EU) 2025/516, Council Regulation (EU) 2025/517 and Implementing Regulation (EU) 2025/518 were adopted on **11 Mar 2025**, published in the OJ on **25 Mar 2025** and in force from **14 Apr 2025** (one source says 12 Apr). **Adopted.** ([Wikipedia summary with OJ refs](https://en.wikipedia.org/wiki/VAT_in_the_Digital_Age); [Meijburg](https://www.meijburg.com/news/eu-proposal-vat-digital-age-package-formally-adopted))
- **Immediately:** Member States may impose domestic B2B e-invoicing without an EU derogation. **In force.**
- **1 Jul 2028:** platform deemed-supplier rules for short-term accommodation and passenger transport (Member States may defer to 1 Jan 2030) and single VAT registration elements. **Adopted, applies from 2028.**
- **1 Jul 2030:** **digital reporting requirements (DRR)** and mandatory e-invoicing (EN 16931 by default) for **intra-EU B2B** supplies, replacing recapitulative statements. Invoices must be issued within 10 days, and data reported in near real time. **Adopted, applies from 1 Jul 2030.**
- **1 Jan 2035:** existing national clearance and reporting systems must converge with the EU DRR standard. **Adopted.**
- Sources: [Sovos ViDA guide](https://sovos.com/guides/vat-in-the-digital-age/), [Accountancy Europe](https://accountancyeurope.eu/publications/vat-in-the-digital-age-vida/), [AVASK timeline](https://avask.com/blog/vida-timeline/). The 10-day issue rule is from my prior knowledge of the directive text and was not re-fetched.

#### 2.2.2 National e-invoicing and e-reporting mandates

| Country | Regime | Dates and status | Source |
| --- | --- | --- | --- |
| **Belgium** | Peppol B2B e-invoicing (BIS Billing 3.0); Act of 6 Feb 2024 | **In force from 1 Jan 2026**; tolerance (good-faith) to 31 Mar 2026; fines €1,500 / €3,000 / €5,000 per repeat. **Near-real-time e-reporting from 1 Jan 2028**: pre-draft approved by Council of Ministers 18 Jul 2026 (five-corner model with FPS Finance); **draft**, not enacted at 1 Nov 2026 | [EY 31 Jul 2026](https://taxnews.ey.com/news/2026-1652-belgium-announces-proposed-e-reporting-requirements-beginning-in-2028); [KPMG](https://kpmg.com/us/en/taxnewsflash/news/2026/07/tnf-belgium-proposal-to-introduce-e-reporting-of-invoicing-data-from-2028.html); [Vertex](https://www.vertexinc.com/resources/resource-library/belgiums-2026-e-invoicing-regulations-explained-scope-deadlines-and-penalties) |
| **Poland** | KSeF (central clearance; KSeF 2.0 API, FA(3) schema) | **In force**: from **1 Feb 2026** (2024 sales > PLN 200m), **1 Apr 2026** (all others); **1 Jan 2027** micro (monthly invoiced sales ≤ PLN 10,000). Penalties in law from **1 Jan 2027**; government bill UD477 (Sejm print 3245, sent 20 Oct 2026) would defer them to **1 Jan 2028** (**proposed**) | [KPMG PL](https://kpmg.com/pl/en/insights/taxes/tax-alert-introduction-of-ksef-confirmed.html); [Sovos](https://sovos.com/blog/vat/poland-e-invoicing-via-ksef/); [Crido on deferral](https://crido.pl/blog-taxes/ksef-bez-kar-do-2028-r/); [Bankier](https://www.bankier.pl/wiadomosc/Kary-za-bledy-w-KSeF-odroczone-o-kolejny-rok-Rzad-chce-przesunac-sankcje-na-2028-rok-9214634.html) |
| **France** | Decentralised model via **Plateformes Agréées (PA)**; PPF acts as directory and data concentrator; formats UBL, CII, Factur-X; e-reporting of B2C and international transactions and payment data | **In force from 1 Sep 2026**: all businesses must **receive**, and large companies and ETIs must **issue** and e-report. **1 Sep 2027**: SMEs and micro-enterprises issue and e-report (**adopted**). Tolerance: no sanctions for firms in difficulty to end of 2026 (administrative stance) | [economie.gouv.fr](https://www.economie.gouv.fr/tout-savoir-sur-la-facturation-electronique-pour-les-entreprises); [impots.gouv.fr guide](https://www.impots.gouv.fr/sites/default/files/media/1_metier/2_professionnel/EV/2_gestion/290_facturation_electronique/guide_pratique_facturation_electronique.pdf) |
| **Germany** | No clearance platform; EN 16931 formats (XRechnung, ZUGFeRD 2.0.1+ except MINIMUM and BASIC-WL; EDI if EN 16931-extractable); domestic B2B only | **In force**: all businesses must be able to **receive** since 1 Jan 2025. **1 Jan 2027**: issuing mandatory if prior-year turnover > €800,000 (**adopted**). **1 Jan 2028**: all businesses (**adopted**). Further BMF guidance reported dated 15 Oct 2025 (and 15 Oct 2026, **unverified**) | [Fiscal Requirements](https://www.fiscal-requirements.com/news/4566-germany-updated-bmf-guidance-on-2025-2028-b2b-e-invoicing-rollout); [Storecove](https://storecove.com/blog/en/e-invoicing-in-germany) |
| **Spain: Verifactu** | RD 1007/2023 invoicing-software requirements (chained, hashed, QR records; optional real-time send to AEAT); also SII for large taxpayers; TicketBAI (Basque Country) | **Adopted**: RDL 15/2025 (2 Dec 2025) set **1 Jan 2027** (corporate-tax payers) and **1 Jul 2027** (others). On **5 Oct 2026** Hacienda **announced** a delay to **Oct 2028** to align with B2B e-invoicing; **no norm published** at time of writing | [KPMG](https://kpmg.com/us/en/taxnewsflash/news/2025/12/tnf-spain-verifactu-invoicing-system-delayed-to-2027.html); [Economist & Jurist](https://www.economistjurist.es/noticias-juridicas/nuevo-aplazamiento-de-la-aplicacion-del-sistema-verifactu-hasta-2028/); [Supercontable](https://www.supercontable.com/boletin/H/articulos/Verifactu_se_aplaza_octubre_2028_incertidumbre_anuncio_sin_norma_con_elecciones.html) |
| **Spain: Crea y Crece B2B** | RD 238/2026 (25 Mar 2026; BOE 31 Mar 2026; BOE-A-2026-7295; in force 20 Apr 2026): mandatory B2B e-invoicing with a public repository; receivers must report invoice **status** (accepted, rejected, fully paid) | **Adopted**; obligation starts **12 months** (turnover > €8m) or **24 months** (others) after the technical **Ministerial Order**, not yet published (several sources). Reported dates: Oct 2027 and Oct 2028 (**unverified**; sources differ between 1 and 6 Oct) | [Garrido](https://garrido.es/real-decreto-238-2026-aprueba-el-desarrollo-reglamentario-de-la-factura-electronica/); [B2Brouter](https://www.b2brouter.net/es/plazos-obligatoriedad-factura-electronica-b2b/) |
| **Croatia** | Fiscalisation 2.0: B2B e-invoicing plus real-time e-reporting; Peppol-based exchange (FINA as AP) | **In force from 1 Jan 2026** (secondary sources only) | [Invoice Navigator](https://www.invoicenavigator.eu/deadlines); [Flick](https://www.flick.network/en/peppol-e-invoicing-countries) |
| **Greece** | myDATA plus certified e-invoicing providers | Large companies from early 2026 (secondary: 2 Feb 2026). Phase 2 (others) moved from 1 Oct 2026 to **2 Nov 2026** (decision A.1197/2026), transition to **31 Jan 2027**; from **1 Feb 2027** only certified providers or AADE tools (**adopted**, per secondary sources) | [Comarch](https://www.comarch.com/trade-and-services/data-management/legal-regulation-changes/greece-postpones-e-invoicing-for-smaller-businesses-and-phase-b-of-digital-movement-documents/); [Invoice-Portal](https://invoice-portal.de/blog/greece-postpones-phase-b/) |
| **Italy** | SDI clearance; FatturaPA XML; B2B and B2C | **In force since 1 Jan 2019**; EU authorisation extended to **31 Dec 2027** (Council Decision (EU) 2024/3150) | [EC eInvoicing in Italy](https://ec.europa.eu/digital-building-blocks/sites/spaces/DIGITAL/pages/467108890/eInvoicing+in+Italy) |
| **Romania** | RO e-Factura (Law 296/2023) | **In force**: B2B since **1 Jan 2024**; B2C reporting since **1 Jan 2025** (5-day deadline, EGO 138/2024); SAF-T D406 | [EC 2025 Romania sheet](https://ec.europa.eu/digital-building-blocks/sites/spaces/einvoicingCFS/pages/881983595/2025+Romania+2025+eInvoicing+Country+Sheet) |
| **Hungary** | Real-time invoice reporting (NAV Online Számla, XML 3.0) | **In force since Jul 2018** | [Invoice Navigator](https://www.invoicenavigator.eu/deadlines) |
| **Portugal** | AT-certified invoicing software, ATCUD code, QR code, monthly SAF-T (PT) billing; accounting SAF-T for FY2027 filed 2028 (secondary); qualified e-signature on PDF invoices from 1 Jan 2027 (Lei 73-A/2025 per secondary; a further postponement was reported) | Software certification **in force**; QES date **unverified** | [Zone & Co](https://www.zoneandco.com/articles/e-invoicing-portugal-compliance-guide); [Thomson Reuters PT](https://europe.thomsonreuters.com/compliance/regulatory-updates/portugal) |
| **Slovakia** | B2B e-invoicing via Peppol access points plus near-real-time reporting (five-corner) | **1 Jan 2027** (secondary; **unverified** officially) | [Invoice Navigator](https://www.invoicenavigator.eu/deadlines) |
| **Latvia** | B2B e-invoicing and reporting to VID | **1 Jan 2028** (moved from 2026; secondary) | Same |
| **Slovenia** | B2B e-invoicing (Peppol or e-invoice providers) | **1 Jan 2028** (secondary) | Same |
| **Ireland** | Phased: large corporates real-time reporting **Nov 2028**; all VAT-registered B2B **Nov 2029**; ViDA cross-border **Jul 2030** | **Announced** (secondary) | Same |
| **Denmark** | Bookkeeping Act: businesses must use **registered digital bookkeeping systems**; providers must register with Erhvervsstyrelsen and meet requirements (backup, e-invoicing via NemHandel/Peppol). New provider requirements (e-invoicing registration, ID verification) reported postponed to **1 Mar 2027** and **Jan 2028** (Executive Order 811/2026) | Core law **in force**; new requirements **adopted, applies 2027/2028** (secondary) | [Grant Thornton DK](https://www.grantthornton.dk/en/insights/articles/new-bookkeeping-act/); [Fiscal Requirements](https://www.fiscal-requirements.com/news/6054-denmark-postpones-new-digital-bookkeeping-system-requirements-to-march-2027-and-january-2028) |

**Software-provider certifications in this list:** Portugal (AT certification), Spain (Verifactu responsible declaration), Denmark (system registration), Hungary (online invoice reporting connection), France (connect to a PA, or become one: a heavy registration and ISO 27001-type process, **unverified** detail), Poland (KSeF API; no certification), Italy (SDI channel accreditation or an intermediary), Greece (certified provider or use one).

#### 2.2.3 Peppol: becoming a service provider (access point / SMP)

- **Steps** (OpenPeppol and authority guides): join **OpenPeppol** as a Service Provider member → sign the **Peppol Service Provider Agreement** with your home **Peppol Authority** (agreement v4.0.2 mandatory from 28 May 2025) → obtain **test PKI certificate** via the Peppol Service Desk → pass the **Peppol Testbed** (AS4 mandatory) → receive the **production PKI certificate** → meet **Peppol Authority Specific Requirements** (e.g. the Netherlands requires **ISO 27001** or an independent IT-audit memorandum; New Zealand needs a security questionnaire). Accreditation with the home authority comes first; other authorities after. ([Testbed](https://peppol.org/tools-support/testbed/); [Onboarding v1.4](https://peppol.org/wp-content/uploads/2024/03/Peppol_TestbedAndOnboarding_v1.4.pdf); [PASR index](https://peppol.org/documentation/governance-documentation/peppol-authority-specific-requirements/); [NL PASR](https://peppol.org/wp-content/uploads/2022/08/The-Netherlands-Peppol-Authority-Specific-Requirements.pdf); [ATO process](https://softwaredevelopers.ato.gov.au/e-invoicing-accreditation-process))
- **Fees** (2025 schedule): AP plus SMP, S2 (11–50 staff): €2,000 sign-up, €5,050 a year once certified; AP-only S1–S2: €1,025 sign-up, €2,800 a year certified. ([peppol.org fees 2025](https://peppol.org/join/fees-2025/))
- **Specifications:** **Peppol BIS Billing 3.0** (EN 16931 CIUS in UBL 2.1/CII) is the European baseline. **PINT** (Peppol International Invoice model) is the newer cross-jurisdiction model used by Singapore, Japan, Australia/NZ, Malaysia and the UAE. Whether an EU PINT replaces BIS 3.0 is **unverified**.
- **Peppol reporting:** service providers must submit end-user statistics and transaction-statistics reports to OpenPeppol. ([Peppol Reporting SP guideline](https://docs.peppol.eu/edelivery/guidelines/reporting/2023-12-01%20Peppol%20Reporting%20-%20SP%20Operational%20Guideline%20v1.0.pdf))
- **Recommendation:** launch through a certified AP partner per region, listed in SUBPROCESSORS.md with location. Decide by mid-2027 whether to become a certified AP and SMP: the UK Peppol Authority (HMRC) requirements land in spring 2028, and the EU mandates keep widening.

#### 2.2.4 EN 16931

- EN 16931-1:2017 (+A1:2019) defines the semantic data model of the core e-invoice. CEN/TS 16931-3 defines the syntax bindings (UBL 2.1 and UN/CEFACT CII). It is mandatory for B2G under Directive 2014/55/EU and is the default for ViDA DRR. A revision for ViDA is reported to be in progress at CEN (**unverified** timing).
- Build the internal invoice model as an EN 16931 superset. Render XRechnung, Peppol BIS, Factur-X/ZUGFeRD, FatturaPA and KSeF FA(3) from it.

#### 2.2.5 SAF-T and audit files

| Country | File | Mode | Status |
| --- | --- | --- | --- |
| Portugal | SAF-T (PT): billing monthly; accounting SAF-T for FY2027 filed in 2028 (secondary; may slip) | Periodic | In force (billing) |
| Poland | JPK_V7M/K (VAT, monthly), JPK_CIT (accounting books; phased from 2025 for largest taxpayers, **unverified** details), other JPK on demand | Periodic plus on demand | In force |
| Norway (EEA, not EU) | SAF-T Financial | On demand | In force |
| Lithuania | i.SAF (invoice registers, monthly), SAF-T on request | Periodic plus on demand | In force |
| Romania | D406 SAF-T (monthly or quarterly; phased by size) | Periodic | In force |
| Austria | SAF-T (AT) | On demand | In force |
| Luxembourg | FAIA | On demand | In force |
| Bulgaria | SAF-T phased from 2026 or 2027 by size (sources conflict) | Periodic | Adopted (dates **unverified**) |
| Denmark | SAF-T-based export for registered bookkeeping systems | On demand | In force (via Bookkeeping Act) |

Sources: [VATit](https://vatit.com/blog/saf-t-reporting-vat-compliance/); [vatcalc SAF-T list](https://www.vatcalc.com/global/which-countries-have-introduced-oecds-saf-t-2/); [Sovos Bulgaria](https://sovos.com/vat/tax-rules/bulgaria-saf-t/).

#### 2.2.6 Germany: GoBD and retention

- **GoBD** (BMF letter as amended **14 Jul 2025**, applicable from that date): for e-invoices it is sufficient to archive **only the structured part**, unless the PDF carries additional tax-relevant information. "Datenträgerüberlassung" becomes "Datenüberlassung", and the tax authority may choose machine evaluation or read-only access. Core principles stay: immutability, traceability, completeness, timeliness (non-cash entries promptly), a **Verfahrensdokumentation** (process documentation) and data access for tax audits. **In force.** ([KPMG DE](https://kpmg.com/de/de/themen/2025/07/bmf-gobd-zweite-aenderung.html); [IWW](https://www.iww.de/bbp/bilanzierung/bmf-schreiben-zweite-aenderung-der-gobd-aufgrund-gesetzlicher-anpassungen-n168278); [ZDH](https://www.zdh.de/ueber-uns/fachbereich-steuern-und-finanzen/abgabenordnung/aktuelles/veroeffentlichung-aktualisierter-gobd/))
- **BEG IV:** retention of **Buchungsbelege** (accounting vouchers, including invoices) cut from **10 to 8 years** (§147(3) AO, §257(4) HGB), applying to documents whose period had not expired on the day after promulgation, effectively from **1 Jan 2025**. **Banks, insurers and investment firms keep 10 years** (EGAO transitional rule). Books, inventories, annual financial statements and opening balance sheets remain **10 years** (from prior knowledge; not re-fetched). **In force.** ([Haufe](https://www.haufe.de/steuern/gesetzgebung-politik/viertes-buerokratieentlastungsgesetz_168_613390.html); [EY DE](https://www.ey.com/de_de/technical/steuernachrichten/laengere-aufbewahrungsfristen-bei-banken-versicherungen-und-wertpapierinstituten); [IHK Karlsruhe](https://www.ihk.de/karlsruhe/fachthemen/steuerrecht/aktuelles/neuer-inhalt4-buerokratieentlastungsgesetz-6347138))
- **Practical:** German customers expect a **DATEV export** (Buchungsstapel format) for their Steuerberater and **SKR03/SKR04** charts. Optional IDW PS 880 software certification is a sales asset.

#### 2.2.7 France: FEC

- Companies with computerised accounts must provide the **Fichier des Écritures Comptables** (FEC), a flat file of all entries with 18 mandatory fields, on request in a tax audit (Livre des procédures fiscales art. L47 A; format in art. A47 A-1). **In force** (from prior knowledge; the official page was not fetched in this session).
- Combined with the 2026–27 e-invoicing reform, MULLAH needs PA connectivity, Factur-X/UBL/CII, e-reporting of B2C and international sales plus payment data (for services, VAT on receipts), and the FEC export.

### 2.3 United States

| Topic | What applies | Status | Source |
| --- | --- | --- | --- |
| **Sales tax economic nexus** (post-*South Dakota v. Wayfair*, 2018) | Every sales-tax state has an economic-nexus rule, typically **$100,000** in sales, sometimes with a **200-transaction** alternative. The trend is to **drop the transaction test**: Alaska (ARSSTC, 1 Jan 2025), Utah (1 Jul 2025), **Illinois (1 Jan 2026)**, **Kentucky (1 Aug 2026)**, plus Wyoming, Indiana, North Carolina and others. Avalara counted 16 states without a transaction test at 1 Jan 2026. New Jersey bills (S711/A3419) to drop it are **proposed** | **In force** (state by state) | [Avalara](https://www.avalara.com/us/en/blog/states-eliminating-economic-nexus-transaction-thresholds.html); [Taxually](https://www.taxually.com/blog/economic-nexus-by-state) |
| **Tax engines** | **Avalara AvaTax** (REST v2; enterprise default; exemption-certificate management, returns), **Vertex** (O Series; enterprise, strong international), **Stripe Tax** (0.5% per transaction no-code or $0.50 per API call in registered jurisdictions, per secondary source), **TaxJar** (Stripe-owned, US-focused API), **Anrok** (SaaS-focused, sales tax plus VAT/GST, ~US$5k a year starting per secondary source). Zamp and Numeral are newer managed services | Market | [Zamp comparison](https://zamp.com/blog/anrok-vs-stripe-tax-vs-taxjar-api); [Numeral](https://www.numeral.com/blog/taxjar-vs-vertex); [switchlabs guide](https://www.switchlabs.dev/post/sales-tax-software-complete-guide-2026) |
| **1099-NEC / 1099-MISC threshold** | For **payments made after 31 Dec 2025**, report when a recipient's payments total **$2,000 or more** (was $600); may be inflation-adjusted from 2027 | **In force** (OBBBA, 2025) | [IRS i1099mec (12/2026)](https://www.irs.gov/instructions/i1099mec); [IRS OBBB news](https://www.irs.gov/newsroom/one-big-beautiful-bill-news) |
| **1099-K** | Third-party settlement organisations report only when gross payments **> $20,000 and > 200 transactions**; OBBBA **retroactively reinstated** the pre-ARPA threshold. Proposed regulations (8 Jan 2026) align backup withholding. States may set lower thresholds | **In force**; backup-withholding regs **proposed** | [IRS 1099-K FAQ](https://www.irs.gov/newsroom/form-1099-k-faqs-third-party-filers-of-form-1099-k); [IRS proposed regs](https://www.irs.gov/newsroom/treasury-irs-issue-proposed-regulations-reflecting-changes-from-the-one-big-beautiful-bill-to-the-threshold-for-backup-withholding-on-certain-payments-made-through-third-parties) |
| **E-file threshold** | Must e-file if filing **10 or more** information returns in aggregate (all types, including W-2) for returns due on or after 1 Jan 2024 (T.D. 9972); hardship waiver via Form 8508 | **In force** | [IRS e-file information returns](https://www.irs.gov/filing/e-file-information-returns); [IRS i1099gi](https://www.irs.gov/instructions/i1099gi) |
| **IRIS replaces FIRE** | Last FIRE test filing 1 Nov 2026; last TCC change 9 Nov 2026; **last FIRE filing 19 Nov 2026, 3 p.m. ET**; from 2027 all FIRE forms go through **IRIS**: Taxpayer Portal (up to 100 returns at a time) or **Application-to-Application (A2A)** for software. Needs an IRIS TCC; A2A also needs an API client ID and ATS testing (secondary sources) | **In force** (IR-2026-99, 24 Aug 2026) | [IRS IR-2026-99](https://www.irs.gov/newsroom/irs-reminder-information-return-e-file-system-transitioning-to-a-new-platform); [Pub 1220 (5-2026)](https://www.irs.gov/pub/irs-pdf/p1220.pdf) |
| **US e-invoicing** | No federal mandate. **DBNAlliance** (spun out of the Fed-convened Business Payments Coalition, 2023) runs a Peppol-like **four-corner** exchange framework based on **UBL 2.x**, joined through certified service providers | **Voluntary** | [FRB Services](https://www.frbservices.org/news/fed360/issues/060123/b2b-payments-experts-launch-digital-business-networks-alliance); [Storecove](https://www.storecove.com/blog/en/digital-business-network-alliance) |
| **ASC 606 / ASC 842** | Revenue from contracts and lease accounting apply to public and private entities (private-company ASC 842 since fiscal years beginning after 15 Dec 2021) | **In force** (prior knowledge) | FASB codification (not fetched) |
| Recent FASB items relevant to a ledger | ASU 2024-03 (expense disaggregation disclosures; public entities, annual periods beginning after 15 Dec 2026); ASU 2025-05 (credit-loss practical expedient for current receivables and contract assets; annual periods beginning after 15 Dec 2025) | **Adopted** (from prior knowledge; **unverified** in this session) | FASB (not fetched) |
| **SOX** | Public-company customers need ITGCs over MULLAH (access, change management, operations), segregation of duties, audit trails and evidence of review; auditors will ask for a **SOC 1 Type II** (SSAE 18) report on MULLAH plus complementary user-entity controls; AI-prepared journals need approver evidence | **In force** (no 2026 change identified) | Prior knowledge |
| **IRS record retention** | Generally 3 years after filing; 6 years if income under-reported by >25%; 7 years for bad-debt or worthless-securities claims; employment tax records 4 years; indefinitely if no return or fraudulent return | **In force** (prior knowledge; IRS "How long should I keep records" page not fetched) | IRS Pub 583 (not fetched) |
| State privacy and ADMT | CCPA/CPRA regulations on automated decision-making technology and risk assessments (finalised 2025, phased compliance) and the Colorado AI Act (effective date pushed to 2026) affect AI on consumer or employee data | **Unverified** in this session | n/a |

### 2.4 Banking, open banking and payments

#### 2.4.1 Open banking and data access

| Jurisdiction | Status (7 Nov 2026) | Source |
| --- | --- | --- |
| **UK open banking** | PSD2-derived AIS and PIS **in force** (CMA9 standards). **Commercial VRP**: UK Payments Initiative (UKPI) **Wave 1 live 2 Jun 2026** (utilities, telecoms, rail, regulated financial services, e-money, government, charities; ~75% of UK current accounts reachable; fixed pence-per-transaction fee charged to the payment provider). **Wave 2 (general e-commerce) expected H2 2026**. Sweeping VRP in force since 2022. FCA to consult on long-term regulatory framework by end-2026; a "Future Entity" replaces Open Banking Ltd as standards body | [Open Banking Tracker cVRP](https://www.openbankingtracker.com/guides/commercial-vrp-ukpi); [A&O Shearman](https://www.aoshearman.com/en/insights/financial-services-horizon-report-2026/payment-services-and-payment-systems); [Modulr](https://www.modulrfinance.com/blog-insights/commercial-vrp-uk-payments) |
| **EU PSD2 → PSD3 / PSR** | PSD2 **in force**. **Provisional agreement 27 Nov 2025**; COREPER endorsed 22 Apr 2026; Council published final texts (8221/26, 8222/26) April 2026; ECON vote 5 May 2026. **I could not confirm plenary adoption or OJ publication.** PSR applies about **18–21 months after entry into force** (VoP-related provisions later), so expect **late 2027 to 2028**. Changes include stronger fraud liability (impersonation scams), IBAN–name checks for all credit transfers, open-banking dashboards and permission management, and stricter API performance | [Arthur Cox](https://www.arthurcox.com/insights/psd3-and-psr-final-compromise-texts-published/); [MoFo](https://www.mofo.com/resources/insights/260430-psd3-and-the-payment-services-regulation-key-developments); [Clearing Post](https://clearingpost.com/insights/psd3-psr-econ-committee-clearance-plenary-slips-june-2026/) |
| **EU FIDA** (open finance) | Council mandate Dec 2024; trilogues stalled. MLex (3 Nov 2026) reports a draft 2027 Commission work programme plan to **withdraw** it within six months. **Proposed, likely withdrawn** | [MLex](https://www.mlex.com/mlex/antitrust/articles/2545816); [Thede Consulting](https://thede-consulting.com/en/payment-en/fida-back-in-motion-from-ambitious-to-workable/) |
| **US CFPB §1033** | Final rule Oct 2024. **Enjoined** by E.D. Kentucky (29 Oct 2025) pending reconsideration; ANPR Aug 2025; new **NPRM sent to OIRA 6 Aug 2026**; Sixth Circuit appeal in abeyance (status reports every 60 days). Compliance dates effectively suspended. **Adopted but enjoined; under reconsideration** | [Cozen O'Connor](https://www.cozen.com/news-resources/publications/2026/section-1033-compliance-date-open-banking-rule-enjoined-and-under-reconsideration); [Moore & Van Allen](https://www.mvalaw.com/data-points/cfpb-enjoined-from-enforcing-personal-financial-data-rights-rule-1033); [CRS](https://www.congress.gov/crs-product/IF13117) |

#### 2.4.2 Bank-feed aggregators (choose per cell for residency)

| Provider | Strength | Coverage (vendor or secondary claims) | Fit for MULLAH cells |
| --- | --- | --- | --- |
| **Plaid** | US leader; EEA via Plaid Financial Ltd | US/Canada deep; UK/EU present | US cell primary |
| **MX** | US data aggregation and enrichment | US | US cell alternative |
| **Finicity** (Mastercard Open Finance) | US bank-grade, lending data | US | US cell alternative |
| **TrueLayer** | UK-origin; payments plus data; strong DACH/Benelux | UK plus ~19 EU countries | UK and EU cells |
| **Yapily** | Pure infrastructure API | ~2,000 banks, 19 countries (vendor) | UK and EU cells |
| **Tink** (Visa) | Broad European coverage | ~6,000 banks, 18 countries (secondary) | EU cell |
| **GoCardless Bank Account Data** (ex-Nordigen) | Was free | **New sign-ups disabled** (third-party reports) | Do not plan on it |

Sources: [Freenance](https://freenance.io/fintech/psd2-aisp-licensed-providers-eu-2026-list-tink-truelayer-yapily-salt-edge-plaid-finicity-comparison/); [Yapily](https://www.yapily.com/blog/tink-alternatives-6-open-banking-platforms-compared/); [Firefly III docs on GoCardless](https://docs.firefly-iii.org/how-to/data-importer/import/gocardless/). Treat bank counts as marketing claims. Each aggregator processes personal data (sole traders, payee names), so each must be in SUBPROCESSORS.md with region **before** integration. Also, direct bank feeds (e.g. Xero's direct feeds) are partnership-driven and take time to secure.

#### 2.4.3 Payment rails

| Rail | Key facts for an accounting product | Status | Source |
| --- | --- | --- | --- |
| UK **Faster Payments** | Real-time; bulk files via bank portals or PISP partner | In force | Prior knowledge |
| UK **Bacs Direct Credit and Direct Debit** | 3-day cycle; Standard 18 files; DD needs a Service User Number (direct or bureau) or a DD provider (GoCardless etc.) | In force | Prior knowledge |
| UK **Confirmation of Payee** | Name check on payee account; widened by PSR direction to ~400 more PSPs (2024) | In force | [Finextra](https://finextra.com/pressarticle/102475/psr-confirms-85k-reimbursement-limit-for-app-fraud-victims) |
| UK **APP fraud reimbursement** | Mandatory reimbursement since **7 Oct 2024**, cap **£85,000** per claim (also CHAPS); independent review running in 2026; mostly protects consumers, micro-enterprises and charities | In force | [Hogan Lovells](https://www.hlc.com/en/publications/app-fraud-uk-psr-confirms-reduced-maximum-level-of-reimbursement) |
| EU **SEPA SCT, SDD Core/B2B, SCT Inst** | Instant Payments Regulation (EU) 2024/886: euro-area PSPs **receive** instant by 9 Jan 2025, **send plus Verification of Payee** by **9 Oct 2025**; non-euro PSPs receive 9 Jan 2027, send and VoP 9 Jul 2027; e-money and payment institutions by 9 Apr 2027 (euro area) | In force / adopted | [ECB IPR page](https://www.ecb.europa.eu/paym/integration/retail/instant_payments/html/instant_payments_regulation.ro.html); [Bird & Bird](https://twobirds.com/en/insights/2024/global/instant-payments-regulation); [Osborne Clarke](https://osborneclarke.com/node/25752) |
| US **ACH** (Nacha) | **Fraud-monitoring rules**: phase 1 from **20 Mar 2026** (ODFIs; originators and third parties with >6m 2023 entries), phase 2 from **19 Jun 2026** (practically 22 Jun) for all remaining non-consumer originators, TPSPs, TPSs and RDFIs. Businesses originating ACH (payment runs) need risk-based processes to spot false-pretences payments | In force | [Nacha phase 1](https://www.nacha.org/rules/risk-management-topics-fraud-monitoring-phase-1); [Nacha phase 2](https://www.nacha.org/rules/risk-management-topics-fraud-monitoring-phase-2) |
| US **RTP and FedNow** | FedNow network limit raised from $1m to **$10m** (Nov 2025), matching RTP; banks may set lower limits | In force | [FRB Services](https://frbservices.org/news/press-releases/090525-fednow-transaction-limit-increase) |
| **ISO 20022** | Corporate formats: **pain.001** (credit transfer initiation), **pain.008** (direct debit), **pain.002** (status), **camt.053** (statement), **camt.054** (notification). SWIFT **CBPR+**: MT/MX coexistence for payment instructions ended **Nov 2025**; **unstructured postal addresses removed after 14 Nov 2026** (hybrid or structured only; a SWIFT Aug 2026 notice about extending structured-address migration is **unverified**); MT101 coexistence ends 14 Nov 2026 for FIs | In force / imminent | [SWIFT ISO 20022 journey](https://www.swift.com/standards/iso-20022/iso-20022-bytes/journey-continues); [SWIFT CBPR+ roadmap](https://www.swift.com/sites/default/files/files/cbpr-phase-2-roadmap_detailed_v4-2.pdf); [J.P. Morgan](https://www.jpmorgan.com/insights/payments/cross-border-payments/iso-20022-migration) |
| **PCI DSS v4.0.1** | All future-dated requirements effective **31 Mar 2025**. SAQ A revised: requirements 6.4.3 and 11.6.1 removed, replaced by an eligibility criterion that the merchant's site is not susceptible to script attacks. MULLAH should **never handle PANs**: use processor-hosted fields and payment pages (Stripe, Adyen, GoCardless) to keep SAQ A scope | In force | [PCI SSC blog](https://blog.pcisecuritystandards.org/important-updates-announced-for-merchants-validating-to-self-assessment-questionnaire-a); [Adyen](https://docs.adyen.com/development-resources/pci-dss-compliance-guide/saq-a-eligibility) |

### 2.5 Accounting standards that change the product

| Standard | Effect on MULLAH | Effective | Status | Source |
| --- | --- | --- | --- | --- |
| FRS 102 (Periodic Review 2024) | Lessee ROU assets and liabilities (s20); five-step revenue (s23); supplier-finance disclosures | Periods beginning on or after 1 Jan 2026 | In force | Section 2.1 |
| FRS 105 (micro) | Revenue simplification aligned with FRS 102 changes; leases stay off balance sheet for micros (prior knowledge, **unverified**) | 1 Jan 2026 | In force (**unverified** detail) | n/a |
| IFRS 15 / IFRS 16 | Revenue and leases (IFRS reporters, UK listed groups, EU subsidiaries) | In force | In force | Prior knowledge |
| **IFRS 18** Presentation and Disclosure | New P&L categories (operating, investing, financing), required subtotals, **management-defined performance measures** disclosed, aggregation and disaggregation rules; report builder must support it | Annual periods beginning on or after **1 Jan 2027** | EU endorsed 13 Feb 2026 (BDO); UK endorsement reported Dec 2025 (**unverified**) | [BDO](https://www.bdo.global/en-gb/news/ifrs-news/the-european-union-endorses-ifrs-18-presentation-and-disclosure-in-financial-statements); [EFRAG status](https://www.efrag.org/en/financial-reporting/endorsement-status) |
| ASC 606 / ASC 842 | US revenue and leases | In force | In force | Prior knowledge |

### 2.6 Record-retention matrix (configure as data per jurisdiction pack)

| Jurisdiction | Default retention for accounting records | Notes |
| --- | --- | --- |
| UK | 6 years from end of the company financial year (tax); Companies Act 3 years private, 6 years public | Longer during enquiries; VAT 6 years |
| Germany | Books, statements, inventories: 10 years; **Buchungsbelege: 8 years** (from 2025); banks and insurers 10 years | GoBD immutability and machine-readable access |
| France | 10 years for accounting documents (Code de commerce L123-22); 6 years tax (LPF L102 B) | Prior knowledge, **unverified** in this session |
| Italy | 10 years (Civil Code art. 2220); e-invoice digital preservation ("conservazione") | Prior knowledge |
| Spain | 6 years (Código de Comercio art. 30); 4-year tax limitation | Prior knowledge |
| Poland | 5 years from end of the year in which tax became due | Prior knowledge |
| US | IRS 3/6/7 years, 4 years for employment tax; SOX auditors' workpapers 7 years (auditor duty) | Prior knowledge |

Retention should be a policy engine with legal hold. Deletion must not break hash chains, so use crypto-shredding of personal fields (as Thapsus Care does) while keeping the ledger skeleton.

### 2.7 Dated deadline timeline (drives build order)

| Date | Event | Status |
| --- | --- | --- |
| 1 Jan 2026 | Belgium Peppol B2B; Croatia Fiscalisation 2.0; FRS 102 amendments; Illinois drops 200-transaction test | In force |
| 1 Feb / 1 Apr 2026 | Poland KSeF (large / all others) | In force |
| 5 Feb 2026 | UK Arts 22A–22D (automated decisions) | In force (secondary) |
| 20 Mar / 22 Jun 2026 | Nacha fraud-monitoring phases 1 and 2 | In force |
| 31 Mar 2026 | HMRC joint accounts-and-CT600 filing service closed (date reported) | In force |
| 6 Apr 2026 | MTD ITSA (> £50k); CIS nil returns and public-body exemption | In force |
| 20 Apr 2026 | Spain RD 238/2026 in force (clock awaits Ministerial Order) | Adopted |
| 2 Jun 2026 | UK commercial VRP Wave 1 | Live |
| 2 Aug 2026 | EU AI Act Article 50 transparency duties | In force |
| 1 Sep 2026 | France: all receive; large and ETI issue and e-report | In force |
| 2 Nov 2026 | Greece phase 2 (transition to 31 Jan 2027) | In force (secondary) |
| 14 Nov 2026 | SWIFT CBPR+: unstructured addresses removed | Imminent (possible extension, unverified) |
| 19 Nov 2026 | Last FIRE filing; IRIS only thereafter | In force |
| 1 Jan 2027 | Germany issuing (> €800k); Poland micro; Poland penalties (or 2028 if bill passes); Spain Verifactu (corporate) unless delayed; IFRS 18; Slovakia (secondary); Portugal PDF QES (unverified) | Adopted / proposed changes |
| 9 Jan 2027 | IPR: non-euro PSPs receive SCT Inst | Adopted |
| 1 Feb 2027 | Greece hard cut-off (certified providers only) | Adopted (secondary) |
| Spring 2027 | UK e-invoicing technical standards and AP certification consultation | Planned |
| 6 Apr 2027 | MTD ITSA (> £30k) | Adopted |
| 1 Jul 2027 | Spain Verifactu (others) unless delayed | Adopted |
| 9 Jul 2027 | IPR: non-euro PSPs send plus VoP | Adopted |
| 1 Sep 2027 | France: SMEs and micro issue and e-report | Adopted |
| ~Oct 2027 | Spain B2B e-invoicing (> €8m), 12 months after Ministerial Order | Adopted; date unverified |
| 2 Dec 2027 | EU AI Act Annex III high-risk obligations (credit scoring of natural persons, worker management) | Adopted (Digital Omnibus, secondary) |
| Late 2027–2028 | EU PSR/PSD3 apply (estimate) | Agreed; publication unverified |
| 1 Jan 2028 | Germany all issue; Belgium e-reporting (draft); Latvia, Slovenia (secondary); Poland penalties (if deferred) | Adopted / draft |
| Apr 2028 | Companies House software-only iXBRL accounts; MTD ITSA (> £20k) from 6 Apr 2028 | Adopted / announced |
| Spring–summer 2028 | UK software-provider requirements; readiness testing | Planned |
| ~Oct 2028 | Spain B2B (others); Verifactu if delay enacted | Adopted / announced |
| Nov 2028 | Ireland large corporates (secondary) | Announced |
| **1 Apr 2029** | **UK mandatory e-invoicing (Peppol)** | Announced |
| Nov 2029 | Ireland all B2B (secondary) | Announced |
| **1 Jul 2030** | **ViDA intra-EU DRR and e-invoicing** | Adopted |
| 1 Jan 2035 | National systems converge with ViDA | Adopted |

---

## 3. AI features in accounting (2026)

### 3.1 What the leaders actually ship

| Vendor | Shipped or announced (2025–2026) | Control model | Source |
| --- | --- | --- | --- |
| **Xero (JAX)** | **Auto bank reconciliation** GA in 2026 (beta Nov 2025): matches high-confidence transactions in real time, explains each match, flags exceptions; Xero claims ~50% time saving. **XeroForce** natural-language agent builder (early access) with a **month-end agent** (reconciliation status review, prepayment and amortisation journals across clients). Stated goal: automate 90% of routine bookkeeping in 2026. Budgeting tool and benchmarking H2 2026. Anthropic Claude integration at an early stage | **Opt-in per bank account**; "Reconciled" page with full audit trail of what JAX matched and why | [Xero blog, Xerocon London 2026](https://blog.xero.com/product-updates/xerocon-london-2026-agentic-era-small-business-finance/); [Xero media release, Xerocon US](https://www.xero.com/media-releases/new-ai-innovations-xerocon-denver/); [Accounting Today](https://www.accountingtoday.com/news/xeros-jax-ai-gains-agentic-capacities) |
| **Intuit (Intuit Intelligence / Assist / agents)** | Agents since mid-2025: accounting (categorisation, reconciliation, clean-up), payments (reminders, late-payment prediction), finance (KPIs, forecasting, scenario planning), customer (leads), payroll, **sales tax agent** (2026). AI features tiered by plan (Plus: AI reconciliation, anomaly detection, scenario planning; Advanced: continuous books-quality checks). >3m customers used agents (Q2 FY26). **Agent Studio** pilot announced at Intuit Connect 2026 | Agents propose; users review; plan-tiered | [QuickBooks pricing page](https://quickbooks.intuit.com/pricing/); [CPA Practice Advisor](https://www.cpapracticeadvisor.com/?p=193101); [Intuit community](https://quickbooks.intuit.com/community/quickbooks-news-64/meet-your-dedicated-digital-teammates-intuit-ai-agents-164995) |
| **Sage (Copilot and agents)** | Sage Copilot across products; 28 Apr 2026 announcement of AI agents across finance, HR and operations (Intacct, HCM, X3), Intacct finance agents GA "later in 2026"; **Finance Intelligence agent** (natural-language Q&A, phased early adopter, 2026 R2); **Close Analytics** (early adopter, 2026 R1); budget variance analysis; AI Gateway and MCP server (2026 R2, per a third-party vendor, **unverified**) | Early-adopter gating; human-in-the-loop | [Sage Intacct release notes](https://www.intacct.com/ia/docs/en_AU/releasenotes/2026/2026_Release_2/Sage_AI_and_Automation/2026-R2-copilot-FIA-EA.htm); [Investegate (Sage RNS)](https://www.investegate.co.uk/announcement/gnw/sage-group--sge/sage-expands-ai-agents-across-finance-hr-and-/9542697) |
| **Oracle NetSuite** | **NetSuite Next** (agentic UX, announced SuiteWorld 2025); autonomous close and reconciliation agents; SuiteWorld 2026 (25–28 Oct): industry **SuiteAgents**, **SuiteSkills** library, AI-driven implementation tool. Bill Capture (AI document extraction) exists (prior knowledge) | Availability partly unclear; review steps | [Diginomica](https://diginomica.com/node/31365); [ERP Today](https://erp.today/2025-was-a-pivotal-point-for-oracle-netsuite-where-its-ai-roadmap-is-heading-in-2026); [Versich recap](https://versich.com/blog/netsuite-suiteworld-2026-recap/) |
| **Microsoft Business Central** | Copilot included in licences (bank reconciliation matching, marketing text, analysis assist, e-document mapping); **Payables Agent** (reads vendor invoices from email, matches vendor and G/L, prepares for approval), **Sales Order Agent** (email to quote to order on approval), **Expense Agent** (preview), **Agent Designer** (2026 wave 1). Agents consume **Copilot Credits** (pre-purchase or pay-as-you-go) | Human-in-the-loop by design; consumption billing | [Microsoft BC pricing](https://www.microsoft.com/en-us/dynamics-365/products/business-central/pricing); [Microsoft release plan](https://learn.microsoft.com/cs-cz/dynamics365/release-plan/2026wave1/smb/dynamics365-business-central/planned-features) |
| **Digits** | "Autonomous General Ledger" (Mar 2025); AI accounting agents (Jun 2025); outcome-based firm pricing (client billable only when ≥95% of transactions are zero-touch, Apr 2026) | Autonomy with accountant review | [Accounting Today](https://accountingtoday.com/news/digits-announces-autonomous-general-ledger); [UsagePricing](https://www.usagepricing.com/blueprint/activity/digits-2026-09-23-firm-pricing-partner-only) |
| **Rillet** | Real-time GL with agents inside the ledger; ASC 606 from contracts; Jul 2026 **audit trail snapshotting the exact ledger data each AI answer used**; $100m Series C at $1bn (Aug 2026), 600+ customers | Accountants review, not key | [The Next Web](https://thenextweb.com/news/rillet-100m-series-c-1bn-accounting-ai); [FF News](https://ffnews.com/news/rillet-secures-100m-series-c-at-1b-valuation-to-scale-ai-native-accounting-erp) |
| **Campfire** | **Ember** assistant (1.4 Mar 2026: charts, code-executed calculations, expense-accrual automation); **Accounting Intelligence** (May 2026: bank-feed auto-categorisation, duplicate detection); lease accounting, multi-book, Ember in Slack, automated accruals (Jun 2026); reusable **Ember Skills** for close (Jul 2026) | Assistant plus workflows | [Fintech Futures](https://www.fintechfutures.com/venture-capital-funding/erp-platform-campfire-fuels-up-with-65m-series-b); [WebProNews](https://www.webpronews.com/campfires-ai-erp-inferno-doubling-revenue-eyeing-ipo-wave) |

**Pattern:** every vendor ships **suggest → explain → human approves → audit trail**, with autonomy only behind per-feature opt-in, such as Xero's per-bank-account auto-reconcile. Pricing is moving to **consumption** (BC Copilot Credits) or **outcome** (Digits) models.

### 3.2 Concrete AI feature list for MULLAH (all off by default)

Common controls for every feature:

- Tenant-level master switch, then per-feature and per-entity toggles (default **off**).
- An **AI gateway** that routes to an in-region model endpoint. The model provider is listed in SUBPROCESSORS.md per region. No training on customer data without explicit opt-in. Prompts and outputs are logged with personal data minimised.
- Every AI output is a **draft or suggestion** with a confidence score and an explanation. Posting, sending, paying or filing needs a human holding the relevant permission, unless the tenant sets a rule-based auto-apply threshold, which is itself audited and reversible.
- AI-generated content is labelled as such (EU AI Act Art. 50 transparency for chatbots and AI-generated text).

| # | Feature | Human-approval design | Regulatory caution |
| --- | --- | --- | --- |
| 1 | Bank reconciliation match suggestions; optional auto-match above a tenant-set confidence | Per bank account opt-in (as Xero); "auto-matched" review queue; one-click undo; explanation shown | Low risk. SOX customers: treat auto-match as an automated control and document it |
| 2 | Transaction coding (account, tax code, dimensions, contact) | Suggest only; accepting creates or updates a bank rule only with confirmation | Wrong VAT codes create tax errors; show the tax impact before accepting |
| 3 | Bill, receipt and statement capture with field extraction | Draft bill with per-field confidence; low-confidence fields highlighted; structured e-invoice data always wins over OCR | Contains personal data (sole-trader suppliers); in-region processing |
| 4 | Duplicate detection (bills, payments, suppliers, customers) | Warn, with soft-block and override reason | Low |
| 5 | Supplier bank-detail change and payment fraud alerts | Hold the payment run line until a second approver confirms; CoP/VoP check | Strong value under APP-fraud and Nacha fraud-monitoring regimes |
| 6 | Anomaly detection (unusual amounts, timing, accounts, VAT patterns) | Alert inbox with reasons; no auto-correction | Low; avoid profiling individual employees without a DPIA |
| 7 | Cash application (remittance parsing, receipt-to-invoice matching) | Suggested allocations; batch approve | Low |
| 8 | Cash-flow forecasting (ML) | Shown as an estimate with drivers and ranges; no automated decisions | Low |
| 9 | Close assistant (checklist status, missing accruals and prepayments, unreconciled accounts) | **Agents prepare, never post**: draft journals into an approval queue with evidence | SOX: approver evidence stored |
| 10 | Flux and variance commentary drafting | Draft text with linked figures; editor must accept; never auto-published to a board pack | Art. 50 labelling if shown as AI-generated |
| 11 | Natural-language reporting and Q&A | Read-only; respects RLS and dimension security; shows the query or report definition used; snapshot of data used (as Rillet) | Art. 50 chatbot disclosure; no access beyond the user's permissions |
| 12 | Collections email drafting and prioritisation | Drafts only, or human-approved templates sent by rule; prioritisation explained | If customers are **natural persons** (sole traders, care self-funders), prioritisation is profiling under GDPR. Keep it advisory |
| 13 | Credit-risk indicators and credit-limit suggestions | Advisory; credit holds require a human; never fully automated for natural persons | **EU AI Act Annex III 5(b)**: creditworthiness evaluation or credit scoring of natural persons is **high-risk** (obligations from 2 Dec 2027). UK Arts 22A–22D: significant decisions need meaningful human involvement. Recommend limiting to companies, or building as a high-risk system |
| 14 | Expense policy checks (receipt vs claim mismatch, duplicate claims) | Flags to approver only | **Annex III 4(b)**: AI used to monitor and evaluate the behaviour of workers may be high-risk. Keep to document checks, no worker scoring; coordinate with Thapsus-HR DPIA |
| 15 | Contract-to-revenue extraction (ASC 606/IFRS 15/FRS 102 s23) | Extracted terms → draft revenue schedule → accountant approves | Judgement-heavy; keep human sign-off |
| 16 | Lease extraction (IFRS 16/ASC 842/FRS 102 s20) | Same | Same |
| 17 | VAT and sales-tax return review (outliers before submission) | Human submits; MTD submission always user-initiated | Tax filing liability stays with the customer |
| 18 | Migration assistant (CoA mapping, history import from Xero/QBO/Sage) | Mapping proposals reviewed before import | Low |
| 19 | Configuration copilot (build reports, workflows, custom fields, rules in plain English) | Generates configuration **drafts** in a sandbox; diff preview; admin publishes | Low; config changes audited |
| 20 | Audit sampling and evidence packs (statistical or risk-based selection) | Method documented; auditor selects and approves | Auditors must evaluate tool reliability under professional standards |
| 21 | Supplier statement reconciliation | Proposed differences list; human resolves | Low |
| 22 | Document Q&A on invoices, contracts and policies | Citations to source page; read-only | Art. 50 |

### 3.3 Regulatory notes on AI

- **EU AI Act** (Reg. (EU) 2024/1689). Prohibitions and AI literacy have applied since 2 Feb 2025, and general-purpose AI obligations since 2 Aug 2025. **Art. 50 transparency** applies from 2 Aug 2026. The **Digital Omnibus on AI** (reported as Reg. (EU) 2026/1744, OJ 24 Jul 2026, in force 27 Jul 2026) moved **Annex III** high-risk obligations to **2 Dec 2027** and Annex I to 2 Aug 2028. Status: **adopted** (secondary sources: [Gibson Dunn](https://www.gibsondunn.com/eu-ai-act-omnibus-agreement-postponed-high-risk-deadlines-and-other-key-changes/), [Orrick](https://www.orrick.com/en/Insights/2026/07/EU-AI-Act-Update-Digital-Omnibus-Finalizes-8-Compliance-Changes), [CSA](https://labs.cloudsecurityalliance.org/research/csa-research-note-eu-ai-act-omnibus-vii-deadline-delay-20260/)). Accounting automation is mostly minimal or limited risk. The high-risk traps are **credit scoring of natural persons** and **worker monitoring**.
- **UK:** the Data (Use and Access) Act 2025 s.80 replaced Art. 22 UK GDPR with **Arts 22A–22D** (reported in force **5 Feb 2026** under SI 2026/82). Solely automated significant decisions are generally permitted with safeguards (information, representations, human intervention, contest), except where special-category data is involved. The ICO's ADM guidance consultation closed 29 May 2026, and final guidance is due winter 2026 (secondary: [Bratby Law](https://bratby.law/automated-decision-making-duaa-uk-regime/), [Janus Compliance](https://www.januscompliance.co.uk/blog/automated-decisions-uk-article-22a-22d-2026)). EU GDPR Art. 22 is unchanged.
- **US:** no federal AI statute affects accounting software directly. State ADMT and AI laws (CCPA regulations, Colorado) could touch consumer and employee decisions (**unverified**).
- **Professional and audit:** AI-prepared journals and reconciliations need the same approval evidence as manual ones (SOX ITGC and ICFR). Keep model version, prompt template version and input snapshot with each AI output.

---

## 4. Integration points

### 4.1 Thapsus-HR ↔ MULLAH

| Flow | Direction | Content | Notes |
| --- | --- | --- | --- |
| Employee as payee | HR → MULLAH | Employee ID, legal name, bank details (verified), cost centre, entity, manager, start and leave dates | Minimise: MULLAH needs payee data, not HR records; separate permission for bank details |
| Org structure and cost centres | HR → MULLAH | Departments, locations and cost centres as **dimension values** (effective-dated) | One source of truth for org; finance owns GL mapping |
| Payroll journals | Payroll engine (Xero/CSV/SFTP connectors) → MULLAH | Gross pay, employer NIC or FICA, pensions, deductions, net pay by employee or department; pay-element-to-GL mapping | Thapsus-HR never runs payroll. MULLAH imports the payroll provider's posted results; HR may forward them |
| Payroll liabilities and net-pay payment | MULLAH | HMRC PAYE, pension, US federal and state deposits; net-pay batch (if customer pays wages through MULLAH) | |
| Expense claims | MULLAH (owner) with HR data | Claimant, approver chain, policy group | Section 1.9 |
| Headcount and salary budgets | HR → MULLAH FP&A | Planned headcount, salary bands (aggregated) | Aggregate to avoid individual pay exposure |
| Labour cost allocation | HR (time and attendance) → MULLAH | Hours by project, service or funder | Care: staff cost per service user or contract |
| Leavers and access | HR → MULLAH | Deactivate payee and approvals on termination | SSO/SCIM for user lifecycle |

### 4.2 BOSSMAN (CRM) ↔ MULLAH

| Flow | Direction | Content |
| --- | --- | --- |
| Customer master | Shared "party" service, or CRM → MULLAH on first billable event | Legal entity, billing contacts, addresses (structured), tax IDs, Peppol ID, currency, terms |
| Quote → order → invoice | CRM → MULLAH | Accepted quote lines become invoice or subscription; product and price list sync; MULLAH returns invoice number and status |
| Credit status and limits | MULLAH → CRM | Credit limit, available credit, on-hold flag, overdue balance, DSO, dunning stage |
| Payment status | MULLAH → CRM | Invoice paid, part-paid or overdue; payment date; disputes |
| Revenue and ARR | MULLAH → CRM | Recognised revenue, MRR/ARR per account, renewals |
| Commission inputs | MULLAH → CRM/HR | Cash-collected or invoiced amounts per rep (pay inputs flow to payroll via HR) |
| Events | Both | Outbox events: `customer.updated`, `invoice.issued`, `invoice.paid`, `credit.hold_changed` with idempotency keys |

### 4.3 KASONGO (ERP) ↔ MULLAH (MULLAH is the finance core)

| Flow | Postings |
| --- | --- |
| Inventory receipts | Dr inventory / Cr **GRNI** (goods received not invoiced); landed-cost allocation adjusts inventory value |
| Supplier invoice match | Dr GRNI / Cr AP; price and quantity variances to PPV accounts; 3-way match status from KASONGO |
| Sales shipments | Dr COGS / Cr inventory at the valuation method (FIFO, weighted average, standard) |
| Customer invoicing from sales orders | AR and revenue via MULLAH's invoicing API (single invoice numbering and e-invoicing engine) |
| Manufacturing / WIP | Material issues to WIP, labour and overhead absorption, completion to finished goods, variances |
| Inventory revaluation, write-downs, stock counts | Adjustment postings with dimension tags |
| Fixed assets from capital POs | Asset creation in MULLAH register |
| Intercompany stock transfers | Intercompany sales and purchases with eliminations of unrealised profit (Enterprise) |
| Period close | KASONGO sub-ledger cut-off and reconciliation (inventory sub-ledger = GL control account) |

Design requirement: a **posting API** (idempotent, with source document reference, dimension validation, period checks and a balanced-entry guarantee) and **sub-ledger control accounts** that only system postings may hit.

### 4.4 Thapsus Care ↔ MULLAH

| Flow | Content | Notes |
| --- | --- | --- |
| Funder invoices | Local authority, NHS Continuing Healthcare, self-funders, third-party top-ups, deferred-payment agreements; schedules per resident and period; split invoicing per funder share | Invoices may need NHS Peppol (NHS England already requires Peppol for suppliers, per UK consultation) |
| Contract and rate cards | Weekly fees, uplift dates, funder contribution rules | Effective-dated |
| Remittances and receipts | LA remittance files matched to resident-level invoice lines | AI cash application candidate |
| **Residents' personal money** | **Client-money (trust) sub-ledger**, not company funds: per-resident balances, deposits, withdrawals, receipts, reconciled to a designated client bank account; dual control over tenant limit; statements to residents and representatives | Keep off the company balance sheet; strict permissions; audit for every view (special-category context) |
| VAT | Care services generally exempt → partial exemption (section 1.13) | |
| Cost allocation | Staff costs and overheads by home, unit, service and funder (bed-days statistical account) | |
| Existing exports | Thapsus Care currently feeds Xero/Sage/QuickBooks; MULLAH should accept the same journal schema to make migration trivial | |

### 4.5 External integrations accountants expect

| Category | Integrations |
| --- | --- |
| Payments in | Stripe, GoCardless (Direct Debit and pay by bank), PayPal, Adyen, Square, open-banking PIS (TrueLayer, Yapily), UK cVRP via PISP |
| Payments out | Bank payment files, PISP bulk payments, Wise Business, Airwallex, US ACH originators (Modern Treasury-type) |
| Commerce | Shopify, Amazon, WooCommerce, eBay (with settlement reconciliation like A2X/Link My Books) |
| Capture | Dext, Hubdoc, AutoEntry (import), plus native capture |
| Spreadsheets | Excel add-in, Google Sheets add-on, OData feeds, scheduled exports |
| Practice | Xero Practice Manager-style systems, Karbon, TaxDome, IRIS, TaxCalc, BTCSoftware (accounts production), DATEV (DE) |
| Tax | Avalara, Vertex, Stripe Tax, TaxJar, Anrok (US); HMRC MTD; Peppol AP partners |
| Reporting and FP&A | Fathom, Syft, Spotlight, Jirav, Pigment, Power BI, Looker Studio |
| Spend | Pleo, Spendesk, Ramp, Brex, Soldo (feeds and receipts) |
| Billing | Stripe Billing, Chargebee, Recurly, Zuora |
| Payroll | Thapsus-HR connectors plus Xero Payroll, Sage Payroll, BrightPay, Staffology, ADP, Gusto, Rippling, Paychex |
| Platform | Public REST API (OpenAPI 3.1, `/v1`), webhooks, SCIM, SSO (SAML/OIDC), Zapier/Make, MCP server for customers' own AI tools (Sage and Intuit are moving this way) |

### 4.6 Suite architecture implications

- **Shared party model:** one canonical organisation and person record service across BOSSMAN, MULLAH, KASONGO and Care, with product-specific extensions. This avoids four customer masters.
- **Outbox events plus idempotent consumers** (already a Thapsus rule) for cross-product flows. Use versioned event schemas published with the OpenAPI spec.
- **Posting API** as the only way other products affect the ledger. Every posting carries `source_product`, `source_document_id`, `idempotency_key`, dimensions and an `effective_date`.
- **Residency:** all integrations that receive personal data (aggregators, Peppol APs, tax engines, AI models, payment processors) need per-region entries in SUBPROCESSORS.md. Some (e.g. Avalara and Vertex) process US data in the US only, which fits the US cell.

---

## 5. Customer-specific configuration without code

| Capability | What leaders offer | MULLAH recommendation |
| --- | --- | --- |
| Custom fields (transactions, lines, contacts, items, projects) | QBA (custom fields), ZB (custom fields plus **custom modules**), SI (custom fields and objects via Platform Services), NS (custom fields, records, segments), OD (Studio), BC (mostly via AL extensions, which means code), XR (very limited) | Typed fields (text, number, money, date, list, lookup, formula) with validation, visibility by role, use in reports, API and templates |
| Custom objects or records | ZB (custom modules), SI (Platform Services), NS (custom records), OD (Studio) | Advanced tier; with RLS and audit like built-ins |
| Dimensions | Section 1.1 | Unlimited, hierarchical, effective-dated, with posting rules |
| Validation and business rules | SI **Smart Rules** and Smart Events, NS SuiteFlow, ZB workflow rules, BC dimension rules | No-code rule builder ("if account in 6000–6999 then project required"), error or warning |
| Approval workflows | NS SuiteFlow, SI approvals, ZB (workflow plus **Blueprints** on Elite), QBA workflow automation, BC workflows plus Power Automate, OD Studio | Visual designer: conditions on amount, dimension, supplier, category; parallel and serial steps; delegation; SLA reminders; mobile approve |
| Document templates (invoice, credit note, statement, remittance, PO) | All (XR branding themes, NS Advanced PDF/HTML, BC Word layouts, ZB templates) | WYSIWYG designer with conditional blocks, multi-language catalogues, per-entity branding; e-invoice output generated separately from layout |
| Numbering sequences | All; country rules require gapless series (ES, IT, PT, PL) | Per entity, document type and series; gapless option with reserved-number audit; fiscal-year reset |
| Custom reports and dashboards | Section 1.15 | Report builder; saved views; scheduled distribution; dashboards per role |
| Bank and coding rules | Section 1.7 | Rule builder with test-on-history preview |
| Dunning sequences and email templates | All | Per customer group; language; channel |
| Roles and permissions | SI, NS, BC, QBA (custom roles) | Custom roles from permission catalogue; field-level for bank details and salaries; dimension restrictions |
| Close checklist templates | NS, CF | Templates with dependencies, owners, evidence requirements |
| Tax configuration | All | Jurisdiction packs maintained by Thapsus (versioned, sourced) plus tenant overrides where law permits |
| Import templates and mappings | All | Saved mappings for CSV and bank formats |
| Sandboxes and promotion | NS (sandbox), SI (sandbox), BC (sandbox environments) | Config as versioned bundles; promote sandbox to production; diff and rollback; audit |
| Integrations without code | Zapier and Make connectors; NS SuiteScript (code); BC Power Automate | Event subscriptions and webhooks configured in the UI; recipe library |

Principle: configuration is data. It is versioned, effective-dated, exportable, auditable and testable in a sandbox, so that "customisable without code" never becomes per-customer forks (matches the Thapsus Care "configurable, never forked" rule).

---

## 6. Market and pricing notes

### 6.1 Published or reported prices (Nov 2026)

| Product | Packaging | Price points | Source and confidence |
| --- | --- | --- | --- |
| Xero UK | Per organisation, unlimited users, feature tiers | Ignite £18, Grow £39, Comprehensive £55, Ultimate £70 per month ex VAT (intro 90% off for 6 months). Add-ons: CIS £5 a month; company accounts and tax £120 a year; auto-reconcile on Ignite £3.50 a month. Multi-currency from Comprehensive; projects on Ultimate; cash-flow forecast 30/60/90/180 days by plan | [xero.com/uk/pricing-plans](https://www.xero.com/uk/pricing-plans/) (official) |
| QuickBooks Online US | Per company, user caps | Free $0 (1 user), Simple Start $38 (1), Essentials $85 (3), Plus $140 (5), Advanced $340 (25) per month list; AI capabilities tiered by plan; Intuit Enterprise Suite by quote (~$8k–15k a year estimate, **unverified**) | [quickbooks.intuit.com/pricing](https://quickbooks.intuit.com/pricing/) (official); [ERP Research IES](https://erpresearch.com/en-us/intuit-enterprise-suite-pricing) (estimate) |
| FreeAgent UK | Per business, by entity type | Limited company £33, partnership/LLP £27, sole trader £19, landlord £10 per month ex VAT; **free** with NatWest, RBS, Ulster Bank or Mettle accounts; Smart Capture Unlimited £5 | [freeagent.com/pricing](https://www.freeagent.com/pricing/) (official) |
| Zoho Books UK | Per organisation, user caps | Free, Standard £12 (£10 annual), Professional £24 (£20), Premium £30 (£25), Elite £99 (£85), Ultimate £199 (£165) per month; multi-currency from Professional; custom modules and budgets from Premium | [zoho.com/uk/books/pricing](https://www.zoho.com/uk/books/pricing/) (official) |
| Odoo | Per user, all apps | One App Free; Standard US$31.10 (annual) / $38.90 (monthly) per user per month; Custom US$61.00 / $76.20 (Studio, multi-company, API, "Agentic AI") | [odoo.com/pricing](https://www.odoo.com/pricing) (official) |
| Sage Accounting UK | Per business, user caps | Start, Standard, Plus: reported from £12–£18 (Start) to £30–£59 (Plus) ex VAT; sources conflict | [Startups.co.uk](https://startups.co.uk/accounting/sage-business-cloud-accounting-review/) (secondary, **unverified**) |
| Microsoft Business Central | Per named user | Essentials **$80**, Premium **$110**, Team Members $8 per user per month (annual); agents billed in Copilot Credits (pre-purchase or pay-as-you-go). Up from $70/$100 (increase announced for Oct 2025, applied around Nov 2025) | [Microsoft](https://www.microsoft.com/en-us/dynamics-365/products/business-central/pricing) (official) |
| NetSuite | Base licence plus per user plus modules; annual contract | Not published; ~$40k–200k+ year one (third-party estimate) | [G2](https://www.g2.com/products/netsuite/pricing) (**unverified** figures) |
| Sage Intacct | Per entity, users and modules; via partners | Not published | Quote-based (**unverified**) |
| Digits | Tiered; firm pricing outcome-based (partner-only from Sep 2026) | Reported $35–$100 a month SMB; firms from $50 per client per month (Starter Plus, 50+ clients) | [UsagePricing](https://www.usagepricing.com/blueprint/activity/digits-2026-09-23-firm-pricing-partner-only) (secondary) |
| Rillet, Campfire | Annual contracts, quote-based | Not published | n/a |
| OpenPeppol (cost input) | Membership plus certification | AP and SMP (11–50 staff): €2,000 sign-up, €5,050 a year certified | [peppol.org](https://peppol.org/join/fees-2025/) (official, 2025 schedule) |
| Tax engines (cost input) | Per transaction or annual | Stripe Tax 0.5% per transaction (no-code); Anrok from ~$5k a year; Avalara ~$20k a year mid-market; Vertex ~$30k+ (all secondary) | [switchlabs](https://www.switchlabs.dev/post/sales-tax-software-complete-guide-2026) (secondary) |

### 6.2 Packaging patterns

- **SMB (micro to ~50 staff):** per-organisation monthly tiers, gated by features and volumes (invoices, bills, payments, users, multi-currency, projects). Heavy intro discounts (Xero 90% for 6 months; QBO 50% for 3 months; FreeAgent 50%). Bank-distribution deals (FreeAgent free with NatWest). Accountant channel with wholesale pricing.
- **Mid-market (50–1,000 staff, multi-entity):** per user plus per entity plus modules (consolidation, revenue, planning), annual contracts, partner-led implementation (Sage Intacct, NetSuite, BC). Team-member or approver licences are cheap.
- **Enterprise:** custom contracts, sandbox environments, SSO, SOC 1/2 reports, premium support, data residency commitments.
- **AI:** included in plans (Xero JAX), tiered by plan (QBO), **consumption credits** (BC Copilot Credits), or **outcome-based** (Digits).

### 6.3 Suggested MULLAH packaging (for discussion)

| Tier | Target | Indicative contents |
| --- | --- | --- |
| Starter (per entity) | Micro and small | Core GL, AR/AP, banking, VAT/sales tax, expenses (limited users), 3 dimensions, standard reports |
| Growth (per entity) | SMB | Unlimited users, multi-currency, workflows, PO light, projects, budgets, custom reports, e-invoicing |
| Scale (per entity plus per user) | Lower mid-market and groups | Multi-entity, intercompany, consolidation, revenue schedules, leases, close management, custom fields and objects |
| Enterprise (contract) | Mid-market, KASONGO customers | NCI and CTA consolidation, multi-book, ASC 606 engine, SoD, sandbox, SOX evidence, dedicated database option |
| Practice (wholesale) | Accountants | Multi-client dashboard, accounts production plus iXBRL plus CT600 |
| AI credits | All tiers | Off by default; tenant caps; usage visible per feature |

---

## 7. Sources

Official and primary sources (fetched or found in search during this session):

- HMRC/DBT, Roadmap for e-invoicing implementation (28 Oct 2026): https://www.gov.uk/government/publications/e-invoicing-implementation-roadmap/roadmap-for-e-invoicing-implementation
- HMRC/DBT, e-invoicing consultation response: https://www.gov.uk/government/consultations/promoting-electronic-invoicing-across-uk-businesses-and-the-public-sector/outcome/promoting-electronic-invoicing-across-uk-businesses-and-the-public-sector-consultation-response
- HMRC, e-invoicing digital reporting consultation: https://www.gov.uk/government/consultations/e-invoicing-digital-reporting/e-invoicing-digital-reporting-consultation-on-the-future-use-of-e-invoicing-digital-reporting
- Companies House, update to accounts filing from April 2028 (23 Oct 2026): https://www.gov.uk/government/news/companies-house-update-to-accounts-filing-from-april-2028
- Companies House, earlier notice (9 Jun 2026, withdrawn): https://www.gov.uk/government/news/companies-house-to-bring-in-changes-to-accounts-filing-from-april-2028
- Changes to UK company law (accounts): https://changestoukcompanylaw.campaign.gov.uk/changes-to-accounts/
- gov.uk, file your company accounts and tax return (service closed): https://www.gov.uk/file-your-company-accounts-and-tax-return
- gov.uk, MTD for Income Tax eligibility: https://www.gov.uk/guidance/find-out-if-and-when-you-need-to-use-making-tax-digital-for-income-tax
- gov.uk news, MTD ITSA £30,000: https://www.gov.uk/government/news/sole-traders-and-landlords-earning-more-than-30000-urged-to-act-now
- HMRC Developer Hub, MTD ITSA end-to-end service guide: https://developer.service.hmrc.gov.uk/guides/income-tax-mtd-end-to-end-service-guide/
- VAT Notice 700/22: https://www.gov.uk/government/publications/vat-notice-70022-making-tax-digital-for-vat/vat-notice-70022-making-tax-digital-for-vat
- Fraud prevention header direction: https://gov.uk/government/publications/direction-under-regulation-22-of-the-delivery-of-tax-information-through-software-ancillary-metadata-regulations-2019-si-2019360
- CIS amendment regulations 2026 (SI 2026/289): https://www.legislation.gov.uk/uksi/2026/289/made
- CIS simplification policy paper: https://www.gov.uk/government/publications/simplifying-and-improving-construction-industry-scheme-administration/simplification-and-administrative-improvements-to-the-construction-industry-scheme
- Finance Act 2026: https://www.legislation.gov.uk/ukpga/2026/11/enacted
- gov.uk company and accounting records: https://www.gov.uk/running-a-limited-company/company-and-accounting-records
- HMRC Compliance Handbook CH14600 and CH15400: https://www.gov.uk/hmrc-internal-manuals/compliance-handbook/ch14600 ; https://www.gov.uk/hmrc-internal-manuals/compliance-handbook/ch15400
- FRC 2026 taxonomy suite: https://www.frc.org.uk/news-and-events/news/2025/11/frc-publishes-2026-uk-taxonomy-suite/
- FRC draft 2027 taxonomy suite: https://www.frc.org.uk/news-and-events/news/2026/07/draft-2027-taxonomy-suite-published/
- Small Business Commissioner, late payment powers: https://www.smallbusinesscommissioner.gov.uk/new-powers-for-the-small-business-commissioner-as-part-of-government-package-to-tackle-late-payments/
- economie.gouv.fr, facturation électronique: https://www.economie.gouv.fr/tout-savoir-sur-la-facturation-electronique-pour-les-entreprises
- impots.gouv.fr, guide pratique 1 Sep 2026: https://www.impots.gouv.fr/sites/default/files/media/1_metier/2_professionnel/EV/2_gestion/290_facturation_electronique/guide_pratique_facturation_electronique.pdf
- European Commission eInvoicing in Italy: https://ec.europa.eu/digital-building-blocks/sites/spaces/DIGITAL/pages/467108890/eInvoicing+in+Italy
- European Commission 2025 Romania sheet: https://ec.europa.eu/digital-building-blocks/sites/spaces/einvoicingCFS/pages/881983595/2025+Romania+2025+eInvoicing+Country+Sheet
- ECB, Instant Payments Regulation: https://www.ecb.europa.eu/paym/integration/retail/instant_payments/html/instant_payments_regulation.ro.html
- OpenPeppol fees 2025: https://peppol.org/join/fees-2025/ ; Testbed: https://peppol.org/tools-support/testbed/ ; Onboarding v1.4: https://peppol.org/wp-content/uploads/2024/03/Peppol_TestbedAndOnboarding_v1.4.pdf ; PASR: https://peppol.org/documentation/governance-documentation/peppol-authority-specific-requirements/ ; NL PASR: https://peppol.org/wp-content/uploads/2022/08/The-Netherlands-Peppol-Authority-Specific-Requirements.pdf ; Peppol reporting: https://docs.peppol.eu/edelivery/guidelines/reporting/2023-12-01%20Peppol%20Reporting%20-%20SP%20Operational%20Guideline%20v1.0.pdf
- ATO e-invoicing accreditation: https://softwaredevelopers.ato.gov.au/e-invoicing-accreditation-process
- IRS, 1099-MISC/NEC instructions (Rev. Dec 2026): https://www.irs.gov/instructions/i1099mec
- IRS, 1099-K FAQs: https://www.irs.gov/newsroom/form-1099-k-faqs-third-party-filers-of-form-1099-k
- IRS, proposed backup-withholding regs: https://www.irs.gov/newsroom/treasury-irs-issue-proposed-regulations-reflecting-changes-from-the-one-big-beautiful-bill-to-the-threshold-for-backup-withholding-on-certain-payments-made-through-third-parties
- IRS, OBBB news: https://www.irs.gov/newsroom/one-big-beautiful-bill-news
- IRS, IR-2026-99 FIRE to IRIS: https://www.irs.gov/newsroom/irs-reminder-information-return-e-file-system-transitioning-to-a-new-platform
- IRS, e-file information returns: https://www.irs.gov/filing/e-file-information-returns ; General Instructions: https://www.irs.gov/instructions/i1099gi ; Pub 1220: https://www.irs.gov/pub/irs-pdf/p1220.pdf
- Nacha fraud monitoring phase 1 and 2: https://www.nacha.org/rules/risk-management-topics-fraud-monitoring-phase-1 ; https://www.nacha.org/rules/risk-management-topics-fraud-monitoring-phase-2
- Federal Reserve FedNow $10m: https://frbservices.org/news/press-releases/090525-fednow-transaction-limit-increase
- FRB Services, DBNAlliance launch: https://www.frbservices.org/news/fed360/issues/060123/b2b-payments-experts-launch-digital-business-networks-alliance
- Congressional Research Service on §1033: https://www.congress.gov/crs-product/IF13117
- SWIFT ISO 20022: https://www.swift.com/standards/iso-20022/iso-20022-bytes/journey-continues ; CBPR+ roadmap: https://www.swift.com/sites/default/files/files/cbpr-phase-2-roadmap_detailed_v4-2.pdf
- PCI SSC SAQ A update: https://blog.pcisecuritystandards.org/important-updates-announced-for-merchants-validating-to-self-assessment-questionnaire-a
- EFRAG endorsement status: https://www.efrag.org/en/financial-reporting/endorsement-status
- Vendor pricing pages: https://www.xero.com/uk/pricing-plans/ ; https://quickbooks.intuit.com/pricing/ ; https://www.freeagent.com/pricing/ ; https://www.zoho.com/uk/books/pricing/ ; https://www.odoo.com/pricing ; https://www.microsoft.com/en-us/dynamics-365/products/business-central/pricing
- Vendor AI and product sources: https://blog.xero.com/product-updates/xerocon-london-2026-agentic-era-small-business-finance/ ; https://www.xero.com/media-releases/new-ai-innovations-xerocon-denver/ ; https://www.intacct.com/ia/docs/en_AU/releasenotes/2026/2026_Release_2/Sage_AI_and_Automation/2026-R2-copilot-FIA-EA.htm ; https://www.intacct.com/ia/docs/en_AU/help_action/Consolidations/Affiliate_entity_dimension/about-affiliate-entity.htm ; https://www-p107.intacct.com/ia/docs/en_US/releasenotes/2025/2025_Release_2/Consolidation/2025-R2-affiliate-entity-dimension.htm ; https://www.sage.com/en-za/sage-business-cloud/intacct/additional-financial-management-modules/consolidation-accounting/ ; https://learn.microsoft.com/cs-cz/dynamics365/release-plan/2026wave1/smb/dynamics365-business-central/planned-features ; https://quickbooks.intuit.com/community/quickbooks-news-64/meet-your-dedicated-digital-teammates-intuit-ai-agents-164995 ; https://www.investegate.co.uk/announcement/gnw/sage-group--sge/sage-expands-ai-agents-across-finance-hr-and-/9542697

Secondary sources (law firms, Big Four, trade press, trackers, vendors):

- ViDA: https://en.wikipedia.org/wiki/VAT_in_the_Digital_Age ; https://www.meijburg.com/news/eu-proposal-vat-digital-age-package-formally-adopted ; https://sovos.com/guides/vat-in-the-digital-age/ ; https://accountancyeurope.eu/publications/vat-in-the-digital-age-vida/ ; https://avask.com/blog/vida-timeline/
- Country tracker: https://www.invoicenavigator.eu/deadlines
- Belgium: https://taxnews.ey.com/news/2026-1652-belgium-announces-proposed-e-reporting-requirements-beginning-in-2028 ; https://kpmg.com/us/en/taxnewsflash/news/2026/07/tnf-belgium-proposal-to-introduce-e-reporting-of-invoicing-data-from-2028.html ; https://www.vertexinc.com/resources/resource-library/belgiums-2026-e-invoicing-regulations-explained-scope-deadlines-and-penalties
- Poland: https://kpmg.com/pl/en/insights/taxes/tax-alert-introduction-of-ksef-confirmed.html ; https://sovos.com/blog/vat/poland-e-invoicing-via-ksef/ ; https://crido.pl/blog-taxes/ksef-bez-kar-do-2028-r/ ; https://www.bankier.pl/wiadomosc/Kary-za-bledy-w-KSeF-odroczone-o-kolejny-rok-Rzad-chce-przesunac-sankcje-na-2028-rok-9214634.html
- Germany: https://www.fiscal-requirements.com/news/4566-germany-updated-bmf-guidance-on-2025-2028-b2b-e-invoicing-rollout ; https://storecove.com/blog/en/e-invoicing-in-germany ; https://kpmg.com/de/de/themen/2025/07/bmf-gobd-zweite-aenderung.html ; https://www.iww.de/bbp/bilanzierung/bmf-schreiben-zweite-aenderung-der-gobd-aufgrund-gesetzlicher-anpassungen-n168278 ; https://www.zdh.de/ueber-uns/fachbereich-steuern-und-finanzen/abgabenordnung/aktuelles/veroeffentlichung-aktualisierter-gobd/ ; https://www.haufe.de/steuern/gesetzgebung-politik/viertes-buerokratieentlastungsgesetz_168_613390.html ; https://www.ey.com/de_de/technical/steuernachrichten/laengere-aufbewahrungsfristen-bei-banken-versicherungen-und-wertpapierinstituten ; https://www.ihk.de/karlsruhe/fachthemen/steuerrecht/aktuelles/neuer-inhalt4-buerokratieentlastungsgesetz-6347138
- Spain: https://kpmg.com/us/en/taxnewsflash/news/2025/12/tnf-spain-verifactu-invoicing-system-delayed-to-2027.html ; https://www.economistjurist.es/noticias-juridicas/nuevo-aplazamiento-de-la-aplicacion-del-sistema-verifactu-hasta-2028/ ; https://www.supercontable.com/boletin/H/articulos/Verifactu_se_aplaza_octubre_2028_incertidumbre_anuncio_sin_norma_con_elecciones.html ; https://garrido.es/real-decreto-238-2026-aprueba-el-desarrollo-reglamentario-de-la-factura-electronica/ ; https://www.b2brouter.net/es/plazos-obligatoriedad-factura-electronica-b2b/
- Greece: https://www.comarch.com/trade-and-services/data-management/legal-regulation-changes/greece-postpones-e-invoicing-for-smaller-businesses-and-phase-b-of-digital-movement-documents/ ; https://invoice-portal.de/blog/greece-postpones-phase-b/
- Portugal: https://www.zoneandco.com/articles/e-invoicing-portugal-compliance-guide ; https://europe.thomsonreuters.com/compliance/regulatory-updates/portugal
- Denmark: https://www.grantthornton.dk/en/insights/articles/new-bookkeeping-act/ ; https://www.fiscal-requirements.com/news/6054-denmark-postpones-new-digital-bookkeeping-system-requirements-to-march-2027-and-january-2028
- SAF-T: https://vatit.com/blog/saf-t-reporting-vat-compliance/ ; https://www.vatcalc.com/global/which-countries-have-introduced-oecds-saf-t-2/ ; https://sovos.com/vat/tax-rules/bulgaria-saf-t/
- FRS 102: https://www.pkfsmithcooper.com/news-insights/frs-102-changes-effective-from-1st-january-2026/ ; https://www.ey.com/content/dam/ey-unified-site/ey-com/en-uk/technical/ifrs-technical-resources/documents/ey-key-changes-to-ifrs-102-arising-from-the-periodic-review.pdf ; https://www.accaglobal.com/uk/en/technical-activities/uk-tech/in-practice-ezine-archive/In-Practice-archive-2025/May/Key-amendments-FRS-102-periodic-review.html
- IFRS 18: https://www.bdo.global/en-gb/news/ifrs-news/the-european-union-endorses-ifrs-18-presentation-and-disclosure-in-financial-statements
- UK late payment: https://www.mayerbrown.com/en/insights/publications/2026/03/uk-government-response-to-late-payment-consultation ; https://www.icaew.com/insights/viewpoints-on-the-news/2026/may-2026/uk-government-ploughs-ahead-with-late-payments-bill
- US nexus and tax engines: https://www.avalara.com/us/en/blog/states-eliminating-economic-nexus-transaction-thresholds.html ; https://www.taxually.com/blog/economic-nexus-by-state ; https://zamp.com/blog/anrok-vs-stripe-tax-vs-taxjar-api ; https://www.numeral.com/blog/taxjar-vs-vertex ; https://www.switchlabs.dev/post/sales-tax-software-complete-guide-2026
- DBNAlliance: https://www.storecove.com/blog/en/digital-business-network-alliance
- CFPB 1033: https://www.cozen.com/news-resources/publications/2026/section-1033-compliance-date-open-banking-rule-enjoined-and-under-reconsideration ; https://www.mvalaw.com/data-points/cfpb-enjoined-from-enforcing-personal-financial-data-rights-rule-1033 ; https://www.openbankingtracker.com/guides/section-1033-status
- PSD3/PSR and FIDA: https://www.arthurcox.com/insights/psd3-and-psr-final-compromise-texts-published/ ; https://www.mofo.com/resources/insights/260430-psd3-and-the-payment-services-regulation-key-developments ; https://clearingpost.com/insights/psd3-psr-econ-committee-clearance-plenary-slips-june-2026/ ; https://www.mlex.com/mlex/antitrust/articles/2545816 ; https://thede-consulting.com/en/payment-en/fida-back-in-motion-from-ambitious-to-workable/
- UK cVRP: https://www.openbankingtracker.com/guides/commercial-vrp-ukpi ; https://www.aoshearman.com/en/insights/financial-services-horizon-report-2026/payment-services-and-payment-systems ; https://www.modulrfinance.com/blog-insights/commercial-vrp-uk-payments
- APP fraud: https://www.hlc.com/en/publications/app-fraud-uk-psr-confirms-reduced-maximum-level-of-reimbursement ; https://finextra.com/pressarticle/102475/psr-confirms-85k-reimbursement-limit-for-app-fraud-victims
- IPR: https://twobirds.com/en/insights/2024/global/instant-payments-regulation ; https://osborneclarke.com/node/25752
- ISO 20022: https://www.jpmorgan.com/insights/payments/cross-border-payments/iso-20022-migration
- PCI: https://docs.adyen.com/development-resources/pci-dss-compliance-guide/saq-a-eligibility
- Aggregators: https://freenance.io/fintech/psd2-aisp-licensed-providers-eu-2026-list-tink-truelayer-yapily-salt-edge-plaid-finicity-comparison/ ; https://www.yapily.com/blog/tink-alternatives-6-open-banking-platforms-compared/ ; https://docs.firefly-iii.org/how-to/data-importer/import/gocardless/ ; https://dev.to/johnfrandsen/gocardless-bank-account-data-alternatives-what-to-use-when-signups-are-disabled-326d
- EU AI Act omnibus: https://www.gibsondunn.com/eu-ai-act-omnibus-agreement-postponed-high-risk-deadlines-and-other-key-changes/ ; https://www.orrick.com/en/Insights/2026/07/EU-AI-Act-Update-Digital-Omnibus-Finalizes-8-Compliance-Changes ; https://labs.cloudsecurityalliance.org/research/csa-research-note-eu-ai-act-omnibus-vii-deadline-delay-20260/
- UK ADM: https://bratby.law/automated-decision-making-duaa-uk-regime/ ; https://www.januscompliance.co.uk/blog/automated-decisions-uk-article-22a-22d-2026
- AI vendors: https://www.accountingtoday.com/news/xeros-jax-ai-gains-agentic-capacities ; https://www.cpapracticeadvisor.com/?p=193101 ; https://diginomica.com/node/31365 ; https://erp.today/2025-was-a-pivotal-point-for-oracle-netsuite-where-its-ai-roadmap-is-heading-in-2026 ; https://versich.com/blog/netsuite-suiteworld-2026-recap/ ; https://accountingtoday.com/news/digits-announces-autonomous-general-ledger ; https://www.usagepricing.com/blueprint/activity/digits-2026-09-23-firm-pricing-partner-only ; https://thenextweb.com/news/rillet-100m-series-c-1bn-accounting-ai ; https://ffnews.com/news/rillet-secures-100m-series-c-at-1b-valuation-to-scale-ai-native-accounting-erp ; https://www.fintechfutures.com/venture-capital-funding/erp-platform-campfire-fuels-up-with-65m-series-b ; https://www.webpronews.com/campfires-ai-erp-inferno-doubling-revenue-eyeing-ipo-wave
- Xero tracking-category limits: https://productideas.xero.com/forums/967124-projects-tracking/suggestions/50517771-tracking-categories-increase-maximum-number-of-a ; https://www.connectbooks.com/blog-posts/glossary-xero-tracking-categories
- Pricing estimates: https://erpresearch.com/en-us/intuit-enterprise-suite-pricing ; https://www.g2.com/products/netsuite/pricing ; https://startups.co.uk/accounting/sage-business-cloud-accounting-review/ ; https://alphavima.com/blog/business-central-pricing-guide/

---

## 8. Things I could not verify

The shared web-search budget ran out before every item could be checked. A follow-up session can close these.

1. **UK MTD ITSA £20,000 stage (6 Apr 2028):** gov.uk still says legislation is "being introduced"; whether Finance Act 2026 enacted it is unconfirmed.
2. **UK e-invoicing primary legislation:** the roadmap says "Finance Bill 2026"; its parliamentary stage is not confirmed.
3. **HMRC CATO closure date** (reported 31 Mar 2026; gov.uk confirms closure without a date). Also HMRC's process for listing CT600 software (assumed "test in live" plus recognition list).
4. **Companies House identity verification start date** (18 Nov 2025 from memory) and the current ACSP requirements.
5. **France:** detailed requirements for becoming a Plateforme Agréée (ISO 27001, hosting rules) and the FEC legal references (from prior knowledge).
6. **Germany:** the BMF letters of 15 Oct 2025 and 15 Oct 2026 (content and dates), and the exact EGAO transitional rule for the 8-year retention.
7. **Spain:** the publication date of the Ministerial Order that starts the RD 238/2026 clock, and whether the Verifactu delay to Oct 2028 has been enacted.
8. **Poland:** whether the KSeF penalty-deferral bill (UD477, Sejm print 3245) has passed; JPK_CIT phasing.
9. **Greece, Croatia, Slovakia, Latvia, Slovenia, Ireland, Portugal (QES), Bulgaria (SAF-T)** dates rest on secondary trackers only.
10. **Belgium e-reporting (1 Jan 2028):** draft; final scope (supplier only vs both parties) unconfirmed.
11. **PSD3/PSR:** plenary adoption, OJ publication and exact application dates.
12. **FIDA withdrawal:** based on a leaked draft work programme (MLex); the official 2027 Commission work programme was not checked.
13. **EU AI Act Digital Omnibus** regulation number (2026/1744) and OJ dates come from secondary sources; the EUR-Lex text was not read.
14. **UK DUAA s.80 commencement (5 Feb 2026, SI 2026/82)** comes from secondary sources.
15. **SWIFT structured-address extension** reported in Aug 2026: whether 14 Nov 2026 still stands.
16. **Peppol:** whether an EU PINT will replace BIS Billing 3.0; current (2026) fee schedule; whether OpenPeppol now requires ISO 27001 for all service providers (confirmed only for the Netherlands).
17. **EN 16931 revision** timing for ViDA.
18. **US items from prior knowledge:** IRS retention periods, FASB ASU 2024-03 and 2025-05 effective dates, CCPA ADMT rules, Colorado AI Act timing, Nacha "PAYROLL" and "PURCHASE" entry-description rules.
19. **Competitor details:** Xero manual-journal approvals; Xero's Melio acquisition and US bill pay status; NetSuite Bill Capture and AI connector specifics; Sage AI Gateway and MCP server; BC native NCI handling; Odoo consolidation app status; exact Sage Accounting UK prices; NetSuite, Sage Intacct, Rillet and Campfire prices.
20. **FRS 105** changes for micro-entities, and UK endorsement of IFRS 18.
21. **Retention periods for France, Italy, Spain and Poland** (from prior knowledge, not re-checked).
22. **Model availability in Google Cloud regions** (europe-west2, europe-west3, us-east5) for in-region AI inference: not researched; needed before any AI feature ships.
