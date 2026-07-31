---
type: project-plan
domain: affiliate-marketing
created: 2026-06-08
tags: [affiliate, strategy, music-tech, ai, tools, project]
status: draft
---

# Project Plan — Free Music × Tech (AI) Tools Site

> [!summary]
> A free site of **AI-powered tools for musicians**, built by you (the SE), where the tools are the traffic magnet and the trust-builder, and **affiliate offers are the monetisation layer**. This is the concrete build-out of the "wedge" + "build tools as moat" ideas from [[Affiliate/Research/Strategy|the strategy note]]. Core engine: *free tool solves a problem instantly → earns trust + backlinks → funnels the user to the paid tool that does it better → recurring/CPS affiliate income.*

---

## 1. Concept & positioning

**One line:** *"Free, no-signup AI tools for musicians — built by a musician who codes."*

Why this positioning wins:
- **Free + no-signup** = lowest-friction entry; tools get used, shared, and linked.
- **"Built by a musician who codes"** = the rare SE×musician authority from [[Affiliate/Research/Strategy|Strategy §3]]. You can both *build* the tool and *judge* the paid alternatives credibly.
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
| **AI music tool directory** (filterable list of every AI music tool) | Low (data + UI) | curates AI | *Every listing* — Lalal.ai, LANDR, Moises, Suno… [[Cost per Sale\|CPS]]/[[Revenue Share\|RevShare]] |
| **Vocal remover / stem splitter** (wrap open model e.g. Demucs, or cap usage) | Med–High (compute) | ✅ | Lalal.ai (30%/180-day), Moises, iZotope RX |
| **Chord progression generator** | Low (client-side) | optional AI | DAWs, Plugin Boutique, songwriting courses |
| **Key & BPM detector** (upload → analyse) | Med (WASM/Essentia) | — | DJ software, DAWs, sample libraries |
| **Scale / fretboard / keyboard explorer** | Low (client-side) | — | Lesson subscriptions (Musora), instruments |
| **Loudness / LUFS meter** | Med (Web Audio) | — | AI mastering (LANDR, eMastered) |
| **AI lyric / songwriting assistant** | Low (LLM API) | ✅ | Songwriting tools, courses |
| **Metronome / tuner / tap-BPM** | Very low | — | Apps, instruments (low value, high traffic, link-bait) |
| **Gear comparison engine** (interactive audio-interface / MIDI-controller DB) | Med (data + UI) | — | Sweetwater / Sam Ash / Thomann [[Cost per Sale\|CPS]], high [[Average Order Value\|AOV]] |

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
| Vocal remover / stem splitter | **Lalal.ai** | **30% commission, 180-day cookie** ✅ | [[Cost per Sale\|CPS]] |
| Vocal remover / practice tools | **Moises** | verify (has affiliate) | [[Cost per Sale\|CPS]]/sub |
| LUFS meter / mastering content | **LANDR** | mastering + distribution; verify rate | [[Revenue Share\|RevShare]]/sub |
| AI directory listings | **Suno / Udio / Soundraw / Aiva** | verify referral/affiliate | varies |
| Plugin/DAW tools & reviews | **Plugin Boutique** | professional affiliate program | [[Cost per Sale\|CPS]] |
| Gear comparison engine | **Sweetwater / Sam Ash / Thomann** | ~3–10%, cookies vary | [[Cost per Sale\|CPS]], high [[Average Order Value\|AOV]] |
| Lesson/scale tools | **Musora (Pianote/Drumeo)** | subscription | [[Revenue Share\|RevShare]]/sub |

> [!tip] Lalal.ai's **180-day cookie** is exceptional — six months for the referred sale to count (see [[Cookie Duration]]). Make the **vocal remover your flagship tool** and Lalal.ai its primary funnel.

Prioritise per [[Affiliate/Research/Strategy|Strategy §4]]: **[[Revenue Share]] (recurring)** first for income that compounds on your scarce time, **[[Cost per Sale]]** for digital + high-[[Average Order Value|AOV]] gear. Compare everything on [[Earnings per Click|EPC]].

---

## 7. The dogfood cross-sell (free Pillar-A income)

You're building this on a real stack — so **document the build** in `/how-i-built-this/` and affiliate the exact dev tools you use:

- Host on **Vercel / Cloudflare / Netlify** → their referral/affiliate.
- Use a managed DB / auth / monitoring SaaS → recurring [[Revenue Share]] (the best programs from [[Affiliate/Research/Strategy|Strategy §5]]).
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
| Affiliate links | **own `/go/{slug}` redirect** | Cloaking + click tracking → real [[Earnings per Click\|EPC]] |
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
> - **Weeknights:** expand the directory, refresh reviews, prune losers by [[Earnings per Click\|EPC]].
> - **Weekends:** add the **gear comparison engine** (high [[Average Order Value\|AOV]]) and a 4th tool; double down on the highest-EPC funnel; consider a second demo-video series.

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
> - **Slow ramp** — still a 6–12 month SEO game ([[Affiliate/Research/Strategy|Strategy §6]]); the tools shorten it (backlinks) but don't eliminate it.

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
- Music/gear program terms: see [[Affiliate/Research/Strategy#Sources|Strategy note sources]]

## Related

- [[Affiliate/Research/Strategy]] — the parent strategy this plan implements.
- [[Affiliate/Term|Affiliate terms glossary]] — definitions for every model and metric used here.
