# CLAUDE.md — Thapsus Finance (PROJECT_MULLAH)

Thapsus Finance is the accounting and finance product of the Thapsus suite: a multi-tenant platform for UK, EU
and US businesses from about 10 to 2,000 staff (SMB to mid-market). It is sold on its own and as part of the
suite with Thapsus-HR (`PROJECT_KAREN`), Thapsus CRM (`PROJECT_BOSSMAN`), Thapsus ERP (`PROJECT_KASONGO`) and
Thapsus Care (`ROCHE`). It holds **the only general ledger** in the suite.

## Status: planning

Nothing is built yet. The plan is in `docs/`. The build starts when the owner approves `docs/BUILD_BRIEF.md`.
Until then, only planning documents change. Read first: `docs/PLAN.md`, `docs/FEATURES.md`,
`docs/suite/SUITE_PLAN.md`.

## How to work in this repo

- Work the phases in `docs/PLAN.md` in order. One branch per phase (`phase-<n>-<slug>`), small Conventional
  Commits (`feat(ledger): …`, `test(tax): …`).
- Do not stop to ask the owner questions. Make the most reasonable choice, record it in `docs/DECISIONS.md`
  (dated ADR: context, decision, consequences) and carry on.
- Anything only a human can do (accounts, HMRC recognition, bank and payment partner contracts, Peppol, legal
  review, keys) goes in `docs/HUMAN_TODO.md` with exact numbered steps. Build against mocks or sandboxes meanwhile.
- **Always ask first before:** deploying to production, deleting any cloud resource or data, or adding
  recurring cost beyond the estimate in `docs/COSTS.md`.
- Never tick a PLAN item whose acceptance criteria are not proved by a test or a check in `pnpm verify`, or
  (for human-only items) by an entry in HUMAN_TODO. Report failures and skipped steps honestly.
- Suite documents in `docs/suite/` are identical in PROJECT_MULLAH, PROJECT_BOSSMAN and PROJECT_KASONGO. Change
  all three copies in one change until the `thapsus-platform` repository holds the single copy.

## Scope guardrails

Out of scope: acting as a bank or e-money issuer, holding client funds (payments go through regulated
partners), corporate cards and spend-management cards, lending, investment advice, running US payroll, tax
filing for individuals other than through MTD for Income Tax where planned, and native mobile apps before the
suite's native phase (ship an installable PWA).

## Non-negotiables

### Accounting integrity
- Double entry always: every journal balances per currency and in base currency, enforced by the database.
- Posted entries are immutable. Corrections are reversing entries, never edits or deletes.
- Periods and lock dates are enforced by the database; a closed period accepts nothing.
- Money is integer minor units plus an ISO 4217 code, never floating point. Rounding follows the rule packs,
  with rounding differences posted to a named account.
- Every ledger line traces to its source document, and every source document to its journal.
- Invoice numbering is gap-free where the jurisdiction requires it.
- Other products never choose ledger accounts. They send accounting events (`docs/suite/INTEGRATION.md` §6);
  posting profiles in Finance decide the accounts.

### Privacy and data residency
- Thapsus is the processor; customers are controllers. Comply with UK GDPR and the Data Protection Act 2018 (as
  amended by the Data (Use and Access) Act 2025), EU GDPR and US state privacy laws.
- Each tenant picks a home region: **UK = europe-west2**, **EU = europe-west3** (backups only in
  europe-west1), **US = us-east5** (backups only in us-west1). All its personal and financial data, files,
  backups, logs, search indexes, caches, queues and emails stay there. The map lives in one module and is
  enforced by tests for every region pair.
- Never use a multi-region location, Cloud CDN for authenticated traffic, Cloud Trace, Error Reporting or the
  `_Default` log bucket. Authenticated responses carry `Cache-Control: private, no-store`.
- The global layer holds no personal or financial data of customers' customers.
- No personal data to a third party unless it is listed in `docs/SUBPROCESSORS.md` (purpose and location per
  region) first.

### Security
- Tenant isolation with PostgreSQL row-level security, enabled and forced, proved by tests. The app role has no
  `BYPASSRLS` and owns no tables. Tenant-to-database routing is configuration.
- Bank details and payment instructions: changes need step-up, a second person's approval and a cooling-off
  period, and are verified (Confirmation of Payee or Verification of Payee) where available.
- MFA for every role that can post, approve or pay; SSO can be enforced; sessions expire; sign-in is rate-limited.
- Append-only audit log of every change and every view of sensitive data, exportable by customers.
- OWASP ASVS level 2; PCI DSS scope kept out (card data never touches Thapsus; partner-hosted fields only).
- Secrets only in Secret Manager. No service-account keys or long-lived cloud keys; GitHub uses workload identity.

### Quality
- WCAG 2.2 AA; works on low-cost phones; light and dark mode.
- No hard-coded user-facing strings (en-GB and en-US at launch).
- **Tax and statutory rules are never hard-coded from memory.** They are versioned, effective-dated data in rule
  packs, each value with its official source URL and the date it was checked, with golden tests.

### Design
- Build every screen from the suite design system (`docs/suite/DESIGN_SYSTEM.md`). Finance's accent is indigo.
  No ad-hoc colours, sizes, radii, shadows or durations. Never copy Apple assets; no SF Pro or SF Symbols.

### AI
- Every AI feature is off by default, goes through the suite AI gateway only, drafts for a person to approve,
  and never posts, pays or files on model output alone (`docs/suite/AI.md`).

## Engineering rules

- **Tests first** for posting rules, tax calculations, currency conversion, rounding, period close, tenant
  isolation and residency.
- Fictional test data only: invented companies and people, `example.invalid` emails, never real tax ids.
- All tenant-scoped queries run inside the tenant-context helper (`app.tenant_id` set per transaction).
- New tenant table checklist: `tenant_id uuid not null`, RLS enabled and forced, tenant policy, index leading
  with `tenant_id`, audit coverage.
- Instants as `timestamptz` (UTC); accounting dates as `date` in the entity's time zone; shared time utilities only.
- Nothing configurable is hard-coded: tax rates, thresholds, charts of accounts, posting profiles, prices,
  feature flags, the product domain.
- OpenAPI 3.1 first; `/v1`; contract tests. Domain events to the transactional outbox in the same transaction;
  jobs idempotent.
- Migrations expand-and-contract only; every migration runs in CI against real PostgreSQL.
- Shared logger and tracer scrub personal data. Never log bodies, names, emails, bank details, tax ids or tokens.
- One OpenTofu definition for staging and production; differences only in the drift allow-list with a reason.
  Production deploys only image digests that staging ran and smoke-tested.
- PostgreSQL full-text and `pg_trgm` for search unless load tests prove otherwise.
- Maintained, permissively licensed dependencies; lockfile pinned; `pnpm audit` passes at high severity.

## Commands (planned; implemented in Phase 0)

| Command | Purpose |
| --- | --- |
| `pnpm install` | Install workspace dependencies |
| `pnpm dev` | Run web, api and worker locally |
| `pnpm verify` | Full gate: lint, typecheck, unit, integration, contract, ledger invariants, tenant isolation, residency, e2e, visual regression, Lighthouse, security scan, build, OpenTofu checks; ends with a pass/fail summary |
| `pnpm db:migrate` / `pnpm db:seed` | Apply migrations / seed the fictional demo company |

## Key documents

`docs/BUILD_BRIEF.md` (draft scope) · `docs/PLAN.md` · `docs/FEATURES.md` · `docs/RESEARCH.md` ·
`docs/DECISIONS.md` · `docs/HUMAN_TODO.md` · `docs/suite/` (suite plan, integration, platform, Studio, AI, design)
