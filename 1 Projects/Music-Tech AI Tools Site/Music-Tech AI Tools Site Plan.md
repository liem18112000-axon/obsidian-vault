---
ai_hash: 4e6340b83196de68
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-08
domain: affiliate-marketing
entities:
- Free Music × Tech (AI) Tools Site
- AI-powered tools for musicians
- SE
- Musician who codes
- Affiliate offers
- Monetisation layer
- Trust
- Traffic
- Free tool
- Paid tool
- Recurring income
- CPS affiliate income
- AI angle
- AI music SaaS
- Flywheel
- Linkable assets
- Domain authority
- Money pages
- Tool portfolio
- AI music tool directory
- Vocal remover / stem splitter
- Lalal.ai
- Moises
- LANDR
- Suno
- Plugin Boutique
- Sweetwater
- Musora
- Demucs
- Essentia
- LLM API
- Client-side tool
- Flagship AI tool
- Heavy AI compute
- Site architecture
- Monetisation map
- Cookie Duration
- Revenue Share
- Earnings per Click
- Dogfood cross-sell
- Pillar A income
- Dev-SaaS affiliates
- Vercel
- Cloudflare
- Netlify
- Astro
- Next.js
- Web Audio API
- WASM
- Markdown / MDX
- Affiliate links
- Traffic & SEO strategy
- Tool keywords
- Editorial backlinks
- Comparison content
- Roadmap
- Metrics to watch
status: draft
tags:
- affiliate
- strategy
- music-tech
- ai
- tools
- project
type: project-plan
---

# Project Plan — Free Music × Tech (AI) Tools Site

> [!summary]
> A free site of **AI-powered tools for musicians**, built by you (the SE), where the tools are the traffic magnet and the trust-builder, and **affiliate offers are the monetisation layer**. This is the concrete build-out of the "wedge" + "build tools as moat" ideas from [[2 Areas/Affiliate/Strategy|the strategy note]]. Core engine: *free tool solves a problem instantly → earns trust + backlinks → funnels the user to the paid tool that does it better → recurring/CPS affiliate income.*

---

## 1. Concept & positioning

**One line:** *"Free, no-signup AI tools for musicians — built by a musician who codes."*

Why this positioning wins:
- **Free + no-signup** = lowest-friction entry; tools get used, shared, and linked.
- **"Built by a musician who codes"** = the rare SE×musician authority from [[2 Areas/Affiliate/Strategy|Strategy §3]]. You can both *build* the tool and *judge* the paid alternatives credibly.
- **AI angle** = rides the highest-search-growth topic in music right now, and AI music SaaS have generous, long-cookie affiliate programs (see §6).

> [!important] The tools are not the product — *trust + traffic* are. The affiliate offers are the product.
> A free browser tool that nails one job earns the right to say "for studio-quality results, here's the paid tool I'd use" — and that recommendation converts because you just demonstrated competence for free.

---

## 2. Why a tool-led site beats a blog (the flywheel)

![[flywheel.canvas]]

> [!note]- Text fallback (if the canvas doesn't render)
> ```
>         build free tool (weekend)
>                 │
>                 ▼
>    ranks for "free X tool" + earns backlinks  ──►  domain authority rises
>                 │                                          │
>                 ▼                                          ▼
>    user solves problem, trusts you            your REVIEW/COMPARISON pages
>                 │                                  rank too (authority halo)
>                 ▼                                          │
>    contextual "do it better" affiliate CTA  ◄─────────────┘
>                 │
>                 ▼
>      recurring / CPS commission  +  email capture (return audience)
> ```

Tools are **linkable assets** — other sites link to a useful free tool, almost never to a plain review. Those links lift the *whole* domain, so your money pages (reviews, comparisons) rank on the tools' coat-tails. This is the leverage a non-coder affiliate cannot copy.

---

## 3. The tool portfolio — your traffic magnets

Each tool is chosen for **(search demand) × (buildable on weekends) × (clean funnel to a paid offer)**. Build client-side where possible (Web Audio API / WASM) so they cost ~$0 to run and scale infinitely.

| Tool | Build effort | AI? | Funnels to (affiliate) |
|------|-------------|-----|------------------------|
| **AI music tool directory** (filterable list of every AI music tool) | Low (data + UI) | curates AI | *Every listing* — Lalal.ai, LANDR, Moises, Suno… [[3 Resources/Work-Side/Affiliate/Term/Cost per Sale|CPS]]/[[3 Resources/Work-Side/Affiliate/Term/Revenue Share|RevShare]] |
| **Vocal remover / stem splitter** (wrap open model e.g. Demucs, or cap usage) | Med–High (compute) | ✅ | Lalal.ai (30%/180-day), Moises, iZotope RX |
| **Chord progression generator** | Low (client-side) | optional AI | DAWs, Plugin Boutique, songwriting courses |
| **Key & BPM detector** (upload → analyse) | Med (WASM/Essentia) | — | DJ software, DAWs, sample libraries |
| **Scale / fretboard / keyboard explorer** | Low (client-side) | — | Lesson subscriptions (Musora), instruments |
| **Loudness / LUFS meter** | Med (Web Audio) | — | AI mastering (LANDR, eMastered) |
| **AI lyric / songwriting assistant** | Low (LLM API) | ✅ | Songwriting tools, courses |
| **Metronome / tuner / tap-BPM** | Very low | — | Apps, instruments (low value, high traffic, link-bait) |
| **Gear comparison engine** (interactive audio-interface / MIDI-controller DB) | Med (data + UI) | — | Sweetwater / Sam Ash / Thomann [[3 Resources/Work-Side/Affiliate/Term/Cost per Sale|CPS]], high [[3 Resources/Work-Side/Affiliate/Term/Average Order Value|AOV]] |

> [!tip] Start with the **directory + 1 client-side tool + 1 AI tool**.
> The directory monetises from day one (every row is an affiliate link) and is pure data work — perfect for tired weeknights. Pair it with one zero-cost client-side tool (chord generator) and one flagship AI tool (vocal remover) as the linkable centrepiece.

---

## 4. The "AI" angle — it means two things, use both

1. **Build AI-powered tools** — wrap open-source models (Demucs for stems, Essentia for analysis) or call an LLM API (lyrics, chord suggestions). These rank for high-growth "AI [thing] for musicians" queries.
2. **Monetise AI music SaaS** — the paid tools you funnel to *are themselves AI products* with strong affiliate programs (§6). You're an AI affiliate *and* an AI builder — the content writes itself ("I built a free vocal remover; here's when the paid AI ones are worth it").

> [!warning] Keep heavy AI compute off your own servers where you can.
> Self-hosting stem-separation GPUs gets expensive fast. Options, in order of preference: **client-side/WASM** (free) → **strict free-tier caps + queue** → **link out to the affiliate tool for the heavy job** (you earn the commission *and* dodge the compute bill). Don't subsidise free users' GPU time out of pocket.

---

## 5. Site architecture

```
musictechtools.xyz
├── /tools/            ← the magnets (each its own landing page + the live tool)
│   ├── vocal-remover
│   ├── chord-generator
│   ├── bpm-key-finder
│   └── ...
├── /ai-tools/         ← the AI music tool DIRECTORY (filterable; affiliate per listing)
├── /reviews/          ← hands-on reviews (the money pages — your content briefs)
├── /vs/               ← comparisons ("Suno vs Udio", "Lalal.ai vs Moises")
├── /guides/           ← tutorials that link to tools + offers
├── /how-i-built-this/ ← dogfood content → Pillar A dev-SaaS affiliates (see §7)
└── newsletter         ← email capture on every tool (return audience = recurring)
```

Each `/tools/X` page: the live tool up top, a short "how it works", then a contextual **"want pro results? → [affiliate]"** block, and internal links to the matching `/reviews/` and `/vs/` pages.

---

## 6. Monetisation map (tool → offer → model)

> [!warning] Verified June 2026 from program/roundup pages; **confirm on official affiliate pages before building** — AI-startup programs change fast.

| Your tool | Paid offer to funnel to | Reported terms | Model |
|-----------|------------------------|----------------|-------|
| Vocal remover / stem splitter | **Lalal.ai** | **30% commission, 180-day cookie** ✅ | [[3 Resources/Work-Side/Affiliate/Term/Cost per Sale|CPS]] |
| Vocal remover / practice tools | **Moises** | verify (has affiliate) | [[3 Resources/Work-Side/Affiliate/Term/Cost per Sale|CPS]]/sub |
| LUFS meter / mastering content | **LANDR** | mastering + distribution; verify rate | [[3 Resources/Work-Side/Affiliate/Term/Revenue Share|RevShare]]/sub |
| AI directory listings | **Suno / Udio / Soundraw / Aiva** | verify referral/affiliate | varies |
| Plugin/DAW tools & reviews | **Plugin Boutique** | professional affiliate program | [[3 Resources/Work-Side/Affiliate/Term/Cost per Sale|CPS]] |
| Gear comparison engine | **Sweetwater / Sam Ash / Thomann** | ~3–10%, cookies vary | [[3 Resources/Work-Side/Affiliate/Term/Cost per Sale|CPS]], high [[3 Resources/Work-Side/Affiliate/Term/Average Order Value|AOV]] |
| Lesson/scale tools | **Musora (Pianote/Drumeo)** | subscription | [[3 Resources/Work-Side/Affiliate/Term/Revenue Share|RevShare]]/sub |

> [!tip] Lalal.ai's **180-day cookie** is exceptional — six months for the referred sale to count (see [[Cookie Duration]]). Make the **vocal remover your flagship tool** and Lalal.ai its primary funnel.

Prioritise per [[2 Areas/Affiliate/Strategy|Strategy §4]]: **[[Revenue Share]] (recurring)** first for income that compounds on your scarce time, **[[Cost per Sale]]** for digital + high-[[Average Order Value|AOV]] gear. Compare everything on [[Earnings per Click|EPC]].

---

## 7. The dogfood cross-sell (free Pillar-A income)

You're building this on a real stack — so **document the build** in `/how-i-built-this/` and affiliate the exact dev tools you use:

- Host on **Vercel / Cloudflare / Netlify** → their referral/affiliate.
- Use a managed DB / auth / monitoring SaaS → recurring [[Revenue Share]] (the best programs from [[2 Areas/Affiliate/Strategy|Strategy §5]]).
- "How I built a free AI vocal remover for $0/mo" is exactly the post that ranks among developers *and* funnels to high-LTV dev SaaS.

This quietly bolts **Pillar A (dev SaaS, recurring)** onto a music site — the SE×musician wedge paying off in both directions.

---

## 8. Tech stack (weekend-SE-friendly)

| Layer | Pick | Why |
|-------|------|-----|
| Framework | **Astro or Next.js** | Static-first = fast, cheap, SEO-friendly |
| Tools runtime | **Web Audio API / Tone.js / WASM (Essentia, ONNX)** | Client-side = $0 compute, infinite scale |
| Heavy AI | **Serverless function w/ strict caps**, or link out | Avoid GPU bills (see §4) |
| Content | **Markdown / MDX** | Fits your Obsidian habit; reviews live as files |
| Affiliate links | **own `/go/{slug}` redirect** | Cloaking + click tracking → real [[3 Resources/Work-Side/Affiliate/Term/Earnings per Click|EPC]] |
| Analytics | Plausible / GA4 | Per-tool + per-offer conversion |
| Email | ConvertKit / Buttondown | Return audience = recurring asset |
| Host | Vercel / Cloudflare Pages | Free tier; *and* dogfood the affiliate (§7) |

---

## 9. Traffic & SEO strategy

- **Tool keywords** are the entry: "free vocal remover online", "bpm finder", "chord progression generator" — high intent, and your *live tool* is the best possible result.
- **Linkable assets**: free tools attract editorial backlinks that reviews never would → domain authority → money pages rank.
- **Comparison content** (`/vs/`) catches decision-stage buyers ("Lalal.ai vs Moises") — closest to conversion under last-click [[Attribution Model]].
- **The directory** ranks for "best AI music tools" and is endlessly updatable from weeknight slots.
- **Demo videos** (you play!) embedded in reviews → YouTube as a second search engine + trust.

---

## 10. Roadmap (mapped to your schedule)

> [!example]- Month 1 — Skeleton + first magnet
> - **Weeknights:** buy domain, scaffold the site, build the **AI tool directory** data (pure data entry — ideal tired-slot work), apply to Lalal.ai + Plugin Boutique + 1 dev-host program.
> - **Weekends:** ship the site + directory live; build **one client-side tool** (chord generator); set up `/go/` redirects + analytics.

> [!example]- Month 2 — The flagship AI tool
> - **Weeknights:** keyword research for `/vs/` + `/reviews/`; outline 3 money pages.
> - **Weekends:** build the **vocal remover** (flagship, client-side or capped); write its `/reviews/Lalal.ai` + `/vs/Lalal-vs-Moises` pages; record a demo video.

> [!example]- Month 3 — Content + dogfood
> - **Weeknights:** publish/maintain reviews; reply to comments; track which offers convert.
> - **Weekends:** build **tool #3** (BPM/key finder); write **"how I built this for $0"** → dev-SaaS affiliate; launch newsletter.

> [!example]- Months 4–6 — Compound
> - **Weeknights:** expand the directory, refresh reviews, prune losers by [[3 Resources/Work-Side/Affiliate/Term/Earnings per Click|EPC]].
> - **Weekends:** add the **gear comparison engine** (high [[3 Resources/Work-Side/Affiliate/Term/Average Order Value|AOV]]) and a 4th tool; double down on the highest-EPC funnel; consider a second demo-video series.

---

## 11. Metrics to watch

- **Per-tool → per-offer conversion** (which magnet actually funnels) — instrument via `/go/` clicks.
- **[[Earnings per Click]]** per offer — kill low-EPC funnels, scale high ones.
- **Confirmed vs pending** earnings — discount for [[Reversal|reversals]] (gear returns, SaaS churn).
- **Backlinks per tool** — proves the linkable-asset thesis; guides what to build next.
- **Email list growth** — your only *owned*, recurring audience.

---

## 12. Risks specific to this build

> [!warning] AI-music-tools failure modes
> - **Compute cost creep** — a viral free AI tool can rack up GPU bills overnight. Cap hard; prefer client-side; link out for the heavy job (§4).
> - **Maintenance burden** — every tool is code you must keep alive. Favour durable, low-dep client-side tools; don't ship 10 fragile ones.
> - **AI space churn** — tools/models/programs appear and vanish monthly; the **directory must be actively curated** or it rots (and dead affiliate links earn nothing).
> - **Google + AI content** — thin AI-generated pages get penalised. Your edge is *genuinely useful tools* + *hands-on, plays-the-instrument reviews*. Keep it real.
> - **Copyright / ethics** — AI generation and stem-separation touch copyright gray zones; add clear disclaimers and a usage policy.
> - **Slow ramp** — still a 6–12 month SEO game ([[2 Areas/Affiliate/Strategy|Strategy §6]]); the tools shorten it (backlinks) but don't eliminate it.

---

## 13. Bottom line

> [!summary] The play in one paragraph
> Build a small set of **free, mostly client-side AI tools for musicians** — anchored by an **AI-tool directory** (monetises immediately) and a **vocal remover** flagship (funnels to **Lalal.ai**, 30% / 180-day cookie). The tools earn backlinks and trust that lift your **reviews and comparisons**, which carry the real affiliate money — **recurring [[Revenue Share]]** for AI/SaaS subscriptions and **[[Cost per Sale]]** for plugins and high-[[Average Order Value|AOV]] gear. **Dogfood your dev stack** and document the build to bolt on high-LTV dev-SaaS income. Build flagships on weekends, curate the directory and reviews on weeknights, keep compute client-side, and compare every funnel on [[Earnings per Click|EPC]].

---

## Sources

Researched June 2026 (verify on official affiliate pages — terms change):

- [LALAL.AI Affiliate Program](https://www.lalal.ai/affiliate-program/) — 30% commission, 180-day cookie
- [LANDR AI Music Tools](https://www.landr.com/ai-music-tools)
- [21 Best AI Affiliate Programs 2026 (Lasso)](https://getlasso.co/niche/ai/)
- [Plugin Boutique](https://www.pluginboutique.com/)
- Music/gear program terms: see [[2 Areas/Affiliate/Strategy#Sources|Strategy note sources]]

## Related

- [[2 Areas/Affiliate/Strategy]] — the parent strategy this plan implements.
- [[3 Resources/Work-Side/Affiliate/Term|Affiliate terms glossary]] — definitions for every model and metric used here.

%% ai-graph-start %%

**Related notes:**
- [[Strategy]]

**Relations:**
- Free Music × Tech (AI) Tools Site — *provides* — AI-powered tools for musicians
- Free Music × Tech (AI) Tools Site — *built by* — SE
- SE — *is a* — Musician who codes
- Affiliate offers — *are the* — Monetisation layer
- AI-powered tools for musicians — *generate* — Traffic
- AI-powered tools for musicians — *build* — Trust
- Free tool — *solves a problem* — instantly
- Free tool — *earns* — Trust
- Free tool — *earns* — Editorial backlinks
- Free tool — *funnels user to* — Paid tool
- Paid tool — *generates* — Recurring income
- Paid tool — *generates* — CPS affiliate income
- AI angle — *rides* — highest-search-growth topic in music
- AI music SaaS — *have* — generous affiliate programs
- Tools — *are* — Linkable assets
- Linkable assets — *attract* — Editorial backlinks
- Editorial backlinks — *increase* — Domain authority
- Domain authority — *helps rank* — Money pages
- Tool portfolio — *includes* — AI music tool directory
- Tool portfolio — *includes* — Vocal remover / stem splitter
- AI music tool directory — *funnels to* — Lalal.ai
- AI music tool directory — *funnels to* — Moises
- AI music tool directory — *funnels to* — LANDR
- AI music tool directory — *funnels to* — Suno
- AI music tool directory — *funnels to* — Plugin Boutique
- AI music tool directory — *funnels to* — Sweetwater
- AI music tool directory — *funnels to* — Musora
- Vocal remover / stem splitter — *uses* — Demucs
- Vocal remover / stem splitter — *funnels to* — Lalal.ai
- Vocal remover / stem splitter — *funnels to* — Moises
- Lalal.ai — *offers* — 30% commission
- Lalal.ai — *offers* — 180-day cookie
- Lalal.ai — *is a type of* — CPS affiliate income
- Vocal remover / stem splitter — *is the* — Flagship AI tool
- Flagship AI tool — *funnels to* — Lalal.ai
- AI-powered tools — *use* — LLM API
- AI-powered tools — *use* — open-source models
- Client-side tool — *avoids* — Heavy AI compute
- Site architecture — *includes path* — /tools/
- Site architecture — *includes path* — /ai-tools/
- Site architecture — *includes path* — /reviews/
- Site architecture — *includes path* — /vs/
- Site architecture — *includes path* — /how-i-built-this/
- Monetisation map — *details* — Paid offer
- Monetisation map — *details* — affiliate model
- Revenue Share — *is a type of* — affiliate model
- CPS affiliate income — *is a type of* — affiliate model
- Dogfood cross-sell — *generates* — Pillar A income
- Dogfood cross-sell — *targets* — Dev-SaaS affiliates
- Vercel — *is a* — Host
- Vercel — *is a* — Dev-SaaS affiliates
- Cloudflare — *is a* — Host
- Cloudflare — *is a* — Dev-SaaS affiliates
- Netlify — *is a* — Host
- Netlify — *is a* — Dev-SaaS affiliates
- Astro — *is a* — Framework
- Next.js — *is a* — Framework
- Web Audio API — *is a* — Tools runtime
- WASM — *is a* — Tools runtime
- Markdown / MDX — *is for* — Content
- Affiliate links — *use* — /go/{slug} redirect
- Traffic & SEO strategy — *uses* — Tool keywords
- Traffic & SEO strategy — *uses* — Linkable assets
- Traffic & SEO strategy — *uses* — Comparison content
- Roadmap — *outlines* — project plan
- Metrics to watch — *include* — Earnings per Click
- Metrics to watch — *include* — conversion

%% ai-graph-end %%