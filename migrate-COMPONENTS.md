# Migration landing pages — component reuse map

These 5 mocks (`migrate-{tableau,looker,power-bi,qlik,thoughtspot}.html`) are built to reuse
existing `sigma-website-sanity` section components. Each page is a stack of section types that
already exist in the marketing renderer (`frontend/app/components/pageBuilder/renderers-marketing.tsx`),
so the dev team can rebuild these as Sanity `pageBuilder` documents with almost no new component code.

Repo reference: `sigmacomputing/sigma-website-sanity`. Section schema in `studio/src/schemaTypes/objects/<type>.ts`;
components in `frontend/sections/app/marketing/<folder>/`.

## Section → existing component

| # | Mock section | Sanity `_type` | Component | Verdict |
|---|---|---|---|---|
| — | Header / nav | (layout, not pageBuilder) | `frontend/sections/global/navbar/Header.tsx` (`app/@header/default.tsx`) | **Reuse as-is** |
| 1 | Hero (headline + 1 CTA + 16:9 Wistia + G2 rating line) | `videoHeroSection` | `.../video-hero/VideoHeroSection.tsx` | **Adapt** — add the "4.5/5 on G2" rating line (or use `homepageHeroSection`'s native `ratings[]`). Real component uses a click-to-play thumbnail; mock uses an inline iframe. |
| 2 | Customer logo strip (grayscale + eyebrow) | `logoCarousel` module (`SimpleLogotypesRow`, `displayMode:'staticRow'`) | `frontend/modules/logotypes-wall-rotation/` | **Reuse as-is** — it's a module embedded via a parent section's `logoCarousel`, not its own block. |
| 3 | "Why teams choose Sigma vs X" — 6 icon cards | `cardsWithIconSection` | `.../cards-with-icon-section/CardsWithIconSection.tsx` (card: `frontend/modules/cards/cards-with-icon/`) | **Reuse as-is** — `columns:3`, each card = `sectionIcon` + title + description. Exact match. |
| 4 | "How the migration works" — dark, 3 numbered steps + 2nd video | `videoCtaSection` + `numberedCardsSection` | `.../video-cta/VideoCtaSection.tsx` + `.../numbered-cards/NumberedCardsSection.tsx` | **Compose / net-new** — no single section pairs dark side-by-side video with numbered steps. `videoCtaSection` gives the dark `primaryBlue900` side-by-side video; `numberedCardsSection` gives the steps. Compose the two, or a small net-new section. |
| 5 | Comparison table (Sigma vs X, 8 rows) + link + footnote | `comparisonTableSection` | `.../comparison-table/ComparisonTableSection.tsx` | **Reuse as-is** — use `variant:'detailed'` (3-col Feature/Sigma/Competitor). Real content pulled from `comparison/sigma-vs-<x>` docs. Auto-renders check/X; `competitorName` + `asOfDate` + `simpleCtaText/Link` (the trailing "see full comparison" link). |
| 6 | Testimonial (centered card: stars + quote + "Verified on G2") | `customerTestimonialSection` / `quoteSection` | `.../customer-testimonial/CustomerTestimonialSection.tsx` | **Adapt / net-new styling** — real component is a 2-column dark quote panel + G2/award badges, not a centered card, and has no "Verified review on G2" button. Either center-ify it or add stars + G2 button to `quoteSection`. |
| 7 | Bottom CTA (blue #1F6FE8 panel + preview image + logo row) | `scheduleADemoSection` | `.../schedule-a-demo/ScheduleADemoSection.tsx` | **Reuse as-is** — the mock mirrors this component's markup verbatim (`rounded-[8px] bg-[#1F6FE8]`, `grid lg:grid-cols-[minmax(0,0.88fr)_minmax(0,1.22fr)]`, white `Button`, right-side image, `logoCarousel` row). This is the `/go/demo-library` closing CTA. |
| — | Footer | (layout) | `frontend/sections/global/footer/Footer.tsx` | Removed on these pages by request (focused landing pages). |

## Feature-card icons (section 3)

Icons render via `SectionIcon` (`frontend/sections/app/marketing/_shared/SectionIcon.tsx`), name→SVG in
`frontend/app/components/pageBuilder/iconMap.ts`. The 6 cards use these `sectionIcon` names (all resolve today):
`globeLock` (Secure Governance), `refreshCw` (Closed-loop Execution / Enterprise SDLC), `brainCircuit`
(AI Ecosystem), `layoutPanels` (AI Applications), `robot` (Sigma Agents), `cable` (Semantic Portability).
The mock's inline SVGs approximate these; the real build should use the named icons.

## Content sources (already real, not invented)

- **Comparison rows, feature-card copy, competitor labels, `asOfDate`** → `comparison/sigma-vs-{tableau,looker,power-bi,qlik,thoughtspot}` docs (Sanity project `9i48iita`/`production`). The mock uses a condensed ~8-row subset per platform.
- **Testimonials** → the verbatim G2 reviews Sigma publishes on those comparison pages (`customerTestimonialSection`).
- **Videos** → the Wistia migration videos (ids: Looker `www153ntj0`, Power BI `kobu9tpeng`, Tableau `ej1hcuhgit`, Qlik `5jbz7dnfho`, ThoughtSpot `py1lk10k4g`). Real build should use `WistiaFacade` (`frontend/modules/wistia`) rather than a raw iframe.
- **Logos** → `cdn.sanity.io/images/9i48iita/production/…` (the same assets the homepage/schedule-a-demo rows use).

## Wrapper conventions to match

- Container: `Container`/`SectionContainer` (`frontend/components/container/Container.tsx`) — `container mx-auto`, `size:'tight'` = `px-4 lg:px-6`, `paddingY:'lg'` = `py-14 lg:py-16`.
- Type: `TYPOGRAPHY.h2` = `font-family-advercase font-normal text-[36px] leading-[1.4] tracking-[-0.01em] lg:text-[48px]`; body1 = `font-sans font-normal text-[16px] leading-[1.3] tracking-[-0.01em] lg:text-[20px]`.
- Card grid shell: `frontend/sections/app/marketing/_shared/CardGridShell.tsx`.

**Bottom line:** sections 2, 3, 5, 7 + header reuse existing components as-is; section 1 (hero) and 6 (testimonial)
are light adaptations; section 4 (dark steps + video) is the one genuinely net-new piece.
