# Thapsus suite design system (plan)

> Suite document, kept identical in `PROJECT_MULLAH`, `PROJECT_BOSSMAN` and `PROJECT_KASONGO` until the
> shared `thapsus-platform` repository exists (HUMAN_TODO H-S1). Change all three copies in the same change.
> Status: plan for owner approval, 2026-11-07.

The owner asked for a UI that is consistent across every Thapsus product. On 2026-11-07 the owner chose
**one shared design system in which each product keeps its own accent colour**. Thapsus-HR and Thapsus Care
move onto the shared tokens over time; the three new products are built on them from their first screen.

## 1. Where we start from

Two products already have their own design contracts. Both follow the same principles (calm, Apple.com-
inspired, system font stack with Inter, Lucide icons, no SF Pro or SF Symbols, WCAG 2.2 AA, light and dark
mode), but their values differ:

| Area | Thapsus-HR (`PROJECT_KAREN/docs/DESIGN.md`) | Thapsus Care (`ROCHE` master prompt §8, ADR-0026) | Shared system decision |
| --- | --- | --- | --- |
| Token source | `packages/ui/src/tokens.ts` → CSS variables, contrast test | `packages/ui/src/tokens/tokens.ts` → `tokens.css` and React Native | One source in W3C Design Tokens format, built to CSS, TypeScript and React Native (§3) |
| Accent | Deep teal `#0b6b63` / dark `#5fd3c6` | Blue `#0071e3` (links `#0066cc`) / dark `#2997ff` | Each product keeps one accent (§4) |
| Page background, dark | `#0a0a0b` | `#000000` | HR's values (more depth for layered surfaces) |
| Secondary text | `#5f5f66` | `#6e6e73` | HR's `#5f5f66` (passes 4.5:1 on every surface) |
| Status colours | success `#1b7a3a`, warning `#8a5a00`, danger `#b42318`; text and icons only | success `#1a7f36`, warning `#b25000`, danger `#d70015`; filled chips with white text | One set of status colours, used as text, icon or a filled chip. The values are chosen in Stage 0 so that each passes 4.5:1 as text and as a fill with white text, and so that the accents in §4 stay clear of them |
| Breakpoints | 640 / 768 / 1024 / 1280 | 735 / 1069 / 1441 | HR's set, plus `2xl` 1440 for wide tables (Care's 1441 is kept as `2xl`) |
| Top bar | 56 px, translucent chrome, global search, phone tab bar | 44 px translucent bar, collapsible sidebar | HR's shell (ADR-0154, ADR-0180): 56 px top bar, 248/64 px rail, phone tab bar |
| Type scale | `large-title` 34/41 for app titles; `callout` 15/22 in dense UI | `title` 32/36; body 15 in tables, 17 in forms | HR's scale. Care's scale maps onto it one-to-one (§3.3) |
| Radii | 8 / 12 / 18 / 28 / pill | 12 inputs, 8 chips, 18 cards, 28 tiles, pill | HR's set |
| Charts | Six-colour categorical palette, checked for colour-vision deficiency | Not yet defined | HR's palette |
| Mobile native | None (PWA only) | `@harbour/ui-native` for Expo | Kept as `@thapsus-suite/ui-native`, used by Care now and by the later native apps (§8) |
| Content rules | Sentence case; en-GB and en-US catalogues; plural families | Sentence case; British English; no emoji; status never by colour alone | Both sets, suite-wide (§7) |

HR's contract is the more complete base (shell, phone patterns, charts, components). Care's contract adds
the native token build, the filled status chip and the print stylesheet. The shared system takes HR's
contract as the base and adds Care's additions.

## 2. Packages

All packages live in the shared `thapsus-platform` repository and are published to a private npm
repository in Artifact Registry (europe-west2), pulled through workload identity in CI. Each product pins a
version and upgrades through Renovate pull requests that run its visual-regression suite.

| Package | Contents |
| --- | --- |
| `@thapsus-suite/tokens` | Token source in W3C Design Tokens format (JSON), built with Style Dictionary to CSS custom properties, a TypeScript object and a React Native object. Holds the base tokens, the five product accents and the contrast test. |
| `@thapsus-suite/ui` | React components on Radix primitives, restyled in the shadcn/ui manner (the HR component set in §6 of its DESIGN.md, plus the business patterns in §5 below). Server-safe where possible. |
| `@thapsus-suite/shell` | The app chrome: top bar, rail, phone tab bar, module header, segmented section nav, **app switcher**, workspace and entity switcher, account menu, command palette, notifications panel, help panel, offline and staleness banners. |
| `@thapsus-suite/charts` | KPI tile, column, bar list, donut, line, sparkline and legend in HTML/SVG, with accessible summaries and the categorical palette. |
| `@thapsus-suite/icons` | Lucide (ISC) at 1.5 px stroke, 20 px in the app and 24 px on marketing pages; one product glyph for each product. |
| `@thapsus-suite/content` | Voice and tone guide, message-catalogue conventions (ICU MessageFormat, plural families), shared strings (buttons, errors, dates). |
| `@thapsus-suite/ui-native` | React Native components from the same tokens (Care's `@harbour/ui-native`, renamed). |
| `@thapsus-suite/eslint-config` | Lint rules that refuse raw colours, pixel sizes, radii, shadows and durations outside the token set, and icon-only buttons without a name. |

Thapsus-HR keeps its `@thapsus/*` names and Thapsus Care its `@harbour/*` names for their own packages;
the shared scope is `@thapsus-suite/*` so nothing collides.

## 3. Tokens

### 3.1 Layers

Tokens resolve in five layers. A later layer can only change what the earlier one allows.

1. **Suite base**: neutrals, status colours, type scale, spacing, radii, shadows, motion, breakpoints.
2. **Product**: the product's accent pair (light and dark), its glyph and its name.
3. **Tenant branding**: logo, workspace name, and an optional brand colour used only on customer-facing
   documents and portals (invoices, quotes, the customer portal, the careers page). A brand colour is accepted
   only if it passes the same contrast test; otherwise the product accent is used and the admin is told why.
4. **User**: theme (System, Light, Dark), density (Comfortable, Compact) and reduced motion.
5. **Accessibility overrides**: high contrast and larger text (200% without loss of content).

### 3.2 Rules carried from both products

- Semantic tokens only; components never use raw hex values (a lint rule and a test enforce it).
- Every text token passes 4.5:1, and every control border 3:1, against `bg`, `surface` and `surface-raised`,
  in both themes. The contrast test runs over every product accent too.
- Status is always shown by an icon and a word, never by colour alone.
- **Text links are underlined** everywhere in the app (not only on hover). This is new for the suite: it lets
  every accent, including the steel ERP accent, meet WCAG 1.4.1 without relying on colour.
- Motion: 120 / 200 / 320 ms with the standard and exit curves; only `opacity` and `transform`; every duration
  is 0 under `prefers-reduced-motion`. Nothing animates on first paint of the app.
- Numbers in tables use tabular figures. Money is right-aligned. Negative amounts follow the locale and a
  tenant setting (`-1,234.56` or `(1,234.56)`), never colour alone.
- Minimum text size 13 px; primary touch targets 44 px; other targets at least 24 px.

### 3.3 Mapping Care's tokens

Care's tokens map onto the shared names without visual surprises: `bgSecondary` → `surface`, `card` →
`surface-raised`, `textSecondary` → `text-secondary`, `link` → `accent-text` (Care's own accent pair keeps its
`#0066cc` link value, see §4), and the type sizes `title`, `eyebrow`, `intro`, `body`, `callout` and `caption`
map to `large-title`, `title-3`, `body-lg`, `body`, `callout` and `footnote`. A codemod in the platform
repository rewrites Care's class names; Care's visual-regression suite shows every changed pixel before it
merges (§9).

## 4. Product accents

Each product has one accent pair. The accent is used for links, primary buttons, the focus ring, selected
states and the product glyph's tile, and nothing else. The values below were measured on 2026-11-07 against
HR's surfaces (`#ffffff`, `#f5f5f7`; dark `#0a0a0b`, `#161618`) with the WCAG formula and CIEDE2000 distances.

| Product | Codename | Light accent | Dark accent | Text on white | White on accent | Dark accent on dark bg | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Thapsus-HR | PROJECT_KAREN | `#0b6b63` teal | `#5fd3c6` | 6.37 | 6.37 | 10.95 | Existing |
| Thapsus Care | ROCHE | `#0066cc` blue (text) / `#0071e3` (fills) | `#2997ff` | 5.57 | 4.70 | 6.56 | Existing. `#0071e3` is 4.31:1 on the soft-grey surface, so text uses `#0066cc` |
| Thapsus Finance | PROJECT_MULLAH | `#3f3fc4` indigo | `#a5a8ff` | 7.72 | 7.72 | 9.09 | Proposed |
| Thapsus CRM | PROJECT_BOSSMAN | `#9c1c8c` plum | `#f19be6` | 7.10 | 7.10 | 9.96 | Proposed |
| Thapsus ERP | PROJECT_KASONGO | `#44475a` steel | `#b9bdd9` | 9.15 | 9.15 | 10.69 | Proposed |

**How they were chosen.** An accent must be at least 15 ΔE00 from every other product accent, so the app
switcher reads clearly, and at least 20 ΔE00 from every status colour, so a primary button is never read as a
warning or an error. Orange (ΔE 6 from Care's warning, 9 from HR's danger) and violet (ΔE 8 from the Finance
indigo) failed. Steel was the only ERP candidate that clears every pair (at least 20 from the other accents,
at least 34 from the status colours). Because steel is close to the body text colour (1.84:1), underlined
links (§3.2) are a requirement, not a preference.

The accents are proposals. They are fixed in Stage 0 with the owner, once the shared status colours are
chosen, and the token test then holds them.

## 5. Shared patterns for business applications

HR's component set covers people, time and approvals. Finance, CRM and ERP add patterns that every product
then gets for free:

| Pattern | Used for | Notes |
| --- | --- | --- |
| Data grid | Ledgers, pipelines, stock, orders | Virtualised rows; sticky header and first column; column chooser; saved views per user, shared views per role; filter chips; inline edit with validation; multi-select bulk actions; keyboard navigation (arrows, Enter, Esc, F2); totals row; export to CSV, Excel and PDF in the background. Becomes a list on phones. |
| Record page | Customer, invoice, item, deal, order | Header with key fields and status, primary action and overflow menu; segmented sections; a right-hand inspector for related records; activity timeline; history with field-level changes from the audit log. |
| Form renderer | Every form | Renders layouts defined in Studio (`STUDIO.md`), so customer-added fields appear without code. Autosave drafts, inline errors linked by `aria-describedby`, one primary action. |
| Line-item editor | Invoices, bills, quotes, orders, journals | Keyboard-first grid for lines with item lookup, quantity, unit, price, discount, tax code, dimensions and totals; paste from spreadsheets; running balance for journals. |
| Money and quantity inputs | Everywhere | Currency-aware, locale formatting, minor units internally, units of measure with conversion hints. |
| Board | Pipelines, tickets, work orders | Kanban with drag and keyboard moves; WIP limits; a list view of the same data. |
| Calendar and timeline | Meetings, deliveries, production, projects | Day, week and month; resource rows; Gantt for projects and production schedules with keyboard moves. |
| Document preview | Invoices, quotes, POs, delivery notes | Paged PDF preview from the template engine, with print and download. |
| Approvals panel | Every approval | The same approve, reject and delegate controls, comments and audit everywhere; also in the suite inbox (§6). |
| Scanner input | Warehouse, shop floor, receiving | Accepts hardware scanners in keyboard-wedge mode and the camera (`BarcodeDetector` with a WASM fallback); audible and haptic confirmation; works offline with a queue. |
| Kiosk mode | Shop floor, clocking | Large targets, PIN sign-in, auto sign-out (as HR's clocking kiosk). |
| Dashboard | Every product | KPI tiles and charts from `@thapsus-suite/charts`, built in the report builder. |
| AI draft | Every AI feature (`AI.md`) | One "AI draft" badge, a sources list, accept, edit and discard controls, and a "why am I seeing this" link. |

## 6. One shell across products

Every product renders the same chrome from `@thapsus-suite/shell`:

- **App switcher** in the top bar lists the Thapsus products the organisation has, each with its glyph and accent
  tile, and opens the same workspace in that product (deep links keep the context, for example from a CRM
  account to its invoices in Finance). Products the organisation does not have are not shown.
- **Workspace and entity switcher**: the organisation, and the legal entity or location where relevant.
- **Global search** (command palette) searches the current product first. Once the suite search
  (`INTEGRATION.md` §8) exists it also searches the other products, permission-aware.
- **Notifications and the suite inbox**: one bell for notifications and one inbox for approvals and tasks
  from every product the person uses.
- **Help** opens the product's help centre; **account** holds profile, theme, language and sign-out (which
  signs out of every product, `PLATFORM.md` §2).

The products remain separate applications with separate deployments. They share packages, not a runtime:
no micro-frontend composition, so one product's release can never break another's page.

## 7. Content

- Sentence case for headings, buttons and labels; plain, specific wording; no idioms that do not translate;
  no emoji.
- en-GB and en-US message catalogues at launch, then the EU languages the EU packs need (`SUITE_PLAN.md`).
  No hard-coded user-facing strings; a number next to a noun is always a plural family.
- Each product's glossary is shared where terms overlap (customer, supplier, entity, cost centre, item) so the
  same thing has the same name in every product.
- Errors never blame the user and say what to do next. Destructive actions name what will happen.

## 8. Mobile

On 2026-11-07 the owner chose **installable PWAs first** for the new products, with a native app later for
heavy offline warehouse and field-service work, reusing Care's Expo and offline stack. The design system
therefore keeps the React Native token build (`@thapsus-suite/ui-native`) from day one, so the later apps
and Care's existing app share the same tokens.

## 9. Governance and enforcement

- **Ownership.** The design system has one owner (a named person, HUMAN_TODO H-S6) and changes through pull
  requests to the platform repository, with a changeset and a visual diff.
- **Versioning.** Semantic versioning. A token value change is a minor release; a rename or removal is a
  major release with a codemod.
- **Gallery.** One Storybook for the shared packages, with every component in light and dark at phone and
  desktop sizes, and axe on every story.
- **Tests in every product.** The contrast test over the product accent; Playwright visual regression of key
  screens in light and dark × phone (390×844) and desktop (1440×900); axe with zero serious findings; the
  lint rules in §2; Lighthouse (mobile) ≥ 90 performance and ≥ 95 accessibility on key screens.
- **Adoption by HR and Care** (`SUITE_PLAN.md` Stage 3): token bridge first (their variables point at the
  shared values), then the shell and app switcher, then component swaps, each behind their visual-regression
  suites. Neither product changes accent colour.
