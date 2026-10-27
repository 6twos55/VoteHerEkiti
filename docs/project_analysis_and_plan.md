# VoteHerEkiti — Project Foundation Analysis & Plan

---

## 1. Product Understanding

### What It Is
The **Gender Accountability Dashboard** is a public-facing civic tech platform for the **Vote Her Ekiti** project by **Women Too Africa Foundation (WTA)**. It tracks the incoming Ekiti State administration's compliance with the **35% Affirmative Action** threshold for women's representation in government.

### Who It Serves

| Persona | Role | Key Need |
|---------|------|----------|
| **Engaged Citizen** | Young Ekiti resident (18-40), mobile-first | Quick answer: "How many women? Is the governor keeping his word?" |
| **Journalist** | Radio/online/print media covering Ekiti | Specific data points, downloadable reports, quotable figures |
| **CSO Advocate** | Civil society organisations, women's rights groups | Trend data, historical comparisons, Charter of Demands status |
| **Government Monitor** | Officials or aides checking admin's public rating | Scoring methodology, unmet Charter demands |
| **WTA Admin** | Non-technical WTA staff updating data | Simple CRUD interface, no developer assistance needed day-to-day |

### Major Features (9 public pages + admin panel)

**Public Pages:**
- **`/` (Home)** — Live Gender Accountability Index score, key metrics strip, recent activity feed, nav cards, social sharing card
- **`/tracker`** — Searchable/filterable cabinet appointment table with gender metadata, trend chart, CSV export
- **`/index`** — Full scoring breakdown across 4 dimensions (40/30/20/10 default weights), methodology note
- **`/pledges`** — Gender Commitment Pledge archive with fulfilment status badges and PDF links
- **`/charter`** — Full Ekiti Women's Charter of Demands with demand-by-demand response status
- **`/history`** — 1999–2026 longitudinal data: administration-by-administration comparison chart, LGA-level view
- **`/bulletins`** — Monthly Accountability Bulletin archive with PDF viewer/download
- **`/about`** — Project and WTA background
- **`/media`** — Press coverage, media mentions, press kit

**Admin Panel (`/admin/*`):**
- Appointments CRUD (+ bulk CSV import, soft delete)
- Pledge management with status history audit trail
- Charter demand status updates (demand text is read-only)
- Bulletin publishing with schedule/publish/unpublish
- Historical data management and CSV import
- Media coverage entries
- Settings: scoring weights (must sum to 100%), target threshold, verdict labels (text + colour), site metadata, social sharing defaults
- Role-based access: Administrator (full) and Editor (content only)
- Audit logging of all admin actions

### Core Technical Requirements
- **Mobile-first**, low-bandwidth optimised (3G target: under 3s homepage load)
- **WCAG 2.1 Level AA** accessibility
- Google Lighthouse mobile score **>= 80**
- **Dark/light mode** support
- **Configurable brand colours** via CSS variables (brand colour TBD from WTA)
- **No public login required** — all data publicly accessible
- **Replicable architecture** — clean enough to replicate for other states

---

## 2. Requirements Triage

### Clear Requirements (Can proceed now)

| Area | What's Clear |
|------|-------------|
| **Site structure** | All 9 public pages and 8 admin sections are fully specified with routes |
| **Data entities** | Cabinet Appointment, Commitment Pledge, Charter Demand Item, Accountability Bulletin — all fields, types, required/optional fully defined |
| **Scoring dimensions** | 4 dimensions with default weights (40/30/20/10) — structure is clear even if exact formulas aren't |
| **Appointments table** | Columns, filters, search, CSV export fully specified |
| **Pledge statuses** | Pending / In Progress / Fulfilled / Not Fulfilled |
| **Charter response statuses** | Acknowledged / Not Yet Addressed / Action Taken |
| **Admin auth model** | Email/password, 2 roles (Admin/Editor), 30-min session timeout, audit logging |
| **Non-functional reqs** | Performance, accessibility, security, browser support all specified |
| **Design principles** | "Accountability journalism meets government data portal", data-forward, configurable colour system |
| **Delivery phases** | Phase 0 then Phase 1 (MVP, end Oct) then Phase 2 (end Nov) then Phase 3 (end Dec) then Handover |

### Awaiting Client Confirmation

These items are flagged in the **Clarification Document** as unresolved:

| # | Question | Impact | Blocking? |
|---|----------|--------|-----------|
| **Q1** | Exact formulas for each of the 4 scoring dimensions (Cabinet Gender Ratio, Policy Commitment, Charter Demand Response, Inclusive Governance Practice) | Cannot implement scoring calculation engine | **Yes** for scoring; No for UI layout |
| **Q2** | Which appointment categories count toward the 35% target? (Cabinet Commissioners only? Or also Special Advisers, Board, LGA?) | Affects score calculation and tracker filtering | **Yes** for scoring |
| **Q3** | Domain ownership and hosting account — who owns, who pays | Affects deployment architecture | **No** for dev work |
| **Q4** | Service accounts (Supabase, Vercel, Cloudinary, Plausible) — WTA-owned or developer-managed? | Affects environment setup | **No** for local dev |
| **Q5** | Post-handover maintenance agreement — who maintains after 30-day support? | No dev impact now | **No** |
| **Q6** | Tight end-of-October MVP target — is it confirmed given the remaining timeline? | Affects scope prioritisation | **Partially** |
| **Q7** | 48-hour review windows — realistic given WTA's capacity? | Process question | **No** |
| **Q8** | Formal acceptance criteria per phase | Affects delivery sign-off | **No** for development |
| **Q9** | Official dashboard name | Affects branding/SEO metadata | **No** — use placeholder |
| **Q10** | Brand colours, logos (SVG) | Affects theming | **No** — use configurable placeholder palette |
| **Q11** | Yoruba translated text for labels | Nice-to-have feature | **No** |
| **Q12** | Charter of Demands full text | Needed for `/charter` page data seeding | **Yes** for charter page content |
| **Q13** | Historical research dataset (1999-2026) | Needed for `/history` page data seeding | **Yes** for history page data |

### Contradictions and Tensions

| Issue | PRD Says | Tech Proposal Says | Resolution Needed |
|-------|---------|-------------------|-------------------|
| **CMS approach** | "Headless CMS (Strapi, Directus, Payload) recommended" | "Custom admin within Next.js + Supabase" | Tech proposal's approach is more practical for the timeline; PRD says "developer discretion" on stack. **No conflict** — PRD explicitly allows developer choice |
| **Database** | "PostgreSQL recommended; SQLite acceptable" | "Supabase (PostgreSQL)" | **Aligned** |
| **Scoring weights** | "Configurable via admin, not hardcoded" + table shows fixed defaults | Proposal mentions "configurable rules" | **Aligned** on intent, but exact calculation formulas are unspecified |
| **File storage** | "Cloudinary or AWS S3" | "Supabase Storage or Cloudinary" | **Minor variance** — either works. Supabase Storage is simpler if using Supabase for DB |
| **F-02 vs F-12 priority** | F-02 says CSV export is "Must Have"; F-12 lists Data Export as "Should Have" | — | CSV export for appointments table is Must Have (per F-02 notes). F-12's "Should Have" refers to broader export features |

---

## 3. Proposed Folder Structure

Based on the existing Next.js 16 + App Router + TypeScript + Tailwind + shadcn/ui setup already in the repo:

```
src/
├── app/                              # Next.js App Router
│   ├── globals.css                   # Global styles + CSS variables
│   ├── layout.tsx                    # Root layout (fonts, providers, metadata)
│   ├── page.tsx                      # / — Home (Live Scorecard)
│   ├── tracker/
│   │   └── page.tsx                  # /tracker — Cabinet Appointment Tracker
│   ├── index-score/                  # /index-score — Gender Accountability Index
│   │   └── page.tsx                  #   (named "index-score" to avoid conflict)
│   ├── pledges/
│   │   └── page.tsx                  # /pledges — Commitment Pledge Archive
│   ├── charter/
│   │   └── page.tsx                  # /charter — Charter of Demands
│   ├── history/
│   │   └── page.tsx                  # /history — Historical Data 1999–2026
│   ├── bulletins/
│   │   └── page.tsx                  # /bulletins — Monthly Bulletins
│   ├── about/
│   │   └── page.tsx                  # /about — About page
│   ├── media/
│   │   └── page.tsx                  # /media — Media Centre
│   └── admin/                        # Admin panel (auth-protected layout)
│       ├── layout.tsx                # Admin layout with sidebar nav
│       ├── page.tsx                  # /admin — Admin dashboard
│       ├── login/
│       │   └── page.tsx              # /admin/login
│       ├── appointments/
│       │   └── page.tsx              # /admin/appointments
│       ├── pledges/
│       │   └── page.tsx              # /admin/pledges
│       ├── charter/
│       │   └── page.tsx              # /admin/charter
│       ├── bulletins/
│       │   └── page.tsx              # /admin/bulletins
│       ├── history/
│       │   └── page.tsx              # /admin/history
│       ├── media-entries/
│       │   └── page.tsx              # /admin/media-entries
│       └── settings/
│           └── page.tsx              # /admin/settings
│
├── components/
│   ├── ui/                           # shadcn/ui primitives (button, card, etc.)
│   ├── layout/                       # Shared layout components
│   │   ├── header.tsx                # Public site header + nav
│   │   ├── footer.tsx                # Public site footer
│   │   ├── mobile-nav.tsx            # Mobile navigation drawer
│   │   └── theme-toggle.tsx          # Dark/light mode toggle
│   ├── home/                         # Home page sections
│   │   ├── hero-score.tsx            # Live score display + gauge
│   │   ├── metrics-strip.tsx         # Key metrics strip
│   │   ├── activity-feed.tsx         # Recent activity feed
│   │   ├── nav-cards.tsx             # Quick navigation cards
│   │   └── share-card.tsx            # Social sharing component
│   ├── tracker/                      # Cabinet tracker components
│   │   ├── tracker-summary.tsx       # Summary header with charts
│   │   ├── appointments-table.tsx    # Searchable/filterable table
│   │   └── trend-chart.tsx           # Time-series trend chart
│   ├── index-score/                  # Accountability index components
│   │   ├── score-overview.tsx        # Composite score display
│   │   ├── dimension-card.tsx        # Individual dimension breakdown
│   │   └── methodology-note.tsx      # Collapsible methodology section
│   ├── pledges/                      # Pledge archive components
│   │   ├── pledge-card.tsx           # Individual pledge card
│   │   ├── pledge-filters.tsx        # Filter controls
│   │   └── pledge-summary.tsx        # Summary counters
│   ├── charter/                      # Charter components
│   │   ├── demand-item.tsx           # Individual demand with status
│   │   └── charter-summary.tsx       # Percentage summary
│   ├── history/                      # Historical data components
│   │   ├── admin-comparison-chart.tsx# Administration comparison chart
│   │   ├── history-table.tsx         # Filterable data table
│   │   └── lga-view.tsx              # LGA-level breakdown
│   ├── bulletins/                    # Bulletin components
│   │   ├── bulletin-card.tsx         # Bulletin card
│   │   └── bulletin-featured.tsx     # Featured latest bulletin
│   ├── charts/                       # Shared chart components
│   │   ├── donut-chart.tsx           # Gender ratio donut
│   │   ├── bar-chart.tsx             # Comparison bar chart
│   │   ├── line-chart.tsx            # Trend line chart
│   │   └── progress-gauge.tsx        # Score gauge/progress ring
│   └── shared/                       # Cross-cutting components
│       ├── status-badge.tsx          # Coloured status badges
│       ├── social-share-buttons.tsx  # WhatsApp, FB, X, link copy
│       ├── pdf-viewer.tsx            # Embedded PDF viewer
│       ├── data-placeholder.tsx      # "Coming soon" placeholder
│       ├── csv-export-button.tsx     # CSV download button
│       └── empty-state.tsx           # Contextual empty states
│
├── lib/
│   ├── utils.ts                      # General utilities (cn, etc.)
│   ├── constants.ts                  # App-wide constants
│   └── format.ts                     # Date/number formatting helpers
│
├── types/
│   ├── appointment.ts                # CabinetAppointment type
│   ├── pledge.ts                     # CommitmentPledge type
│   ├── charter.ts                    # CharterDemandItem type
│   ├── bulletin.ts                   # AccountabilityBulletin type
│   ├── historical.ts                 # HistoricalData type
│   ├── media.ts                      # MediaCoverage type
│   ├── scoring.ts                    # ScoringDimension, VerdictLabel types
│   └── index.ts                      # Re-exports
│
├── data/                             # Mock data for development
│   ├── mock-appointments.ts
│   ├── mock-pledges.ts
│   ├── mock-charter.ts
│   ├── mock-bulletins.ts
│   ├── mock-historical.ts
│   ├── mock-media.ts
│   └── mock-scoring.ts
│
├── config/
│   └── site.ts                       # Site metadata, nav links, brand config
│
└── hooks/
    ├── use-theme.ts                  # Theme toggle hook
    └── use-media-query.ts            # Responsive breakpoint hook
```

> [!NOTE]
> **Route naming**: The PRD specifies `/index` for the scoring page, but `index` is a reserved concept in Next.js routing. I propose `/index-score` as the route. Alternatively, we could use `/score` or `/accountability-index`. **Needs your input.**

---

## 4. Recommended Initial Dependencies

### Already Installed (keep as-is)
| Package | Purpose |
|---------|---------|
| `next@16.4.0` | Framework (App Router, SSR/SSG, API routes) |
| `react@19.3.0` / `react-dom@19.3.0` | UI library |
| `tailwindcss@4` | Styling (already configured with Turbopack) |
| `shadcn@4.21.5` + `radix-ui` | Component library primitives |
| `class-variance-authority` | Component variant management |
| `lucide-react` | Icon library |
| `tw-animate-css` | Animation utilities |
| `typescript@5` | Type safety |

### Recommended Additions
| Package | Purpose | Why |
|---------|---------|-----|
| **`recharts`** | Charts (bar, line, donut, gauge) | Built on React, responsive by default, lightweight. Better React integration than Chart.js. Recommended by shadcn/ui for chart components |
| **`@tanstack/react-table`** | Appointments table | Headless, accessible data table with sorting, filtering, pagination. Powers the `/tracker` table |
| **`next-themes`** | Dark/light mode | Handles theme persistence, SSR flash prevention, system preference detection. Required by PRD |
| **`date-fns`** | Date formatting | Lightweight, tree-shakeable. For "Last updated: 3 October 2026" style dates |
| **`zod`** | Schema validation | Already in node_modules (transitive dep). For form validation and type-safe data schemas |

### Defer Until Client Confirms
| Package | Why Deferred |
|---------|-------------|
| **`@supabase/supabase-js`** | Database/auth — awaiting hosting ownership confirmation (Q3, Q4) |
| **`next-auth`** / auth library | Auth approach depends on Supabase vs custom (Q4) |
| **Cloudinary SDK** or **Supabase Storage** | File storage decision pending (Q4) |
| **`@sentry/nextjs`** | Error monitoring — deployment config needed first |
| **`plausible-tracker`** | Analytics — awaiting account setup (Q4) |
| **`next-pwa`** or service worker tooling | Offline-first is "Should Have" — Phase 3 concern |

---

## 5. What Can Be Safely Built Now (with Mock Data)

### Safe to Build — No Unresolved Dependencies

| Component / Page | Rationale |
|-----------------|-----------|
| **TypeScript type definitions** (`types/`) | All 4 data entity schemas are fully specified in the PRD |
| **Mock data files** (`data/`) | Enables all UI development without a database |
| **Site config** (`config/site.ts`) | Nav links, placeholder brand name/colours |
| **Public layout** — Header, Footer, Mobile Nav | Structure is clear; uses placeholder brand colours |
| **Theme toggle** (dark/light mode) | No data dependency |
| **Home page layout** — Hero section, Metrics strip, Nav cards | Use mock score/metrics; structure is fully specified |
| **Activity feed component** | Mock recent entries |
| **Cabinet Tracker page** — Summary header, Appointments table, Trend chart | All columns, filters, and behaviours fully specified |
| **CSV export button** | Pure client-side, no backend needed |
| **Pledge Archive page** — Cards, filters, summary counters | Status options and display fully defined |
| **Charter page** — Demand items with status badges | Layout clear; use placeholder demand text |
| **Bulletins page** — Card grid with featured bulletin | Layout and fields fully specified |
| **Historical Data page** — Charts and table layout | Use illustrative mock data for chart shapes |
| **About page** | Static content layout; use placeholder WTA description |
| **Media Centre page** | Card list layout with mock entries |
| **Status badges** (Pending/Fulfilled/etc.) | All status enums are defined |
| **Social share buttons** (WhatsApp, FB, X, link copy) | Pure client-side; no backend needed |
| **Donut chart, Bar chart, Line chart, Progress gauge** | Reusable chart components with mock data |
| **Data placeholder / empty state** | PRD requires no blank pages — "data will be available by [date]" |

### Do NOT Build Yet

| Component | Reason |
|-----------|--------|
| **Scoring calculation engine** | Exact formulas for all 4 dimensions unconfirmed (Q1, Q2) |
| **Database schema / Supabase setup** | Hosting ownership and service accounts unconfirmed (Q3, Q4) |
| **Authentication system** | Auth provider and account ownership unconfirmed (Q4) |
| **Admin panel CRUD operations** | Requires database; build UI shells only |
| **File upload / storage** | Storage provider unconfirmed |
| **Production data import** (CSV bulk import) | Dataset not yet provided (Q12, Q13) |
| **Social sharing image generation** (server-side PNG) | Requires backend infrastructure; can mock the UI |
| **Email subscription** (Mailchimp integration) | Nice-to-have, service account unconfirmed |
| **Yoruba language toggle** | Translated text not yet provided (Q11) |
| **Service worker / offline caching** | Phase 3 concern |

---

## 6. Technical Risks and Timeline Dependencies

### High Risk

| Risk | Impact | Mitigation |
|------|--------|------------|
| **Scoring formulas undefined** | The headline feature (the score on the homepage) cannot be verified or tested until WTA confirms exact calculation rules for all 4 dimensions | Build the scoring UI with mock data; design the scoring engine to be pluggable so formulas can be swapped in without UI changes |
| **End-of-October MVP target is 16 days away** | The PRD sets Phase 1 MVP (homepage + tracker + admin + deployment) for end of October 2026. Today is 14 Oct 2026. Even with the project already scaffolded, delivering a fully deployed MVP with working admin in ~16 days is **extremely tight** | Reduce MVP scope: ship homepage + tracker with static/mock data first; admin panel as fast-follow. Clarify with client what "MVP" means minimally |
| **Brand assets not yet provided** | No logo, no confirmed dashboard name, no primary/secondary colours | Build with a configurable CSS variable system. Use a professional placeholder palette. All brand elements can be hot-swapped without code changes |

### Medium Risk

| Risk | Impact | Mitigation |
|------|--------|------------|
| **Charter of Demands text unavailable** | `/charter` page cannot be populated without the ratified Charter document | Build the layout with mock demands; WTA to provide text for data seeding |
| **Historical dataset unavailable** | `/history` charts will use illustrative data only | Mock data is structurally accurate; real data drops in via admin CSV import |
| **Supabase/hosting account ownership unclear** | Deployment could be blocked if accounts aren't set up | Develop entirely locally with mock data; deployment can happen on any provider once accounts are confirmed |
| **Non-technical admin user** | Admin panel must be very intuitive — testing with actual WTA staff will reveal usability gaps | Follow established admin UI patterns (shadcn/ui forms, clear labels, confirmation dialogs, preview mode) |

### Low Risk

| Risk | Impact | Mitigation |
|------|--------|------------|
| **3G performance target** | Under 3s load on 3G is achievable with Next.js SSR + code splitting, but requires intentional optimisation | Use Next.js Image component, lazy-load charts, compress assets. Test with Lighthouse throttling |
| **WCAG 2.1 AA compliance** | Achievable with shadcn/ui (built on Radix, which is accessible by default) + colour contrast testing | Include automated a11y checks; ensure status badges have text labels not just colours |
| **Replicability for other states** | Clean architecture with configurable state name/threshold will enable this | Use config-driven design from the start |

---

## 7. Recommended Immediate Next Steps

> [!IMPORTANT]
> **Before any code changes**, the following decisions need your approval:

1. **Route for scoring page**: `/index-score` (my recommendation), `/score`, or `/accountability-index`?
2. **Approve the folder structure** above — any changes?
3. **Approve the dependency additions**: `recharts`, `@tanstack/react-table`, `next-themes`, `date-fns` (and `zod` for validation)
4. **Confirm scope**: Should I build all safe-to-build components listed in Section 5, or focus on a specific subset first?
5. **MVP scope discussion**: Given we're 16 days from the Phase 1 target, do you want me to prioritise the homepage and tracker first?

Once approved, the work sequence would be:
1. Set up folder structure + types + mock data + site config
2. Build shared components (layout, charts, status badges)
3. Build public page shells in priority order (Home, Tracker, Bulletins, Pledges, Charter, History, About, Media)
4. Build admin layout shell (no CRUD logic, just UI)
5. Polish responsive design + dark mode
