# Optional AI across the Thapsus suite (plan)

> Suite document, kept identical in `PROJECT_MULLAH`, `PROJECT_BOSSMAN` and `PROJECT_KASONGO` until the
> shared `thapsus-platform` repository exists (HUMAN_TODO H-S1). Change all three copies in the same change.
> Status: plan for owner approval, 2026-11-07. Research: `RESEARCH_PLATFORM.md` §3 and §4.

The owner's requirement: AI features should be available, but optional. This document sets the rules every
product follows. It builds on what the suite already runs or has decided:
- **Thapsus-HR** "Ask HR" (ADR-0112, ADR-0248): Claude on Vertex AI **regional endpoints only**, two legal holds in
  code per cell, a staff availability switch plus the account owner's choice, redaction, citation validation,
  and an audit event per question that records the region.
- **Thapsus Care** §16: an AI gateway as the only path to a model, pseudonymisation, a scope lock, no automated
  decisions, 12 months of audit, an evaluation suite in CI, per-tenant budgets and a kill switch.

## 1. Principles

1. **Off by default, at three levels.** A feature works only when (a) Thapsus has made it available in the cell
   (legal hold lifted by a reviewed code change, after legal review), (b) the customer's owner has switched AI on
   for the workspace (step-up required), and (c) the specific feature is switched on. Admins can also limit it to
   roles, entities or locations. Switching AI off is immediate and needs no step-up.
2. **AI drafts; people decide.** No AI output changes state on its own: nothing is posted, paid, filed, sent,
   ordered, moved, scored or assigned until a person accepts it. Every suggestion shows an "AI draft" badge, its
   sources and its confidence where meaningful, with accept, edit and discard controls.
3. **Every feature has a non-AI path.** Rules, filters, templates and statistical methods do the job without a
   model; AI adds speed or polish, never a capability the product cannot do without it.
4. **In-region processing.** Models are called only through the cell's AI gateway, on regional endpoints, never
   global or multi-region ones. Where Google gives a model's ML processing as multi-region within an area, the
   product says so in words and needs legal review first (as HR's ADR-0248).
5. **Least data.** The gateway sends only what the feature needs, pseudonymises names and identifiers where it
   can, never sends sensitive categories (health, diversity, immigration, bank details, tax identifiers, payroll
   data, care records), and applies the viewer's permissions before retrieval.
6. **No training on customer data.** Providers are used only under terms that forbid training and retention beyond
   abuse monitoring, and are listed per region in each product's `docs/SUBPROCESSORS.md`.
7. **Transparent.** People are told when they are talking to or reading from AI (EU AI Act Article 50). Each
   product has an AI page in settings that says which features exist, where they run, on which model, and what
   data they use.

## 2. The AI gateway (`@thapsus-suite/ai`)

- The **only** code path allowed to call a model. A lint rule and a test refuse any other import of a model SDK.
- **Providers** behind adapters with contract tests: Claude on Vertex AI and Gemini on Vertex AI (regional
  endpoints), plus a fake provider for tests (refused in production). Bring-your-own-model is a later option for
  enterprise plans.
- **Placement check before every call**, as `assertVertexRegionAllowed` in Thapsus-HR: the region must be the cell's
  own region, or its approved opt-in region with both switches on; `global`, `us` and `eu` are always refused.
- **Guards**: input classification (refuses out-of-scope requests per feature), redaction and pseudonymisation,
  structured output with schema validation, citation checks for retrieval answers, a post-model policy check
  (for example "no decision about a person"), and refusal handling.
- **Retrieval**: pgvector in the product's own database for that tenant's documents and help content, with
  permissions applied before ranking. No vector data leaves the cell.
- **Tools for agents**: a feature that lets the model call product functions gets only read tools plus "create
  draft" tools. Commit actions are always a separate human step.
- **Audit**: every call records the feature, user, model and version, region, token counts, the pseudonymised
  context references and the outcome (accepted, edited, discarded). Prompt and output text is kept only where the
  customer opts in, for 12 months at most, in the home region.
- **Budgets and kill switches**: per-tenant monthly budgets and rate limits; an operator kill switch per feature
  and per cell; graceful fallback to the non-AI path.
- **Evaluation**: golden sets per feature, red-team prompts (prompt injection through documents, emails and
  notes, cross-tenant exfiltration attempts, jailbreaks), hallucination and citation checks, and fairness checks
  for anything about people. Runs in CI; a failure blocks that feature's release.

## 3. Holds and switches

The same model as Thapsus-HR's ADR-0248, generalised:
- A per-cell, per-feature **availability map in code** (`AI_FEATURES` in `@thapsus-suite/ai`), each entry with the
  model, region, approval flag and the date its facts were checked. Lifting a hold is a reviewed code change that
  records the legal review; nothing in the back office or database can lift it.
- **Staff availability** per feature (back office, audited) and **customer choice** (owner, step-up). Turning
  availability off clears customers' choices in the same transaction.
- Facts (model availability per region, ML-processing location, retirement dates) are re-checked every quarter.

## 4. Regional availability

What can run where, checked on 2026-11-07, is in `RESEARCH_PLATFORM.md` §3. Products plan features so that each
has a deterministic fallback in a cell where no suitable model is available in-region.

## 5. Features by product

Each product's `docs/FEATURES.md` lists its AI features with their human-approval step. A summary:

| Product | Features (all optional, all drafts) |
| --- | --- |
| Finance | Bank-match suggestions; bill and receipt capture (field extraction from PDFs and photos); account and tax-code coding suggestions; duplicate and anomaly flags; variance commentary for management packs; collections email drafts; close checklist assistant; cash-flow forecast explanations; questions about the books in plain language, answered from governed reports with the figures cited |
| CRM | Email and reply drafts; meeting and call summaries with action items (only where recording consent rules pass); record updates suggested from emails; deal-risk and next-step suggestions from activity rules plus model commentary; service answers drafted from the knowledge base with citations; a customer-facing chatbot that says it is AI and hands over to a person; case summaries and routing suggestions; segment building in plain language; data-cleanup suggestions |
| ERP | Supplier document capture (invoices, delivery notes, certificates of analysis); suggested reorder quantities and forecast adjustments with explanations; draft purchase orders for approval; anomaly flags (stock, costs, yields); plain-language questions over operational reports; maintenance notes summarised; item data cleanup (descriptions, attributes) |
| Suite | "Ask Thapsus": questions across the products the person can see, answered from each product's governed reports and documents with sources; an MCP server per product so a customer's own AI agents can read data and create drafts with a scoped, revocable credential |
| HR | Ask HR (built, ADR-0248) |
| Care | The AI Management Copilot (planned, master prompt §16), unchanged |

## 6. What AI never does in the suite

- Decide or recommend anything about an individual's employment, credit, pay, or care.
- Infer emotions of employees or customers.
- Score people for creditworthiness (an EU AI Act high-risk use) — Finance's credit control uses rules the
  customer sets, not a model.
- Send a message, post a journal, pay a bill, place an order, change a pipeline stage or close a case by itself.
- Use one tenant's data for another tenant, or for training.
