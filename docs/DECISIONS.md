# Decisions — Thapsus Finance

Dated architecture and product decisions. Format: context, decision, consequences. Suite-wide decisions are
summarised in `docs/suite/SUITE_PLAN.md` §2; this file records how they apply here.

## ADR-0001 — Owner decisions for the new products (2026-11-07)

**Context.** The owner asked for an accounting and finance product (codename PROJECT_MULLAH) as part of a suite
with Thapsus-HR, Thapsus Care, a CRM and an ERP, and answered seven scoping questions on 2026-11-07.

**Decision.**
- Markets: UK, EU and US home-region cells from day one; UK tax and compliance content at launch; EU and US
  content follow (D-1).
- Client size: SMB to mid-market, about 10 to 2,000 staff (D-2).
- Payroll: a UK payroll module in a later phase (D-4). Thapsus-HR keeps owning pay inputs and still never runs
  payroll.
- Design: the shared suite design system with Finance's own accent (D-5).
- Mobile: an installable PWA first; a native app later with the suite (D-6).
- Build order: the platform kit first, then Finance, CRM and ERP in parallel (D-7). ERP builds against a mock of
  Finance's ledger API, so Finance publishes its posting contract early (Phase 1).

**Consequences.** Tax content is per jurisdiction and effective-dated from the start, even though only the UK pack
is complete at launch. The ledger API and its AsyncAPI events are a Phase 1 deliverable because ERP depends on
them. UK payroll needs HMRC recognition (HUMAN_TODO), which has its own lead time.

## ADR-0002 — Stack and platform kit (2026-11-07)

**Context.** Thapsus-HR already runs the three-region cell model on Google Cloud with OpenTofu, forced RLS and
Cloud SQL; most shared code is extracted from it into the `PROJECT_ATLAS` repository (D-9, D-10).

**Decision.** TypeScript, pnpm and Turborepo; `apps/web` (Next.js App Router, installable PWA), `apps/api`
(Fastify, TypeBox route schemas as the OpenAPI 3.1 source, `/v1`), `apps/worker` (outbox, idempotent jobs, close
and reconciliation jobs); `packages/ledger` (the posting engine and invariants), `packages/rules` (tax and
statutory packs), `packages/db` (migrations, RLS policies); shared `@thapsus-suite/*` packages for everything in
`docs/suite/SUITE_PLAN.md` §4; OpenTofu cell and global roots from the platform modules.

**Consequences.** The product starts with HR's proven residency, isolation and deployment machinery. It depends
on the platform kit's release cadence (risk and mitigation in `SUITE_PLAN.md` §7).

## ADR-0003 — The only general ledger in the suite, fed by accounting events (2026-11-07)

**Context.** ERP, CRM and Care all create financial documents. Two ledgers in one organisation would disagree.

**Decision.** Finance holds the only general ledger per legal entity (D-12). Other products send accounting
events (`docs/suite/INTEGRATION.md` §6). **Posting profiles** in Finance, configurable in Studio, decide the
accounts from the event type, item or party posting groups, entity and dimensions. Finance posts balanced,
immutable journals, links each line to its source document, and reconciles each sub-ledger nightly.

**Consequences.** Customers change account mapping without code. ERP never needs ledger logic. The accounting
events API is versioned and contract-tested like any public API; the same events feed connectors to Xero,
QuickBooks Online, Sage and Business Central for customers who buy ERP without Finance.

## ADR-0004 — Ledger data model and invariants (2026-11-07)

**Context.** Financial misstatement is the worst failure mode of this product.

**Decision.**
- Journals and journal lines in append-only tables; posted rows are never updated or deleted (privileges plus a
  trigger, as HR's audit table). Corrections are reversing journals linked by `reverses_id`.
- A deferred constraint trigger checks that every journal balances in transaction currency and base currency
  before commit.
- Accounting periods per entity with `open`, `soft-closed` (only named roles can post) and `closed`; lock dates
  enforced in the database.
- Money as `bigint` minor units plus ISO 4217 code; exchange rates as exact decimals with their source and date;
  revaluation creates its own journals.
- Dimensions are typed (cost centre, department, project, location, customer, supplier, item, plus up to 20
  custom dimensions defined in Studio) and stored as columns or a validated `jsonb` set on each line, never encoded
  into account codes.
- Balances are derived from lines, with materialised period balances refreshed by an idempotent job and checked
  against the lines nightly.
- Gap-free document numbering is allocated in the posting transaction where a jurisdiction requires it.

**Consequences.** Property-based tests generate random postings and check the invariants (trial balance always
balances; reversing twice is identity; closed periods refuse writes). Reports read period balances for speed and
drill to lines.

## ADR-0005 — Tax and statutory rules as rule packs (2026-11-07)

**Context.** Tax rates, thresholds, filing formats and deadlines change. Thapsus-HR keeps employment rules as
versioned, effective-dated data with sources (its `packages/rules`).

**Decision.** The same framework (shared through the platform kit) holds tax packs: VAT and sales-tax rates and
rules, reverse-charge cases, thresholds, filing deadlines, e-invoicing formats, chart-of-accounts templates,
mileage and expense rates, and (later) payroll rates. Each value carries its official source URL and the date it
was checked, and golden tests cover each pack. Packs are editable in the back office with an audit trail.

**Consequences.** No tax value appears as a literal in code; a test scans for them. A law change is a data release.

## ADR-0006 — Money moves only through regulated partners (2026-11-07)

**Context.** Bank feeds, payment initiation, direct debits and card collection are regulated activities.

**Decision.** Thapsus never holds client funds and is not itself an account information or payment initiation
provider. Bank feeds and payment initiation go through a regulated open-banking provider per region; collections
through payment partners (direct debit and card); supplier payments by bank file or the partner's API. Every
partner is listed in `docs/SUBPROCESSORS.md` before integration. Card data never touches Thapsus (partner-hosted
fields), keeping PCI DSS scope minimal.

**Consequences.** Contracts and DPAs with partners are on the critical path (HUMAN_TODO). Each partner sits behind
a port with a contract-tested mock so a partner can be replaced per region.

## ADR-0007 — E-invoicing on one semantic model (2026-11-07)

**Context.** EU member states are introducing e-invoicing and e-reporting at different dates and in different
formats; the UK has announced its own mandate; Peppol is the common network (dates in `docs/RESEARCH.md`).

**Decision.** Every invoice and credit note is stored in a model aligned with EN 16931 from Phase 2, so any
syntax (Peppol BIS Billing 3.0 UBL, CII, XRechnung, Factur-X, FatturaPA, KSeF FA(3)) can be produced from it.
Delivery and clearance go through a certified Peppol and clearance partner behind an e-invoicing port; becoming a
Peppol service provider ourselves is reconsidered when volume justifies it.

**Consequences.** EU countries are added one by one as rule-pack data plus a syntax mapper, with coverage published
per country.

## ADR-0008 — UK payroll later, with Thapsus-HR keeping pay inputs (2026-11-07)

**Context.** The owner chose UK payroll in a later phase (D-4). Thapsus-HR already produces an append-only,
hashed pay-period snapshot and never runs payroll.

**Decision.** Finance's payroll module (Phase 15 of `docs/PLAN.md`) consumes HR's period snapshot through a new
"Thapsus Finance payroll" connector in HR (`docs/suite/INTEGRATION.md` §6a), or a CSV import for customers without
Thapsus-HR. It calculates pay, files RTI, handles pensions auto-enrolment, returns payslips to HR for publishing,
and posts the payroll journal.

**Consequences.** HMRC RTI recognition, pension-provider integrations and payroll golden tests are required
before launch of that module.

## ADR-0009 — AI features are optional and draft-only (2026-11-07)

**Context.** The owner wants AI features available but optional (D-13).

**Decision.** Every AI feature in `docs/FEATURES.md` §AI is off by default at three levels (Thapsus availability per
region, tenant owner, feature), runs only through the suite AI gateway on regional Vertex AI endpoints, and
produces suggestions or drafts that a person accepts. Nothing posts, pays, files or sends on model output alone.
Every feature has a deterministic path that works without AI.

**Consequences.** AI matching, capture and commentary are additive. Their evaluation suites run in CI and a
failure blocks release of that feature.

## ADR-0010 — The US cell runs in us-central1 (Iowa) (owner decision, 2026-11-07)

**Context.** The US cell was planned in us-east5 (Columbus). Cloud Scheduler, Cloud Tasks and single-region Document
AI are not offered there, which the suite needs. The owner allowed the US cell to run in any compatible US region
(suite decision D-16).

**Decision.** The US cell runs in **us-central1 (Iowa)**; backups and replicas stay in **us-west1 (Oregon)**. It is
the only US region with every service this product uses, checked on Google's pages on 2026-11-07
(`docs/suite/PLATFORM.md` §5.1a, `docs/suite/RESEARCH_PLATFORM.md` addendum). Generative AI in the US cell uses
Gemini in us-central1, or Claude on the us-east5 regional endpoint, which is the only US region serving Claude
regionally. Google processes requests to every US regional endpoint within the US multi-region, so the data stays in
the United States either way.

**Consequences.** The region map, the residency tests (every region pair) and the US settings files use us-central1
and us-west1 from Phase 0. The residency guard allows us-east5 for the AI gateway's Claude endpoint only, and refuses
it for every other resource. The US folders' organisation policy is `in:us-central1-locations` and
`in:us-west1-locations`.
