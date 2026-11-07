# Suite: steps only a person can take

> Suite document, kept identical in `PROJECT_MULLAH`, `PROJECT_BOSSMAN` and `PROJECT_KASONGO` until the
> shared `thapsus-platform` repository exists. Product-specific steps are in each product's `docs/HUMAN_TODO.md`.
> Status: 2026-11-07. Nothing here has been done yet.

Order matters: H-S1 to H-S4 unblock the platform kit (Stage 0 of `SUITE_PLAN.md`).

## H-S1 Create the shared platform repository

1. On GitHub, create a **private** repository `Bmwarari2/thapsus-platform` with a `main` branch and no template.
2. Protect `main`: pull requests required, linear history, required status checks (added once CI exists).
3. Install the Claude GitHub App on it (GitHub → Settings → Applications → Claude → Repository access).
4. Add it to the Claude Code sessions that build the suite (lead, platform and product sessions).
5. Tell the lead session, which moves the `docs/suite/` documents there and replaces the three copies with links.

## H-S2 Approve the cost estimate before anything is deployed

1. Read each product's `docs/COSTS.md` once written in its Phase 0 (staging UK cell only at first).
2. Approve it in writing in the lead session. Nothing new is deployed before this (the rule in every CLAUDE.md).

## H-S3 Google Cloud folders, projects and policies

1. In the Google Cloud console, under the Thapsus organisation, create the folders `suite/staging/uk`,
   `suite/production/uk`, and later `…/eu`, `…/us` (only when a customer in that region signs).
2. On each cell folder set the organisation policy `constraints/gcp.resourceLocations` to the cell's region group
   (`in:europe-west2-locations` for UK; EU `in:europe-west3-locations` and `in:europe-west1-locations` for backups;
   US `in:us-east5-locations` and `in:us-west1-locations`), as Thapsus-HR's bootstrap does.
3. Create one shared project `thapsus-suite-shared` in `europe-west2` for Artifact Registry and the global layer.
4. Link every project to the billing account and set a budget alert on each (amount from `COSTS.md`).
5. Run the platform's bootstrap script (written in Stage 0, modelled on Thapsus-HR's `infra/gcp/bootstrap.sh`)
   for each project; it creates the deploy identities and workload identity federation for GitHub.

## H-S4 Domains

1. Choose the suite domain (Thapsus-HR's domain is also still open: `THAPSUS_HR_DOMAIN`). Recommended: one
   domain for the suite, with a sub-domain per product and region-specific API hosts, for example
   `finance.<domain>`, `api.uk.finance.<domain>`.
2. Register it, and give the lead session the name; it goes into configuration only, never code.

## H-S5 Product names and trademarks

1. Confirm or change the working names Thapsus Finance, Thapsus CRM and Thapsus ERP (decision D-8).
2. Ask a trademark attorney to search UKIPO, EUIPO and USPTO for the chosen names and file where advised.

## H-S6 Name the design-system owner

1. Name one person who approves changes to the shared design system and the product accents
   (`DESIGN_SYSTEM.md` §4 and §9).
2. With that person, confirm the three proposed accents (indigo, plum, steel) or choose others that pass the
   token tests.

## H-S7 Legal documents

1. Instruct a solicitor (UK) and, before EU and US launch, counsel there, to review: terms of service per product
   and for bundles, the data processing agreement per region, the sub-processor lists, the AI terms
   (`AI.md`), and the EU Data Act switching terms.
2. Record each approval date in the product's `docs/HUMAN_TODO.md`; nothing marked "for legal review" goes to a
   customer before it.

## H-S8 Security certification

1. Choose a SOC 2 auditor and an ISO 27001 certification body; decide whether to use a compliance automation
   service (it must be listed as a sub-processor if it can see customer data, and should not).
2. Start the SOC 2 Type II observation window when the first product's staging is live (Stage 2), so the report
   exists by general release.
3. Book Cyber Essentials Plus for UK public-sector buyers.

## H-S9 Design partners

1. Recruit at least two pilot customers for each new product (for ERP, at least one per industry pack it
   launches with), using only fictional data until their contracts and DPAs are signed.
2. Agree what each pilot tests and when, and tell the lead session.

## H-S10 Email sending

1. Amazon SES production access in each region a product sends from (the SES-only AWS account Thapsus-HR already
   uses, ADR-0219), with separate configuration sets and domains for CRM marketing email.
2. Publish SPF, DKIM and DMARC records for each sending domain when the lead session supplies them.

## H-S11 AI legal review (before any AI feature is switched on)

1. As for Thapsus-HR's Ask HR (its HUMAN_TODO §7): legal review of each model's ML-processing location per region,
   the 30-day sub-processor notice, and the AI terms.
2. Only then does a reviewed code change lift the legal hold for that region and feature (`AI.md` §3).

## H-S12 Payment and billing accounts

1. Extend Thapsus-HR's Stripe accounts (one per region) with the new products' prices, or create them, when the
   lead session supplies the price configuration (prices are configuration, never code).
