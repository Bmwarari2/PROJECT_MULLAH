# Thapsus Studio: customising each product without code (plan)

> Suite document. The canonical copy is `PROJECT_ATLAS/docs/suite/`; `PROJECT_MULLAH`, `PROJECT_BOSSMAN` and
> `PROJECT_KASONGO` keep read-only mirrors. Change it in PROJECT_ATLAS, then copy it to the three mirrors in the same change.
> Status: plan for owner approval, 2026-11-07. Research: `RESEARCH_PLATFORM.md` §1.

The owner's requirement: each product must be easy to customise for a client's business **without changing
code**. Studio is the one customisation engine shared by every Thapsus product, so a client learns it once and
an implementation partner can configure any product with the same skills.

Thapsus-HR already has the core of it: custom fields of eight types, custom objects with validation and
field-level permissions, automation rules (record, date, document-expiry and threshold triggers; task,
notification, workflow, webhook and integration actions with dry run and an execution log), workflow templates,
multi-step approvals, forms, a report builder and feature flags. Thapsus Care has a forms and assessment engine
and a task and workflow engine. Studio extracts these into `@thapsus-suite/studio` and widens them.

## 1. What a client admin can change

| Capability | What it does | Notes |
| --- | --- | --- |
| **Custom fields** | Add fields to any standard record: text, long text, rich text, number, money (with currency), percentage, date, date-time, duration, boolean, single and multi select, lookup to another record, user, file, URL, email, phone, address, formula (read-only, computed), roll-up (sum, count, min, max over related records) | Field-level permissions per role; required by stage or status; default values; help text in every language; audit of every change |
| **Custom objects** | New record types with their own fields, layouts, list views, permissions, numbering, relationships (one-to-many and many-to-many) to standard and custom records, and API endpoints | Appear in search, reports, automation, imports, exports and the public API (`/v1/custom/<object>`) automatically |
| **Picklists and reference lists** | Values, order, colours (with icons), translations; dependent picklists | Values used in automation are referenced by key, so renaming is safe |
| **Layouts and forms** | Arrange sections and fields per record type, per role and per stage; conditional sections (show when an expression is true); mobile layout | Rendered by one form renderer (`DESIGN_SYSTEM.md` §5) |
| **List views** | Columns, filters, sorting, grouping, totals; personal and shared views | Saved views respect permissions |
| **Record types and stages** | Several variants of one record (for example "Retail order" and "Trade order") with their own layouts, picklists and stages | Pipelines in CRM, document types in Finance, order types in ERP |
| **Validation rules** | Block a save with a message when an expression is true | Run on API, imports and UI alike |
| **Formulas** | Computed fields and default values from an expression | Same expression language (§3) |
| **Numbering sequences** | Prefixes, per entity, per year, gap-free where the law needs it (invoices in many EU countries) | Gap-free sequences are allocated in the posting transaction |
| **Automation** | When something happens (created, changed, field changed to, date reached, threshold crossed, schedule, inbound webhook, event from another Thapsus product), if conditions hold, do actions (update fields, create records, create tasks, send notifications or emails, start an approval, call a webhook, call an integration, post a message) | Dry run, execution log, retries, idempotent jobs, loop protection, per-tenant limits |
| **Approvals** | Multi-step, conditional (amount bands, entity, department), parallel or sequential, delegation, escalation and SLA timers, separation of duties (the requester never approves) | One approvals engine and panel in every product |
| **Business processes** | Guided stages for a record (lead → customer, requisition → PO → receipt) with required fields and checklists per stage | |
| **Document templates** | Invoices, quotes, purchase orders, delivery notes, statements, letters, emails: layout, logo, fonts from the allowed set, merge fields, conditional blocks, languages | Rendered to PDF in the home region; a preview with sample data |
| **Email and notification templates** | Wording, merge fields, languages, channels | |
| **Roles and permissions** | Custom roles from the permission catalogue, scopes (entity, location, team, own records), field-level security, record sharing rules | An admin cannot grant more than they hold |
| **Dashboards and reports** | Report builder over standard and custom fields (filters, grouping, charts, schedules, exports); dashboards per role | Runs on read replicas |
| **Imports and mappings** | Saved mappings from CSV, Excel and the common competitors' exports | |
| **Integrations and webhooks** | Outbound webhooks on any event; inbound webhooks into automation; connector settings | SSRF-guarded, from the home region |
| **Labels and terminology** | Rename standard labels (for example "Deal" to "Opportunity", "Item" to "Product") per language | Through the message catalogues, never by editing code |
| **Branding** | Logo, workspace name, brand colour for documents and portals (`DESIGN_SYSTEM.md` §3.1) | |

## 2. Solution packs and sandboxes

- **Solution packs** bundle configuration (fields, objects, layouts, picklists, automation, templates, reports,
  roles, rules-pack settings) for an industry or a use case: for example "Food manufacturing" for ERP,
  "Construction (CIS)" for Finance, "B2B software sales" for CRM. Thapsus ships packs; partners can build them;
  a client installs one in a click and can then change it.
- **Layers.** Thapsus-owned configuration (standard and pack items, namespaced `thapsus.*`) is kept separate
  from client-owned configuration. A Thapsus release can update its own layer without overwriting a client
  change; where both changed the same item, the client's version wins and the admin sees the difference.
- **Sandboxes.** A client can create a sandbox workspace from production configuration with synthetic data (as
  HR's sandboxes do), change configuration there, and **promote a configuration package** to production. A
  package is versioned, shows a diff before it is applied, and can be rolled back. Every change is audited.
- **Export and import of configuration** as a versioned JSON document, so a partner can move a setup between
  clients and a client can keep it in their own repository. This is also a legal requirement: under the EU Data
  Act, the "exportable data" a customer can take when switching provider includes metadata created by its use of
  the service, which covers its configuration.
- **Change sets and replay.** Every edit goes into a draft change set; publishing validates it (schema, CEL type
  checks, dependencies, permission references, cost budget), shows a diff and applies it in one transaction.
  Before every Thapsus release, CI replays each tenant's compiled configuration (no data) against the new schema,
  so a release that would break a customer's rule is caught before deployment. Customers get an upgrade impact
  report listing where their override masks a Thapsus change.

## 3. The expression language

Formulas, validation rules, conditions, visibility rules and approval rules all use one expression
language. The choice is the **Common Expression Language (CEL)**, a Google specification that is not
Turing-complete, has no side effects and is type-checked before it runs. It is used in Kubernetes admission
policies and Google Cloud IAM conditions.

- **Library**: `@marcbachmann/cel-js` (MIT, zero dependencies, typed environments, custom functions, structural
  limits), with `@bufbuild/cel` (Apache-2.0) as the alternative if conformance matters more than speed. The
  applicable CEL conformance tests run in CI.
- **Storage**: the source text and the checked syntax tree are both stored; the visual rule builder emits CEL, so
  there is one runtime.
- **Cost**: the JavaScript libraries offer structural limits only, so Studio adds an estimator (node count times
  declared maximum collection sizes, the cel-go approach) with a budget per plan, checked when a rule is published,
  plus caps on bound list and string sizes and a wall-clock guard for bulk evaluation such as imports.
- **Deterministic function library**: never `now()` (the request time is injected); dates through the shared time
  utilities with an explicit IANA zone; money in minor units with the currency checked; no locale formatting inside
  expressions.

The comparison with JSONLogic, Power Fx and embedded JavaScript is in `RESEARCH_PLATFORM.md` §1.5.

Admins never type CEL directly unless they want to: the editor offers a builder (field, operator, value) that
writes the expression, and shows the expression for power users. Expressions are checked against the record's
schema when saved, so a renamed or deleted field is caught before it breaks anything.

## 4. Beyond no-code

For the few needs that configuration cannot meet, without forking the product:

1. **Public API, webhooks and the SDK** (every product).
2. **Connectors** to common systems, configured in Studio.
3. **Sandboxed scripts** (later phase): small TypeScript functions run in an isolated WebAssembly JavaScript
   engine with no network except allow-listed connectors, memory and time limits, and the same permissions as
   the user who installed them. Off by default; enterprise plans only.
4. **Marketplace** (later phase): partner apps and solution packs, reviewed by Thapsus.

Thapsus never maintains a client-specific branch of a product. "Configurable, never forked" is a rule for every
product, as it already is for Thapsus Care.

## 5. How it is stored

- **Custom fields** live in a `custom jsonb` column on each standard table, validated against the tenant's field
  definitions on write, with a GIN index for containment queries. Fields flagged filterable, sortable or unique
  also write a row, in the same transaction, to a typed **index-slot table** (`custom_field_index`: text, number,
  date, timestamp and reference columns, each with b-tree indexes leading with `tenant_id` and the field). This gives
  range queries, sorting and uniqueness for any tenant without per-tenant DDL. Thousands of per-tenant partial
  indexes scale badly and are avoided; the largest tenants move to a dedicated database instead. This keeps forced
  row-level security and audit unchanged, because every table is tenant-scoped.
- **Sensitive custom fields** (health, diversity, immigration, bank or tax data) never go in `custom jsonb`: a field
  must carry a classification, and Studio stores sensitive ones in the encrypted vault with per-access audit.
- **Custom objects** live in one generic `custom_record` table per product database (`(tenant_id, id)` keys,
  `jsonb` values, the same index slots), with typed relationship tables and full RLS.
- **Payload caps** (for example 32 KB of custom data per row) keep updates cheap; long text and files live in their
  own tables or in object storage.
- **Metadata** (definitions, layouts, rules, automations) lives in versioned tables with `tenant_id`, forced
  RLS and audit, cached per tenant in memory with event-driven invalidation.
- **No entity-attribute-value tables** for values: they make reports and indexes slow at scale.
- **Limits** per plan: number of custom fields per record type, custom objects, automations, scheduled jobs and
  expression cost. Limits are configuration, not code.

## 6. Acceptance criteria for Studio (platform kit)

- Every capability in §1 works through the API, imports and the UI, with the same validation.
- A custom field added in Studio appears in forms, lists, search, reports, automation, exports, the public
  API and the OpenAPI document of that workspace, with no deployment.
- Isolation tests cover custom objects and metadata tables; the isolation suite fails on any table without a
  policy.
- An expression that exceeds its cost limit is refused at save time; a runaway automation loop stops at the
  loop limit and is logged.
- A configuration package promoted from a sandbox applies the same result as making the changes by hand, shows
  its diff first, and rolls back cleanly.
- A Thapsus release that changes a pack item never overwrites a client's change to it (tested).
- Performance: list and search over 1 million records with 50 custom fields stay within the product's latency
  budget (benchmark recorded).
