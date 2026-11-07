# BUILD BRIEF (DRAFT): Thapsus Finance, an accounting and finance platform by Thapsus

> **Draft for the owner's approval, prepared on 2026-11-07** from the owner's request and the decisions recorded in
> `suite/SUITE_PLAN.md` §2. When approved, it becomes the source of truth for scope, in the same way as
> `PROJECT_KAREN/docs/BUILD_BRIEF.md` is for Thapsus-HR. Until then nothing is built.

You are the lead engineer building Thapsus Finance (codename PROJECT_MULLAH), a production-ready, multi-tenant
accounting and finance platform for businesses of about 10 to 2,000 staff in the United Kingdom, the European Union
and the United States. It holds the only general ledger in the Thapsus suite and must work with Thapsus-HR, Thapsus
Care, Thapsus CRM and Thapsus ERP when a customer buys more than one.

First: read `CLAUDE.md`, `docs/PLAN.md`, `docs/FEATURES.md` and `docs/suite/`, then show the plan for Phase 0 and
wait for the owner to start the build.

## 1. Product position

- Mid-market depth at SMB simplicity: unlimited dimensions, real multi-entity and consolidation, partial exemption,
  FRS 102 lease and revenue schedules, close management and proper controls, with transparent pricing and calm design.
- UK first (D-1): VAT with Making Tax Digital, CIS, year-end accounts with iXBRL and CT600, and Peppol e-invoicing ahead
  of the UK mandate. EU and US packs follow on the same ledger.
- The suite's ledger (D-12): ERP, CRM and Care post through one accounting-events API, so customers of several Thapsus
  products have one set of books.
- Out of scope: holding client funds or being a bank, card issuing and spend cards, lending, investment advice, US
  payroll, individual tax returns, a full practice-management system (integrate first), native apps before the
  suite's native phase. UK payroll comes in a later phase (D-4).

## 2. Non-negotiables

As in `CLAUDE.md`: accounting integrity (double entry, immutable postings, database-enforced periods, minor units,
gap-free numbering where required, traceability both ways); privacy and data residency (home regions UK
europe-west2, EU europe-west3, US us-central1); security (forced RLS, bank-detail change control, MFA, audit, ASVS L2,
PCI scope kept out, no keys); quality (WCAG 2.2 AA, i18n, tax rules as sourced and effective-dated data); the suite
design system with Finance's accent; optional, draft-only AI.

## 3. Scale targets (prove with load tests; record in docs/PERFORMANCE.md)

- One tenant with 20 legal entities, 5 million journal lines a year, 200,000 invoices and 1 million bank lines a year.
- 300 concurrent users per tenant; a payment run of 10,000 lines within 1 minute; a year's trial balance within 2
  seconds; a monthly close for 20 entities with consolidation within 15 minutes of background work.
- p95 API latency under 300 ms for common reads and under 1 second for paginated lists.
- 99.9% availability; RPO 15 minutes; RTO 4 hours.

## 4. Architecture and stack

As in `docs/DECISIONS.md` ADR-0002 and `suite/PLATFORM.md`: TypeScript, pnpm and Turborepo; Next.js PWA; Fastify
with TypeBox and OpenAPI 3.1; PostgreSQL 17 on Cloud SQL with forced RLS and ledger invariants in the database; the
platform kit (`@thapsus-suite/*`); Cloud Run; a PostgreSQL-backed job queue with an in-cell scheduler; regional
Pub/Sub for suite events; OpenTofu cells from the platform modules; staging equals production.

## 5. Users and roles

Thapsus back-office staff; company owner; finance director or controller; accountant; bookkeeper; AP clerk; AR and
credit controller; payroll administrator (later phase); budget holder and approver; employee (expenses only);
external accountant (practice edition); auditor (read-only, time-boxed); customer portal user. Custom roles with
field-level rules (bank details, salaries), scoped by entity, location or fund. Approvers and expense claimants are
light users.

## 6. Features

`docs/FEATURES.md` is the scope: chart of accounts and dimensions; general ledger; multi-entity and consolidation;
multi-currency; sales, receivables and credit control; purchases and payables; banking and cash; expenses, assets and
leases; revenue, budgets and projects; tax; close, reporting and audit; statutory filing and the practice edition;
e-invoicing; UK payroll (later); optional AI; Studio configuration; integrations.

## 7. Delivery phases (complete in order; lanes may run in parallel where `PLAN.md` allows)

0 Foundations and the published posting contract · 1 Ledger core · 2 Sales, receivables and credit control · 3
Purchases, payables and payment runs · 4 Banking · 5 UK tax · 6 Reporting, budgets and close · 7 Expenses, assets and
leases (release F1) · 8 Multi-entity and consolidation · 9 Revenue and subscriptions (release F2) · 10 UK year-end and
practice edition (F3) · 11 E-invoicing and the EU pack · 12 US pack · 13 UK e-invoicing and Peppol accreditation · 14
Optional AI · 15 UK payroll · 16 Suite links live · 17 Scale and launch readiness. Acceptance criteria per phase:
`docs/PLAN.md`.

## 8. Engineering rules

As in `CLAUDE.md`. In particular: tests first for posting rules, tax, currency, rounding, close, isolation and
residency; property-based tests for ledger invariants; fictional data only; `pnpm verify` green at the end of every
phase with the summary printed; human-only steps in `docs/HUMAN_TODO.md`; ask before production deploys, deletions or
new recurring cost.

## 9. Documentation

README, ARCHITECTURE (with diagrams), DESIGN (pointing to the suite design system), SECURITY, DECISIONS, COSTS,
PERFORMANCE, COVERAGE (tax and e-invoicing by country), SUBPROCESSORS, COMPLIANCE (SOC 1, SOC 2, ISO 27001 mapping),
HUMAN_TODO, API docs (including the accounting-events contract), runbooks, DPIA template, records of processing,
admin, accountant and user guides, and a migration guide from Xero, QuickBooks Online and Sage.

## 10. Definition of done

Every phase ticked in `docs/PLAN.md` with evidence; `pnpm verify` green; HMRC recognition in place for each HMRC
service used; staging deployed in each region before its production cell; smoke and load tests meeting §3; production
defined and ready to deploy on the owner's approval; all §9 documents present; every remaining human step in
`docs/HUMAN_TODO.md`.
