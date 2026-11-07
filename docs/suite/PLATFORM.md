# Thapsus suite platform (plan)

> Suite document, kept identical in `PROJECT_MULLAH`, `PROJECT_BOSSMAN` and `PROJECT_KASONGO` until the
> shared `thapsus-platform` repository exists (HUMAN_TODO H-S1). Change all three copies in the same change.
> Status: plan for owner approval, 2026-11-07. Research: `RESEARCH_PLATFORM.md`.

This document covers what every product shares below the business features: the platform repository,
identity, tenancy, security, hosting on Google Cloud, compliance, billing and operations. It is based on what
Thapsus-HR already runs (its `ARCHITECTURE.md`, ADR-0214 to ADR-0248) and what Thapsus Care decided (ADR-0002,
ADR-0098).

## 1. The platform repository

- **Repository**: `thapsus-platform` (to be created, H-S1), a pnpm and Turborepo monorepo of `@thapsus-suite/*`
  packages (list in `SUITE_PLAN.md` §4), OpenTofu modules and the Thapsus ID and suite services.
- **Publishing**: a private npm repository in Artifact Registry (europe-west2). CI publishes with workload identity
  federation; products install through the same route. No tokens or keys in any repository.
- **Releases**: changesets, semantic versioning, weekly releases, a changelog per package. Products pin versions
  and upgrade through Renovate pull requests that must pass their own `pnpm verify`.
- **Compatibility tests**: the platform runs each product's contract tests against a release candidate before it
  is published (a product registers its test command), so a platform change cannot silently break a product.

## 2. Identity: Thapsus ID

People should sign in once and move between the products their organisation uses.

- **What it is.** A regional identity service per cell, built from Thapsus-HR's authentication module (passwords,
  TOTP, passkeys, magic links, SAML 2.0 and OpenID Connect single sign-on, SCIM 2.0, verified domains, session
  rules, sign-in rate limits and lockout, step-up). It holds people's sign-in data only in their home region.
- **What it is not.** It is not an identity provider for customers' other applications. That stays out of scope,
  as Thapsus-HR's scope guardrails say. Thapsus ID serves Thapsus products only.
- **Federation.** Customers that use Microsoft Entra ID, Google Workspace, Okta or another provider connect it once
  to Thapsus ID; every Thapsus product then uses it. SCIM provisioning arrives once at Thapsus ID and is passed to
  each product the person is entitled to.
- **Products as relying parties.** Each product trusts Thapsus ID over OpenID Connect within the cell and keeps its
  own session, roles and permissions. Authorisation never moves to Thapsus ID.
- **One sign-out.** Signing out, an admin ending a session, or a leaver's last day ends the person's sessions in
  every product (back-channel logout).
- **Adoption.** The new products use Thapsus ID from Phase 0. Thapsus-HR and Thapsus Care add it as one more sign-in
  method (their plans: `PROJECT_KAREN/docs/SUITE_INTEGRATION_PLAN.md`, `ROCHE/docs/plans/thapsus-suite.md`) and
  keep their own methods for customers that use only them.
- **Rules carried over.** MFA mandatory for admin roles and for anyone who can post, pay, approve or export;
  customers can enforce SSO; idle and absolute session limits; sign-in throttling; NCSC password guidance
  (12+ characters, deny-list, no forced expiry).

## 3. Organisations, tenants and the directory

- **Suite organisation**: the customer. Lives in the global directory (europe-west2) with its id, slug, home
  region, status, plan and entitlements per product, and verified domains. It holds no personal data.
- **Workspace**: one per product per organisation, in the organisation's home cell. Each product's tenant id is
  its own; the workspace records the suite organisation id.
- **Legal entities and locations** live inside the products (Finance owns legal entities in the suite;
  `INTEGRATION.md` §4).
- **Routing**: "find my workspace" and sign-in resolve the organisation's region through the global directory,
  then everything happens in the cell, exactly as Thapsus-HR's sign-in routing works today. A request for an
  organisation homed in another cell gets `421` with the right address, and no data is read.
- **One home region per organisation.** All of an organisation's products live in the same cell, so links never
  cross cells. A Thapsus Care customer is always in the UK cell.

## 4. Data layer and security (every product)

| Rule | How |
| --- | --- |
| Tenant isolation | PostgreSQL row-level security enabled and forced on every tenant table; the app role has no `BYPASSRLS` and owns no tables; `app.tenant_id` set per transaction with `set_config(..., true)`; composite keys `(tenant_id, id)` so rows cannot point across tenants; an isolation suite that enumerates every table and fails if one has no policy |
| Large tenants | Tenant-to-database routing is configuration; a tenant can move to a dedicated database with the move tool and no code change |
| Reporting | Read replicas with a reporting role (`SELECT` only, same RLS) |
| Sensitive data | A separate vault table with field-level envelope encryption under the cell's Cloud KMS key, every decryption audited (HR's vault). Used for bank details, tax identifiers, payroll data and anything a product classes as sensitive |
| Audit | Append-only, sealed with a per-tenant hash chain; every change and every view of sensitive data; customer export and SIEM streaming |
| Files | Cloud Storage with CMEK in the cell, uniform access, no public or signed access, malware scanning, downloads streamed through the API and audited |
| Secrets | Secret Manager, replicated only in the cell's region; no service-account keys; GitHub to Google Cloud through workload identity federation; cells to AWS (SES only) through STS web identity |
| Network | Private IP Cloud SQL with deletion protection; regional external Application Load Balancer with Cloud Armor per cell; no CDN or edge cache for authenticated traffic; `Cache-Control: private, no-store` |
| Application security | OWASP ASVS 5.0 level 2; SSRF guard on every outbound call; idempotency keys; rate limits per tenant; held exports with second-admin approval (HR ADR-0246) |
| Supply chain | Lockfiles pinned, minimum release age for new versions, approved install scripts only, `pnpm audit` at high severity, secret scanning, SBOM and provenance for images |

## 5. Hosting on Google Cloud

### 5.1 Cells and regions

| Cell | Region | Backups and replicas | Notes |
| --- | --- | --- | --- |
| UK | europe-west2 (London) | None outside London; across zones only | Every product, including Care |
| EU | europe-west3 (Frankfurt) | europe-west1 (Belgium) only | Finance, CRM, ERP, HR |
| US | us-east5 (Columbus, Ohio) | us-west1 (Oregon) only | Finance, CRM, ERP, HR |
| Global layer | europe-west2 | — | Suite directory, suite billing, marketing sites; no personal data |

The organisation policy `gcp.resourceLocations` is set per cell folder so that a resource elsewhere cannot be
created. The same map lives in `@thapsus-suite/core` and every Google client goes through the residency guard.
Production cells for EU and US are defined in code from day one, but deployed only when the first customer in
that region signs (D-1, and to avoid recurring cost before it is needed).

### 5.2 Projects

One Google Cloud project per product per environment per cell (for example `thapsus-finance-prod-uk`), under a
folder per environment and cell that carries the location policy, plus one **suite project** per environment and
cell for the shared services (Thapsus ID, the suite event topics, the AI gateway, the mail service). Separate
projects give each product its own blast radius, budget and IAM boundary.

### 5.3 Services per product cell

| Need | Service | Notes |
| --- | --- | --- |
| Web, API, worker | Cloud Run services (web and API split into separate services with their own service accounts, the follow-up HR recorded in ADR-0248) | Minimum instances set per product; the worker's drain endpoint is called by Cloud Scheduler and Cloud Tasks |
| Database | Cloud SQL for PostgreSQL 17, Enterprise Plus in production with regional high availability, point-in-time recovery, private IP, CMEK, deletion protection; one instance per product per cell | AlloyDB remains the upgrade path if a cell outgrows Cloud SQL |
| Jobs and schedules | Cloud Tasks and Cloud Scheduler (regional) driving idempotent jobs; the transactional outbox in PostgreSQL | |
| Suite events | Pub/Sub topics with a message storage policy limited to the cell's region, published and consumed through regional endpoints | `INTEGRATION.md` §3 |
| Files | Cloud Storage, regional buckets, CMEK | |
| Keys and secrets | Cloud KMS (regional key ring), Secret Manager (regional secrets) | |
| Logs and metrics | Cloud Logging with regional log buckets (never `_Default`), Cloud Monitoring, OpenTelemetry with personal data scrubbed | No Cloud Trace or Error Reporting |
| Email | Amazon SES in the home region through keyless STS (HR ADR-0219), behind the suite mail service | CRM's marketing email uses separate sending domains and configuration sets |
| AI | Vertex AI regional endpoints only, through the AI gateway | `AI.md` |
| Edge | Regional external Application Load Balancer, Cloud Armor, Certificate Manager | |

Facts about regional availability of each Google service, checked on 2026-11-07, are in
`RESEARCH_PLATFORM.md` §3, including the services that exist only as multi-region or global and are therefore
not used.

### 5.4 One definition, staging equals production

As in Thapsus-HR (ADR-0214 to ADR-0226): one OpenTofu root per layer (`infra/gcp/cell`, `infra/gcp/global`) that
deploys every environment and cell from `infra/gcp/envs/<env>-<cell>.tfvars.json`; a drift test that allows only
listed differences (size, redundancy, retention, domain, seed data); production deploys only image digests that
staging ran and smoke-tested; a plan guard that refuses to delete or replace a stateful resource unless the
reviewed run names it; one reusable deploy workflow with the global layer first. The modules move to the platform
repository so all five products share them.

### 5.5 Recovery

RPO 15 minutes and RTO 4 hours for every product (point-in-time recovery plus regional high availability; EU and
US backups copied to their permitted backup regions; UK across London zones only). Restore and zone-failure
drills each quarter, recorded in each product's runbooks.

## 6. Compliance programme (suite-wide)

| Item | Plan |
| --- | --- |
| Data protection | Processor terms per region (UK, EU, US), sub-processor lists per product and region, records of processing, DPIA template, subject-request tooling, retention engine, breach log with the 72-hour clock |
| EU Data Act | Every product offers full data export in documented, machine-readable formats and a switching process with no switching charges (`RESEARCH_PLATFORM.md` §6) |
| Security certification | SOC 2 Type II and ISO 27001:2022 for the suite (one information security management system, one set of controls), Cyber Essentials Plus for UK public-sector buyers |
| Accessibility | WCAG 2.2 AA; an accessibility statement per product; the European Accessibility Act for customer-facing portals |
| AI | The controls in `AI.md`, mapped to ISO/IEC 42001 |
| Payments | PCI DSS scope kept out: card data only in partner-hosted fields |
| Secure development | The UK Software Security Code of Practice, OWASP ASVS level 2, a threat model per product, penetration tests before launch and yearly |

## 7. Operations

- **Back office**: one back office per cell for all products (built from HR's): organisations, workspaces,
  plans and entitlements, feature flags, rule-pack data and the law-changes feed, support access that the
  customer grants (time-limited and audited), and a staff audit trail.
- **Support**: in-app help centre and tickets per product, stored in the home region; SLA timers per plan.
- **Status page** per region and product.
- **Monitoring and alerts** per project, from configuration; budgets and cost alerts per project.
- **On-call and incident response**: one runbook set for the suite, including the breach process.

## 8. Commercial model and billing

- **Each product sold alone**, with transparent self-serve plans for small businesses and contracts for
  mid-market (D-2), and **suite bundles** with one bill per organisation.
- **Suite billing** in the global layer (built from HR's Stripe Billing and Stripe Tax integration), holding only
  company billing data. Prices, bands, bundles and discounts are configuration in the back office, never code.
- **Entitlements** flow from the suite directory to each product's feature flags.
- **Usage-based items** (AI credits, e-invoices through partner networks, SMS and WhatsApp messages, bank-feed
  connections above a plan limit) are metered per workspace and shown to the customer before they are charged.
- **Free full export** at any time and no lock-in on monthly plans, as Thapsus-HR promises today.
