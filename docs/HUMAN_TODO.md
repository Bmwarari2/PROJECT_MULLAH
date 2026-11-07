# Steps only a person can take — Thapsus Finance

Suite-wide steps (repository, Google Cloud, domains, legal, certification, design partners) are in
`suite/HUMAN_TODO_SUITE.md`. These are Finance's own. Status on 2026-11-07: none done. Build against mocks and
sandboxes until each is done.

## M-1 Approve the build brief

1. Read `docs/BUILD_BRIEF.md`, `docs/PLAN.md` and `docs/FEATURES.md`.
2. Approve, or note changes, in the lead session. The build starts only after approval.

## M-2 Design partners and an accountant reviewer (needed by the F1 gate)

1. Find at least two UK pilot companies (one VAT partially exempt, such as a care provider; one using CIS) and one
   accounting practice willing to review outputs.
2. Sign pilot agreements and DPAs before any real data; until then pilots use fictional data.

## M-3 HMRC Developer Hub and Making Tax Digital for VAT (start in Stage 1)

1. Register Thapsus on the HMRC Developer Hub (https://developer.service.hmrc.gov.uk), create a sandbox application
   and subscribe it to the VAT (MTD) APIs.
2. Give the lead session the sandbox client id; the secret goes into Secret Manager (the lead session gives the
   secret name).
3. When Phase 5 passes the sandbox tests, request production credentials: complete HMRC's production approvals
   checklist, minimum functionality standards and terms of use, and prove fraud-prevention headers.
4. Ask HMRC to list Thapsus Finance on its "Software Choices" page.
5. MTD for Income Tax: HMRC has closed production-credential requests for new 2026–27 quarterly-update products. Ask
   HMRC when the next window opens; record the answer here.

## M-4 Open-banking aggregator per cell

1. UK first: choose between TrueLayer and Yapily (both cover the UK and much of the EU); GoCardless Bank Account Data
   takes no new sign-ups. US later: Plaid, MX or Finicity.
2. Sign the agreement and DPA; confirm where data is processed for each cell; record it in `docs/SUBPROCESSORS.md`
   before the integration is built.

## M-5 Payment partners

1. Stripe (cards, hosted fields only) and GoCardless (Direct Debit and pay by bank) platform or partner agreements;
   decide whether to offer UK commercial variable recurring payments through a PISP.
2. A regulated payment-initiation partner for supplier payment runs.
3. Record each in SUBPROCESSORS before integration.

## M-6 Companies House and HMRC filing (before Phase 10)

1. Apply to Companies House for software-filing presenter credentials and test access for accounts in iXBRL.
2. Register for HMRC's Corporation Tax online filing test service (CT600 with iXBRL).
3. Decide whether Thapsus becomes an Authorised Corporate Service Provider for agent filing, or leaves that to
   accountant users.

## M-7 Peppol and e-invoicing (decide by mid-2027)

1. Choose a certified Peppol access-point and e-invoicing partner covering the EU countries in Phase 11 (including
   France's approved platforms, Poland's KSeF, Italy's SDI); sign the agreement and DPA.
2. By mid-2027 decide whether Thapsus becomes its own Peppol access point and SMP (OpenPeppol membership, Peppol
   Authority agreement, test PKI, testbed, production certificate; 2025 fees about €2,000 sign-up and €5,050 a year
   for AP plus SMP at 11–50 staff). The UK Peppol Authority (HMRC) publishes its requirements in spring 2028, with
   readiness testing in summer 2028 for the 1 April 2029 mandate.

## M-8 US tax and filing (before Phase 12)

1. Choose a sales-tax engine (Avalara, Vertex, Stripe Tax or Anrok) and sign the partner agreement.
2. Apply for an IRS IRIS Transmitter Control Code and API client id for A2A filing of 1099s, and complete the IRS
   testing (FIRE closes on 19 November 2026).
3. Engage a SOC 1 Type II auditor for SOX-reliant US customers.

## M-9 UK payroll (before Phase 15)

1. HMRC RTI: register for the PAYE online test service and HMRC's recognition scheme for payroll software.
2. Pension providers: agreements and API access with the main auto-enrolment providers the pilots use.

## M-10 Pricing

1. Approve the Finance price configuration (plans per entity, user types, practice-edition wholesale prices, AI
   credits). Prices are configuration in the suite back office, never code.
