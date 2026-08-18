# SEO_CANON.md
# Canon of professional SEO: demand → architecture → technique → content → monitoring.
# Owner: @SEO (roles/ROLE_SEO.md). Dual search: Google + search engines. Horizon 2026: classic SERP + AI answers.
# Main shift: SEO is not "meta-tags after handover", it is an entry point into design. Semantics — BEFORE information architecture and design.

> **Three layers, in this order:** DEMAND (what people search for → which pages exist) → TECHNIQUE (the search engine can read and render quickly) → TRUST (content, locality, E-E-A-T). Skipping the first layer cannot be compensated by the other two: a beautiful, fast site without pages matching demand is invisible.

---

## §1. SEMANTIC CORE — the foundation (CORE mode, before IA and design)

### 1.1 Methodology (5 steps, deterministic)

```
S1. BUSINESS MARKERS: from BUSINESS_LOGIC/INDUSTRY INTELLIGENCE extract services/products/client problems
    in the CLIENT'S words (not "comprehensive B2C cleaning" but "apartment cleaning after renovation"). 10–30 markers.
S2. EXPANSION: for each marker — Keyword tools (+ region!), Google Keyword Planner, search
    suggestions (suggest), "People also search", competitor SERP (top-10 titles). Goal: ×10–30 queries per marker.
S3. CLEANING: minus-words (free/DIY/vacancies/second-hand — if not our intent), zero-frequency,
    competitor brands, irrelevant geo.
S4. CLUSTERING BY INTENT: group by query meaning and by SERP overlap
    (queries with a shared SERP = one cluster). NOT by shared word.
S5. PRIORITISATION: frequency × commercial value × achievability (competition) → content waves.
```

### 1.2 Intents (query type = page type)

| Intent | Markers | Page for it |
|--------|---------|-------------|
| Commercial | buy, price, order, [service]+[city] | service/product/pricing |
| Local | nearby, [district], address, opening hours | GEO landing, contacts, business listings |
| Informational | how, why, which is better, comparison | article/guide/FAQ (leads to commercial) |
| Navigational | [brand], [brand]+reviews | home, "about us", reviews |

**Iron rule:** `1 cluster = 1 page = 1 intent = 1 H1`. Two pages for one cluster =
cannibalisation (they compete with each other); one landing page "about everything" = invisible for everything.

### 1.3 Output — `docs/artifacts/SEMANTIC_CORE_[PROJECT].md`

Table: `Cluster · Intent · Frequency (by region) · Page (URL) · Priority/wave · Status`.
This is NOT a "file for later" — @SEO builds the page map from it (§2), and @CREATOR/@DESIGN receive
ready meanings for headlines instead of invented ones.

---

## §2. ARCHITECTURE FROM DEMAND — page map and URL

- **Page map** is derived from the core: Home · Service hubs · Service pages (by clusters) ·
  GEO pages (§7) · Cases/portfolio · Pricing · About/team · Reviews · Blog/guides (info-clusters) · FAQ · Contacts.
- **URL grammar:** human-readable, short, hierarchical: `/services/cleaning-after-renovation/`,
  `/services/cleaning-after-renovation/london-south/`. No dates in service URLs, no `?id=`, one slash format,
  lowercase, hyphens. URL does not change after indexation (otherwise 301).
- **Internal linking:** hub → services → related (block "also ordered with"); breadcrumbs everywhere
  (+ BreadcrumbList markup); any page ≤ 3 clicks from home; internal link anchors =
  cluster sub-queries, not "read more".
- **Catalogue pagination:** `rel=canonical` on itself for each page, `<a href>` links (not "show more"
  on JS without links), or a "view all" page.

---

## §3. RENDERING — architectural decision #1 (Law 29)

> **The problem with our default stack:** Vite React SPA delivers an empty `<div id="root">` to the search engine.
> Google will re-render JS with a delay and a budget, other search engines — worse and unstably. For the showcase this is
> invisibility by design. The decision is made by @ARCH via ADR at the start; the requirement is set by @SEO.

| Contour | Default solution | When otherwise |
|---------|----------------------|-------------|
| Showcase/landings/blog (indexable) | **SSG** — static on build: Astro (islands; Mantine not needed on showcase) or Next.js SSG, or vite-prerender for small sites | **SSR**, if page content is dynamic (catalogue from DB, prices from API) — Next/Remix |
| `/app`, `/admin` (operational) | SPA as-is + `noindex, nofollow` + closed in robots | never indexed |
| Hybrid (our typical) | Showcase — separate application (SSG) on the same domain; SPA lives at `/app` | reverse proxy routes; showcase styles — world tokens (VISUAL_CONCEPT), no Mantine runtime |

**Acceptance criterion (TECH audit):** `curl -A "Googlebot" URL` (without JS) returns full content:
H1, texts, navigation links, schema. Empty → 🔴, showcase deploy is blocked.

---

## §4. TECHNICAL MINIMUM (checklist canon; verified by @SEO TECH, executed by @DEV)

### 4.1 Indexation
```
□ robots.txt: content open; /app,/admin,/api, search, UTM duplicates closed; css/js/images NOT closed; Sitemap: line present
□ sitemap.xml generated automatically on build (only 200 canonical URLs, honest lastmod)
□ canonical on each page (self-referencing; UTM/filters → to the clean URL)
□ 301 (not 302) for moves; www↔without-www and http→https — one 301; trailing slash — one variant
□ 404 returns 404 code (not a 200-stub); permanently deleted — 410
□ no duplicates: /page, /page/, /Page, ?utm= — one canonical
```

### 4.2 Meta and headings (formulas — §5)
```
□ title is unique on each page, 50–60 chars, primary cluster query closer to the start
□ meta description is unique, 140–160 chars, offer + CTA (affects CTR, not ranking)
□ H1 one, = cluster intent, ≠ title verbatim; H2–H3 = cluster sub-queries (heading grammar)
□ Open Graph + twitter card (sharing = traffic and behavioural signals)
□ favicon, lang="en" (or by language), charset, viewport
```

### 4.3 schema.org markup (JSON-LD, table by type)

| Page/entity type | Markup |
|-----------------|--------|
| Organisation (all pages) | Organization / **LocalBusiness** (subtype by niche: BeautySalon, AutoRepair, Dentist, MovingCompany…) + logo, sameAs |
| Service | Service (+ Offer with price "from", areaServed) |
| Product | Product + Offer (+ AggregateRating if real reviews exist) |
| FAQ block | FAQPage (questions = info sub-queries of the cluster) |
| Article | Article + author (persona with E-E-A-T, §8) |
| Breadcrumbs | BreadcrumbList |
| Reviews | Review/AggregateRating — ONLY real ones; fake markup = manual penalties |

Validation: Rich Results Test + Search Console Rich Results — in CI check or in TECH audit.

### 4.4 Speed and CWV (field metrics, not just lab)
```
Budgets (field, CrUX): LCP ≤ 2.5s · INP ≤ 200ms · CLS ≤ 0.1 (our §5/V3 is stricter)
□ hero image: priority loading (fetchpriority=high), not lazy; everything else — lazy
□ images: AVIF/WebP + width/height (reserve — LAYOUT_INVARIANTS §5), srcset for viewports
□ fonts: self-host, woff2, preload display font, font-display: swap; ≤ 2 families (DNA axis 4)
□ showcase JS budget ≤ 150KB gzip; third-party scripts — defer/after interaction (including analytics, chats)
□ TTFB ≤ 800ms (SSG solves this by definition; SSR — cache)
Connection: MOTION_LIBRARY §VII performance rails (Lighthouse ≥85) remain; CWV — field supplement.
```

### 4.5 Search engine regional specifics
```
□ Webmaster tools: site added, region assigned, sitemap submitted, "Quality" without critical issues
□ Regional targeting is critical; commercial factors (prices/phone/address visible);
  behavioural signals (genuine page usefulness; click manipulation = ban)
□ Analytics (+ session recording consciously) — does not block render (async, after interaction)
```

---

## §5. ON-PAGE RECIPES (executable formulas)

**Title:** `[Primary cluster query] — [benefit/offer] | [Brand]` · services+geo: `[Service] in [City] — from [price], [timeline/guarantee]`.
**Description:** `[What they get] in [time/condition]. [2–3 proof facts: years, projects, guarantee]. [CTA with phone/action].`
**H-grammar:** H1 = intent ("Post-renovation cleaning in Manchester"); H2 = sub-queries (Pricing · What is included ·
Timelines · How to order · Work examples · FAQ); H3 — details. Heading = promise of the section, not decoration.

**Skeleton of a service sales page (content minimum, delivered by @SEO ONPAGE → @DESIGN):**
```
H1 (intent) → offer paragraph (benefit+numbers) → trust block (years/projects/guarantees) → what is included (list from core)
→ pricing (table/from) → examples/cases (real photos) → how to order (steps) → FAQ (5–8 info sub-queries,
FAQPage schema) → CTA + NAP. Volume: as much as the intent needs (usually 400–1200 words) — not "for volume".
```
**Alt grammar:** alt = what is in the photo + query context, no spam ("master polishes parquet — post-renovation cleaning").
**Thin pages are forbidden:** a page without a unique answer to the intent → merge/strengthen/do not create.

---

## §6. CONTENT PRIMACY (intersection with design)

Order for showcase: `SEMANTIC_CORE → SEO_ONPAGE (H-structure+text skeleton) → @DESIGN SPEC → layout`.
Design serves the content, not cuts it: if the mockup requires "removing the FAQ, it ruins the composition" —
the conflict is resolved by page intent (commercial page lives by the semantic core), aesthetics finds the form
(accordion within the project world), does not delete the meaning. Real texts and photos — before final layout; lorem = 🟡.

---

## §7. LOCAL SEO (typical niches — local businesses: salons, services, clinics)

```
□ Google Business Profile + local map listings: 100% complete (categories, services with pricing, real photos
  ≥10, hours), links to site; reviews — collection process (QR/link after visit), reply to all
□ NAP consistency: Name/Address/Phone identical on site, maps, social media, directories
□ On site: address+phone in header/footer as text (not image), interactive map on contacts,
  LocalBusiness schema with geo, opening hours
□ GEO pages: city/district — ONLY with unique content (branch address, local cases, map, team,
  area pricing). Templated "[service] in [substitute district]" without uniqueness = duplicates = filter
□ Local links: maps, industry directories, local media — quality, not quantity
```

---

## §8. E-E-A-T AND TRUST

Experience·Expertise·Authoritativeness·Trustworthiness — what distinguishes a business from a doorway site:
"About us" page with real people and licences; article authors — personas with credentials (schema author);
cases with numbers and before/after photos; real contacts, company details, service agreement; privacy policy;
reviews from external platforms (widget/screenshots with link). Stock-photo smiles and "team of professionals"
without names — anti-pattern (and per TASTE GATE K8 too).

---

## §9. AI SEARCH (2026 SERP: AI Overviews, AI assistants, ChatGPT search)

- Page = **extractable answer**: direct answer to the intent in the first 1–2 paragraphs, then details;
  lists/tables/steps — machine-readable structure; FAQ blocks.
- schema.org (§4.3) — main channel for entity understanding by AI search.
- `llms.txt` in the root (map of key pages with descriptions for LLM crawlers) — cheap, always do it.
- Citability: unique data (own numbers, pricing, timelines) — what AI cites with a link.
- Forbidden: cloaking "for bots", hidden text, prompt-injection in content.

---

## §10. ANTI-PATTERNS (🔴 in TECH audit)

SPA showcase without SSG/SSR (§3) · cannibalisation (2 pages for one cluster) · templated GEO duplicates ·
keyword stuffing and "SEO walls of text" in footers · hidden text/links · link spam purchases ·
title=H1=description identical across the site · robots blocking CSS/JS · infinite scroll without href pagination ·
content "for robots" instead of a human answer · click manipulation · Review-schema without real reviews ·
redirect chains > 1 hop · changing URL structure without a 301 map.

---

## §11. MONITORING AND CYCLE (MONITOR mode)

```
At deploy (mandatory): Search Console + Search Webmaster connected, sitemap submitted,
  region set, Analytics installed and not blocking render.
Metrics in docs/artifacts/METRICS_REGISTRY.md (M-SEO-XX): indexation (pages in index / in sitemap),
  rankings by core (top-3/10/30 by waves), organic traffic and conversions from organic, field CWV.
Cadence: 2 weeks — indexation and crawl errors; 6–8 weeks — first rankings, fixing title/content
  of underperforming clusters; quarter — report, new content wave from core (S5-priorities).
Live edits: title/content — freely; URL — only with 301; removing pages — 410 + clean up links.
```

---

Reference: `roles/ROLE_SEO.md` (process/modes/gate) · `roles/ROLE_ARCH.md` (rendering-ADR) · `roles/LAYOUT_INVARIANTS.md` §5 (CLS) · `roles/MOTION_LIBRARY.md` §VII (perf) · `roles/METRICS_PROTOCOL.md` (M-SEO) · `roles/CONCEPT_ANATOMY.md` §4 (references ≠ competitor SERP: SERP — intent map, differentiation — through the world)
Version: 1.0 (system v6.21) | 2026-07-06
