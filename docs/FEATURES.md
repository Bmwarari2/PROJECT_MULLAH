# Feature catalogue — Thapsus Finance

Researched against Xero, QuickBooks Online and Advanced, Intuit Enterprise Suite, Sage Intacct, Sage Accounting and
50, Oracle NetSuite, Microsoft Dynamics 365 Business Central, FreeAgent, Zoho Books, Odoo, and the AI-native ledgers
Digits, Rillet and Campfire (`RESEARCH.md` §1, with which leaders have each feature). **Tier**: Core (launch, all paid
plans), Advanced (higher SMB or mid-market tier), Enterprise (top tier or add-on). **Phase**: see `PLAN.md`.
Everything marked configurable is set in Studio (`suite/STUDIO.md`) without code.

## 1. Chart of accounts and dimensions

| Feature | Tier | Phase |
| --- | --- | --- |
| Hierarchical chart with account types mapped to statutory lines (UK Companies Act, FRS 102 and 105, US GAAP, IFRS) | Core | 1 |
| Jurisdiction chart templates as rule-pack data (UK, US; DE SKR03 and SKR04, FR PCG, ES PGC, PL, IT in the EU pack) | Core / Advanced | 1, 11 |
| Unlimited typed dimensions (cost centre, department, location, project, customer, supplier, item, employee, funder, custom) | Core (10) / Advanced (unlimited) | 1 |
| Dimension hierarchies, roll-ups and effective-dated values | Advanced | 1 |
| Dimension validation rules at posting ("accounts 6000–6999 need a project") | Advanced | 1 |
| Default dimensions from master data, including employees from Thapsus-HR | Core | 1, 16 |
| Dimension-level access (users see only their entities, locations or funds) | Enterprise | 1 |
| Statistical accounts (headcount, floor area, beds, bed-days) | Advanced | 1 |
| Cash and accrual reporting from one ledger | Core | 6 |
| Multi-book (local GAAP, IFRS, tax, management) | Enterprise | 8 |
| Account mapping to tax-return boxes | Core | 5 |

## 2. General ledger and journals

| Feature | Tier | Phase |
| --- | --- | --- |
| Manual journals with attachments, notes and dimensions; import with preview | Core | 1 |
| Recurring, reversing and auto-reversing journals; templates and standard narratives | Core | 1 |
| Journal approval (maker-checker, thresholds) | Advanced | 1 |
| Allocations: fixed percentage, statistical driver, step-down, recurring | Advanced | 1 |
| Period locks (soft and hard; per entity and module); posting into closed periods by permission with reason | Core / Advanced | 1 |
| Immutable posted entries, corrections only by reversal, hash-chained audit | Core | 0, 1 |
| Real-time sub-ledger posting through the accounting-events API with posting profiles | Core | 1 |
| Opening balances and migration from Xero, QuickBooks Online and Sage | Core | 1 |
| Year-end close entries and retained-earnings roll-forward | Core | 6 |

## 3. Multi-entity, intercompany and consolidation

| Feature | Tier | Phase |
| --- | --- | --- |
| Many organisations under one sign-in (separate books) | Core | 1 |
| True multi-entity ledger with shared masters | Advanced | 8 |
| Automatic due-to and due-from; paired intercompany invoices | Advanced | 8 |
| Intercompany reconciliation and mismatch reports | Enterprise | 8 |
| Consolidation with eliminations | Advanced | 8 |
| Currency translation (current-rate method, CTA) | Advanced | 8 |
| Non-controlling interest, partial ownership, indirect holdings | Enterprise | 8 |
| Several consolidation structures; group close dashboard; drill-down | Enterprise / Advanced | 8 |
| Shared services (pay or receive on behalf of another entity) | Advanced | 8 |

## 4. Multi-currency

| Feature | Tier | Phase |
| --- | --- | --- |
| Foreign-currency invoices, bills, bank accounts and contacts | Core | 1–4 |
| Daily rates with source and rate type (spot, month-end, average, historical, budget) | Core / Advanced | 1 |
| Realised and unrealised gains and losses; period-end revaluation | Core | 1 |
| Additional reporting currency | Advanced | 8 |
| Rate override per transaction with audit | Core | 1 |

## 5. Sales, receivables and credit control

| Feature | Tier | Phase |
| --- | --- | --- |
| Customer financial fields (terms, credit limit, tax ids, Peppol id, channel, language, currency) | Core | 2 |
| Quotes, pro-formas, invoices, credit notes on an EN 16931-aligned model; gap-free numbering per series | Core | 2 |
| Repeating invoices; deposits, prepayments, retentions | Core / Advanced | 2 |
| Progress and milestone billing | Advanced | 9 |
| Subscription and usage billing | Advanced / Enterprise | 9 |
| Customer statements; reminders and dunning; statutory late-payment interest as data | Core | 2 |
| Collections workspace (worklists, promises to pay, disputes, drafted emails) | Advanced | 2 |
| Customer portal (view, pay, download, dispute) | Advanced | 2 |
| Payment links: card (hosted fields), Direct Debit, pay by bank and UK commercial VRP, ACH (US pack) | Core | 2, 12 |
| Cash application with suggested matches | Advanced | 2 |
| Credit limits, holds and order blocks shared with CRM and ERP | Advanced | 2, 16 |
| Bad-debt write-off with VAT bad-debt relief | Core | 2, 5 |
| Structured e-invoice output (Peppol BIS 3.0, XRechnung, Factur-X, KSeF, FatturaPA) | Core where mandated | 11, 13 |
| Invoice designer, branding, multi-language | Core | 2 |
| Aged receivables and DSO | Core | 2 |

## 6. Purchases and payables

| Feature | Tier | Phase |
| --- | --- | --- |
| Supplier master with verified bank-detail changes (step-up, second approver, cooling-off, Confirmation or Verification of Payee) | Core | 3 |
| Document inbox (email, upload, phone camera); bill entry; structured e-invoices preferred | Core | 3 |
| Light purchase orders and two- and three-way matching (ERP facts when linked) | Advanced | 3 |
| Approval workflows (amount, dimension, category, supplier risk), delegation, mobile approval | Advanced | 3 |
| Supplier portal (onboarding, bank-detail self-service with verification, invoice submission, status) | Enterprise | 3 |
| Payment runs; bank files (Faster Payments, Bacs Standard 18, ISO 20022 pain.001, NACHA in the US pack) | Core / Advanced | 3, 12 |
| Payment initiation through a regulated partner | Advanced | 3 |
| Remittance advices; early-payment discounts; supplier statement reconciliation | Core / Advanced | 3 |
| Duplicate bill detection | Core | 3 |
| 1099 tracking, TIN collection and IRIS e-filing | Core (US) | 12 |
| CIS deductions, CIS300 returns (including nil returns), verification, statements | Core (UK construction) | 5 |
| UK payment-practices reporting data | Enterprise | 3 |

## 7. Banking and cash

| Feature | Tier | Phase |
| --- | --- | --- |
| Bank feeds through a regulated aggregator per cell | Core | 4 |
| Statement import (CSV, OFX, QIF, MT940, camt.053, BAI2) | Core | 4 |
| Bank rules with test-on-history preview | Core | 4 |
| Reconciliation with suggested matches (1:1, 1:n, n:1, partial); cash coding; reconciliation report | Core | 4 |
| Payment-processor clearing (Stripe, GoCardless, PayPal payouts net of fees) | Core | 4 |
| Card and petty-cash accounts; transfers | Core | 4 |
| Positive pay (US); lockbox import | Advanced / Enterprise | 12 |
| Short-term cash forecast from due dates, recurring items and balances | Core | 6 |
| Scenarios and 13-week driver-based forecast | Advanced | 6 |
| Multi-entity cash positioning; bank-account register; loan schedules and covenants | Advanced | 8 |

## 8. Expenses, assets and leases

| Feature | Tier | Phase |
| --- | --- | --- |
| Expense claims with phone receipt capture, multi-line, multi-currency | Core | 7 |
| Mileage at statutory rates (rule-pack data) | Core | 7 |
| Approval from the HR reporting line; policy rules | Core / Advanced | 7 |
| Reimbursement in payment runs (employee bank details from HR's vault) | Core | 7 |
| Card-statement import and receipt matching (no card issuing) | Core | 7 |
| Fixed-asset register, depreciation methods, disposals, revaluation | Core | 7 |
| Capitalise from bill lines; multiple depreciation books; UK capital allowances | Advanced / Enterprise | 7 |
| Lessee lease accounting: FRS 102 s20 (from 1 Jan 2026), IFRS 16, ASC 842 | Advanced | 7 |
| Lessor accounting | Enterprise | Later |

## 9. Revenue, budgets and projects

| Feature | Tier | Phase |
| --- | --- | --- |
| Deferred revenue and expense schedules | Advanced | 9 |
| Contract revenue engine (IFRS 15, ASC 606, FRS 102 s23) | Enterprise | 9 |
| SaaS metrics (ARR, NRR, churn, cohorts) | Advanced | 9 |
| Budgets by account, dimension and period with versions, approval and locking | Core / Advanced | 6 |
| Rolling and driver-based forecasts; headcount planning from HR (aggregated) | Enterprise | 6, 16 |
| Excel and Google Sheets round-trip | Advanced | 6 |
| Encumbrance (budget check on POs) | Enterprise | 8 |
| Light projects (dimension, time from HR, T&M and milestone billing, profitability) | Advanced | 9 |

## 10. Tax

| Feature | Tier | Phase |
| --- | --- | --- |
| UK VAT codes and 9-box return; Making Tax Digital for VAT with fraud-prevention headers | Core | 5 |
| VAT schemes (cash, flat rate, annual); domestic reverse charge; postponed import VAT; bad-debt relief; fuel scale charges | Core | 5 |
| Partial exemption (standard method, de minimis, annual adjustment) | Advanced | 5 |
| Capital Goods Scheme | Enterprise | Later |
| EU VAT per country, OSS and IOSS, intra-EU reverse charge, triangulation | Advanced | 11 |
| US sales and use tax through a tax engine; exemption certificates; nexus monitoring | Core (US) | 12 |
| Withholding tax | Advanced | 11, 12 |
| UK corporation tax (CT600 with iXBRL computations; capital allowances) | Advanced | 10 |
| Tax content as versioned, sourced rule-pack data | Core | 0 |

## 11. Period close, reporting and audit

| Feature | Tier | Phase |
| --- | --- | --- |
| Close checklist with owners, dependencies and sign-off | Advanced | 6 |
| Balance-sheet reconciliations with preparer and reviewer | Advanced | 6 |
| Accrual and prepayment schedules | Advanced | 6 |
| Flux and variance analysis; audit-ready close pack | Advanced | 6 |
| P&L, balance sheet, cash flow, trial balance, GL detail, aged AR and AP | Core | 1–6 |
| Drill-down from every figure to its source document, in any Thapsus product | Core | 6 |
| Report builder; management packs; dashboards per role | Advanced / Core | 6 |
| Statutory layouts including IFRS 18 (from 2027) | Advanced | 6 |
| Exports and live Excel connection (OData) | Core / Advanced | 6 |
| Change history per record; auditor role; exportable audit log | Core | 0 |
| Tax-audit exports: GoBD, FEC, SAF-T variants, DATEV | Advanced | 11 |
| Segregation-of-duties conflict reports, access reviews, SOX evidence | Enterprise | 12 |

## 12. Statutory filing and the practice edition

| Feature | Tier | Phase |
| --- | --- | --- |
| UK accounts production (FRS 105, FRS 102 s1A, abridged, filleted) with iXBRL and Companies House software filing | Advanced | 10 |
| Companies House identity-linked filing for agents | Advanced | 10 |
| US 1099 and 1096 through IRIS; state 1099 filing | Core (US) | 12 |
| Free accountant access; multi-client dashboard; client templates; wholesale billing | Core (practice edition) | 10 |

## 13. E-invoicing and documents

| Feature | Tier | Phase |
| --- | --- | --- |
| Per-tenant inbound email address and upload; classification | Core | 3 |
| Structured e-invoice receipt and validation (all major syntaxes) | Core where mandated | 11 |
| Peppol send and receive through a certified partner; national platforms (France, Poland, Italy) | Core in mandated countries | 11 |
| UK Peppol e-invoicing (mandatory from 1 Apr 2029) and accreditation | Core (UK) | 13 |
| Country archiving rules (original format, immutable, retention) | Core | 11 |
| Documents linked to transactions; full-text search | Core | 3 |

## 14. Payroll (later phase, UK)

| Feature | Tier | Phase |
| --- | --- | --- |
| Gross to net (PAYE, National Insurance, student and postgraduate loans) from HR's pay-period snapshot | Advanced | 15 |
| Statutory payments; pension auto-enrolment with provider integrations | Advanced | 15 |
| RTI (FPS, EPS) to HMRC; P60; year-end | Advanced | 15 |
| Payslips returned to HR for publishing; payroll journal; net-pay and liability payments | Advanced | 15 |
| Payroll journal import from any other payroll (always available) | Core | 1 |

## 15. Optional AI features (off by default, drafts only)

| Feature | Human step | Phase |
| --- | --- | --- |
| Bank-match suggestions; auto-match above a tenant-set confidence per bank account | Review queue, undo, explanation | 14 |
| Coding suggestions (account, tax code, dimensions, contact) | Accept with the VAT impact shown | 14 |
| Bill, receipt and statement capture | Draft with per-field confidence | 14 |
| Duplicate and anomaly flags; supplier bank-detail fraud alerts | Alerts; payment line held for a second approver | 14 |
| Cash application from remittances | Suggested allocations, batch approve | 14 |
| Cash-flow forecast with drivers and ranges | Shown as an estimate | 14 |
| Close assistant: draft accruals and prepayments into an approval queue | Never posts | 14 |
| Variance commentary drafts | Editor accepts | 14 |
| Collections email drafts and prioritisation (advisory for natural persons) | Drafts only | 14 |
| Questions in plain language over governed reports, with a snapshot of the data used | Read-only | 14 |
| Contract-to-revenue and lease extraction into draft schedules | Accountant approves | 14 |
| VAT return review before submission | Person submits | 14 |
| Migration mapping assistant | Mapping reviewed | 14 |
| Configuration copilot (drafts Studio changes in a sandbox) | Admin publishes | 14 |
| MCP server with read and draft-only tools | Drafts approved in Finance | 14 |
| **Never**: credit scoring of natural persons; scoring employees from expense behaviour | — | — |

## 16. Configuration without code (Studio)

Custom fields and objects; unlimited dimensions with posting rules; validation rules ("account 6xxx needs a
project"); approval workflows (amount, dimension, supplier, category; serial and parallel; delegation; reminders);
document templates (invoice, credit note, statement, remittance, PO) with conditional blocks and languages; numbering
series per entity and document type with gap-free option; report builder and dashboards; bank and coding rules with
preview; dunning sequences and email templates; custom roles with field-level rules for bank details; close checklist
templates; posting profiles for accounting events; tax configuration within what the law allows; import mappings;
sandboxes with promotion and rollback.

## 17. Integrations customers expect

Payments in (Stripe, GoCardless, PayPal, Adyen; pay by bank); payments out (bank files, payment initiation, Wise,
Airwallex); commerce settlement (Shopify, Amazon, WooCommerce, eBay); capture imports (Dext, Hubdoc, AutoEntry);
spreadsheets (Excel add-in, Google Sheets, OData); practice software and DATEV; tax engines (US); Peppol partners;
FP&A tools; spend tools (Pleo, Spendesk, Ramp, Brex feeds); billing tools (Stripe Billing, Chargebee); payroll (the
Thapsus-HR connectors plus other payroll software journals); platform (REST API, webhooks, SCIM, SSO, Zapier and
Make, MCP).
