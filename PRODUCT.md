# Nova Data — Product Specification

A single-page marketing website for Nova Data, a BI & AI consulting business based in Brisbane, Australia. Target audience: enterprise stakeholders looking for BI/AI consulting services.

---

## Hosting & Infrastructure

| Component | Provider | Details |
|---|---|---|
| Source code | GitHub | `franciscoexmachina/novadata` repo, `master` branch |
| Website hosting | Vercel | Auto-deploys on every push to `master`. Free tier. |
| Hero background video | Cloudflare R2 | Public bucket URL: `https://pub-061c603ed4cc4a328d0f0c647e18186b.r2.dev/` |
| Domain | GoDaddy | `novadata.com.au` — nameservers pointed to Vercel (`ns1.vercel-dns.com`, `ns2.vercel-dns.com`). DNS A record `@` → `216.198.79.1`. |

**Deploy flow:** edit `index.html` → `git push` → Vercel auto-deploys in ~1 minute.

---

## File Structure

```
/
├── index.html               ← entire site (single file)
├── data-cubes.png           ← image used in About section
├── novadata_logo.jpg        ← logo shown in footer
├── favico.jpg               ← browser favicon
├── revenue_clean_final.html ← source reference for chart component (not served directly)
├── .gitignore               ← excludes node_modules/, .next/, *.mp4, *.docx
└── package-lock.json        ← Next.js dependency lock (scaffolded but unused)
```

**Note:** The project was scaffolded as Next.js (has `package-lock.json`, `next-env.d.ts`, empty `app/`, `components/`, `lib/` dirs) but the entire site is plain HTML in `index.html`. The Next.js framework is not used.

---

## Design System

All styles are inline in `<style>` tags inside `index.html`. No CSS framework.

### Colour Palette (CSS variables)
```css
--bg: #09090b                          /* near-black page background */
--surface: #111113                     /* card/panel background */
--border: rgba(255,255,255,0.07)       /* subtle dividers */
--text-primary: #f4f4f5                /* headings, bold text */
--text-secondary: #a1a1aa              /* body text */
--text-muted: #52525b                  /* labels, footer text */
--accent: #2563eb                      /* primary blue */
--accent-light: #3b82f6               /* hover blue, links */
--accent-glow: rgba(37,99,235,0.15)   /* icon backgrounds */
```

### Typography
- Font: **Outfit** (Google Fonts), weights 300/400/500/600/700/800
- Hero title: `clamp(2.75rem, 6vw, 5rem)`, weight 800, letter-spacing -0.04em
- Section titles: `clamp(2rem, 4vw, 3rem)`, weight 700
- Body: 1.0625rem, line-height 1.7

### Animations
- **fadeUp keyframe:** `opacity 0 → 1, translateY(20px) → 0`, used on hero elements with staggered delays (0s, 0.1s, 0.2s, 0.3s, 0.4s)
- **pulse keyframe:** badge dot pulses opacity/scale on 2s loop
- **marquee keyframe:** tech strip scrolls `translateX(0) → translateX(-50%)` over 30s linear infinite
- **scroll reveal:** `.reveal` class starts `opacity:0, translateY(24px)`. `IntersectionObserver` (threshold 0.08, rootMargin -40px bottom) adds `.visible` class on enter, triggering `opacity:1, translateY(0)` over 0.7s cubic-bezier(0.16,1,0.3,1). Applied to: about, services, experience, contact sections.
- **service card stagger:** each `.service-card` gets `transitionDelay: i * 60ms` via JS

---

## Sections (top to bottom)

### 1. Nav (fixed)
- Position: fixed top, full width, z-index 100
- Background: `rgba(9,9,11,0.6)` with `backdrop-filter: blur(16px)`. Transitions to `rgba(9,9,11,0.92)` on scroll > 40px.
- Left: logo "Nova **Data**" — "Data" in `--accent-light`
- Centre: links — About, Services, Experience, Contact (hidden on mobile ≤1024px)
- Right: "Get in touch" CTA button → `mailto:contact@novadata.com.au`

### 2. Hero
- Full viewport height (`100dvh`), dark theme
- **Background:** `<video>` element, `autoplay muted playsinline`, plays once (no loop), src: `https://pub-061c603ed4cc4a328d0f0c647e18186b.r2.dev/Bus_to_Dash.mp4`, opacity 0.55, `object-fit: cover`
- **Overlay:** two layered gradients — diagonal dark-to-transparent left-to-right, plus bottom-up full black fade
- **Badge:** pill with pulsing blue dot — "Available for consulting engagements"
- **Headline:** "Data that / drives decisions." — "drives decisions." in blue gradient text (`#60a5fa → #2563eb → #1d4ed8`)
- **Subheadline:** "Senior BI & AI consultants with 15+ years transforming raw enterprise data into strategic intelligence — across SAP, Power BI, and modern cloud stacks."
- **CTA buttons:** "Explore services" (primary, → #services) + "View LinkedIn" (ghost, → #contact)
- **Stats block:** position absolute, bottom-right, 3 items stacked right-aligned — "15+ Years experience", "30+ Enterprise clients", "6 Industry sectors". Hidden on mobile ≤640px.
- **Playback rate:** hero video plays at 0.7x speed via JS `canplay` event listener

### 3. Tech Strip
- Scrolling infinite marquee of 18 technology tags
- Tags: SAP HANA, Microsoft Power BI, Azure Data Factory, SAP BusinessObjects, SAP Analytics Cloud, SQL Server, SAP Signavio, Celonis, Oracle, Azure Logic Apps, SAP BW/4HANA, SAP Data Services, Crystal Reports, Analysis for Office, MS DevOps, Postman, SAP Lumira, HP ALM
- Marquee is duplicated (two identical `.tech-marquee-inner` divs) to create seamless loop
- Faded edges via `mask-image: linear-gradient(to right, transparent, black 10%, black 90%, transparent)`

### 4. About
- Two-column grid (1fr 1fr), 5rem gap, collapses to 1 column on tablet
- **Left:** `data-cubes.png` image in a 16/10 aspect ratio rounded container (border-radius 16px), with a subtle white border overlay via `::after` pseudo-element
- **Right:** section label "About", title "BI architecture meets AI innovation.", two body paragraphs, 4 bullet points with SVG checkmarks in accent blue
- Body text: "Based in Brisbane, QLD — we partner with enterprise stakeholders to design and deliver business intelligence solutions that hold up in production. Our work spans architecture, development, data integration, and governance across on-premise, IaaS, and SaaS platforms."
- Second paragraph: "From modernising legacy reporting landscapes for StartUps to delivering complex multidimensional BI for Mining Industry Titans — we bring both depth and breadth to every engagement."
- Bullet points:
  1. Comprehensive SAP BusinessObjects expertise with cross-industry reach
  2. Deep expertise in dimensional modelling and virtual HANA data models
  3. Proven track record integrating SaaS data via API-based pipelines
  4. Former 9-year senior consultant at AU/NZ SAP lead partner DXC Technology

### 5. Services
- Section header: two-column grid — left: title "What we deliver for your organisation." / right: body text
- Body: "End-to-end consulting from strategy to production — across SAP, Microsoft, and AI-enabled BI platforms. Designed for asset-intensive industries, utilities, rail, oil & gas, and government."
- **6 service cards** in a 3×2 grid with 1px gap borders on `--border` background, border-radius 16px, overflow hidden. Each card has an accent-blue icon box (SVG), service name, and description.
  1. **BI Architecture & Design** — Blueprint enterprise BI landscapes on SAP HANA, BW/4HANA, and Microsoft Azure
  2. **Power BI & SAP Dashboards** — Executive-grade dashboards on Power BI, SAC, BusinessObjects, Lumira
  3. **AI-Enabled Process Automation** — Intelligent ETL in Azure Data Factory, process mining with SAP Signavio and Celonis
  4. **Data Integration & ETL** — Pipelines connecting RDBMSs with SaaS APIs using ADF, Logic Apps, SAP Data Services
  5. **Platform Migration & Upgrades** — BO 4.x upgrades to HANA XSA migrations across Rail, Utilities, Government
  6. **Data Governance & Training** — Governance frameworks, self-service BI rollout, team training

### 6. Experience
- Layout: `grid-template-columns: 320px 1fr`, 5rem gap. On tablet (≤1024px) the sidebar is hidden entirely.
- **Left sidebar (sticky, top: 6rem):** animated revenue line chart in a rounded dark card (`--surface` bg, `--border` border)
  - Built with Chart.js 4.4.1 (loaded from cdnjs CDN)
  - Data: `[4,6,5,9,11,10,15,18,17,22,26,25,32,38,42,58]` (16 points, ascending trend)
  - Line colour: `#1D9E75` (green), fill with `rgba(29,158,117,0.07)`
  - No axes labels, no legend, no tooltip
  - Animation: chart built on load (all nulls), then `IntersectionObserver` (threshold 0.4) triggers `startReveal()` when canvas enters viewport. Points revealed one at a time every 140ms after a 300ms initial delay. No replay button.
- **Right:** 3 work history entries, each `grid-template-columns: 120px 1fr`
  1. **2022–2025** — BI Developer (Contract), Rio Tinto — Oil & Gas / Resources. Power BI dashboards for Occupational Health, Forwood Risk Control Logic Apps pipeline, BO 4.3 upgrade, Archer Snapshots HANA XS interface.
  2. **2013–2022** — Lead Consultant SAP BI/HANA, DXC Technology (formerly Oxygen) — AU/NZ. Clients: Queensland Rail, TasNetworks, ElectraNet, Jemena, Oilsearch. IoT real-time dashboards on Lumira 2.1.
  3. **2011–2013** — Integration Specialist, Telecom New Zealand (now Spark NZ).

### 7. Contact / CTA
- Two-column grid — left: large heading "Ready to turn your data into decisions?" / right: body + contact links + CTA button
- Body: "Available for consulting engagements in Brisbane and remote across AU/NZ. Let's discuss how we can help your organisation make better use of its data."
- Contact links: email `contact@novadata.com.au` + LinkedIn `https://www.linkedin.com/in/franciscoalmeida-novadata/`
- CTA button: "Start a conversation" → `mailto:contact@novadata.com.au`

### 8. Footer
- Three elements stacked/spaced: `novadata_logo.jpg` (height 48px, opacity 0.85), "© 2026 Nova Data. Brisbane, QLD.", "Nova Data"

---

## Assets

| File | Used in | Hosted |
|---|---|---|
| `Bus_to_Dash.mp4` | Hero background video | Cloudflare R2 (not in git) |
| `data-cubes.png` | About section image | Git repo / Vercel |
| `novadata_logo.jpg` | Footer logo | Git repo / Vercel |
| `favico.jpg` | Browser favicon | Git repo / Vercel |
| `xls_to_dash.mp4` | Not currently used | Local only |
| `BI_Chart_Animation_for_Website.mp4` | Not currently used | Local only |
| `clean_office.mp4` | Not currently used | Local only |

---

## JavaScript Behaviours

All JS is inline at the bottom of `index.html` before `</body>`.

1. **Scroll reveal:** `IntersectionObserver` on all `.reveal` elements (threshold 0.08, rootMargin -40px bottom). Adds `.visible` class on intersect, then unobserves.
2. **Service card stagger:** loops `.service-card` elements, sets `transitionDelay: i * 60ms`.
3. **Hero video playback rate:** sets `playbackRate = 0.7` on `#hero-vid` immediately and on `canplay` event.
4. **Nav background:** `scroll` event listener (passive) darkens nav from `rgba(9,9,11,0.6)` to `rgba(9,9,11,0.92)` when `scrollY > 40`.
5. **Revenue chart:** IIFE that dynamically appends Chart.js script tag from CDN. On load: builds empty chart, registers `IntersectionObserver` (threshold 0.4) on canvas. On intersect: triggers point-by-point reveal animation.

---

## Responsive Breakpoints

- **≤1024px:** nav links hidden, section padding → 2rem, about/services-header/cta go single column, experience sidebar hidden, services grid → 2 columns
- **≤640px:** services grid → 1 column, hero stats hidden

---

## Contact & Identity
- Brand name: Nova Data
- Email: `contact@novadata.com.au`
- LinkedIn: `https://www.linkedin.com/in/franciscoalmeida-novadata/`
- GitHub user: `franciscoexmachina`
- Location: Brisbane, QLD, Australia
