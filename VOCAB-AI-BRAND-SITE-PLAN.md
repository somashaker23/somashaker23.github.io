# VOCAB.AI Brand Site — Build Plan for Claude Code

## 0. Objective
Turn the four existing brand documents into one static, public **Visual Guidelines
site**, hosted on GitHub Pages, that employees/vendors/partners can reference —
replacing the interactive "design canvas" mockups with a real, permanent site.

Source material already reviewed (all in `./raw-data`):
- `VOCAB_AI_Brand_Guidelines_dc.html` — full guidelines doc (cover, positioning,
  logo system, color, typography, pattern, applications, email signature). This
  is the primary content source — most sections can be ported near-verbatim.
- `VOCAB_AI_Brand_Deck_dc.html` — same content as a slide deck (cover → thank you).
  Useful for phrasing/speaker-notes, not needed as a separate build target.
- `VOCAB_AI_Email_Signature_dc.html` — standalone signature mockup, matches the
  signature section already in the guidelines doc.
- `Canvas_dc.html` — 5 **alternative logo mark explorations** (decision-tree,
  knowledge-graph facet, infinite loop, system-architecture stack, grid-lattice),
  explicitly framed as "moving away from the connected-node network" mark that's
  used throughout the other three documents.

## 1. Logo decision — RESOLVED
**Decision: keep the current connected-node network mark for v1.** It's already
fully specified (primary/stacked/icon-only lockups, clearspace, favicon,
monochrome/inverted variants, don'ts) — the 5 Canvas alternatives are parked as
future exploration, not blocking this build.

Still build it as a single reusable SVG symbol (`<symbol id="logo-mark">`)
referenced everywhere via `<use>`, so swapping marks later (if the Canvas
exploration lands on a replacement) is a one-file edit instead of a site-wide
find/replace.

## 2. Design tokens (extracted from source, ready to use)

**Color** (all defined as OKLCH — keep them that way, don't convert to hex):
| Token | Value | Use |
|---|---|---|
| `--ink` | `oklch(21% 0.03 240)` | primary text, dark logo |
| `--paper` | `oklch(98% 0.006 95)` | light background |
| `--teal-900` | `oklch(23% 0.05 200)` | dark surfaces, icon bg |
| `--teal-700` | `oklch(35% 0.07 200)` | |
| `--teal-500` | `oklch(51% 0.095 200)` | primary teal (logo strokes, links) |
| `--teal-300` | `oklch(72% 0.07 200)` | |
| `--teal-100` | `oklch(93% 0.025 200)` | |
| `--orange-500` | `oklch(66% 0.175 48)` | single accent — CTAs/decision points only, never background |
| `--surface-dark` | `oklch(15% 0.025 240)` | cover/footer/dark-mode sections |
| `--line` | `oklch(88% 0.008 220)` | hairline borders |
| `--muted` | `oklch(55% 0.015 220)` | captions/labels |

**Type:**
- Display/headings: **Sora** 600/700/800 (Google Fonts)
- Body/UI/data: **Inter** 400/500/600 (Google Fonts)
- Scale: Display 56/64 · H1 40 · H2 28 · H3 20 · Body 16 · Label 13 (uppercase, 0.04em)

**Voice:** precise, confident, no hype, no anthropomorphizing.

## 3. Site map (single-page, anchor-nav — matches the existing guidelines doc structure)

```
index.html
├── Nav (sticky) — Logo · Color · Type · Pattern · Applications · Signature
├── #cover        — dark hero, mark + wordmark, tagline
├── #positioning  — what we stand for / personality / voice
├── #logo         — primary/stacked/icon lockups, clearspace, mono/inverted, favicon, don'ts
├── #color        — swatch grid + light/dark surface examples
├── #type         — Sora/Inter specimens + type scale table
├── #pattern      — node-network texture, on-paper / on-dark examples
├── #applications — social banner, pitch deck cover, business card, website hero
├── #signature    — the standard employee signature spec + live example
└── Footer        — mark + version + contact
```

This mirrors `VOCAB_AI_Brand_Guidelines_dc.html` section-for-section — that file
is effectively the working draft; the job is mostly a faithful, cleaned-up port
to a real static site (fixed nav anchors, no template placeholders, real SVG
symbol, no editor-only scaffolding like `sc-if`/`sc-for`/`{{markSize}}`).

## 4. What changes vs. the source file (cleanup, not redesign)
- Strip all design-canvas-only scaffolding: `<x-dc>`, `<helmet>`, `support.js`,
  `data-dc-script`, `sc-if`/`sc-for` template tags, `{{prop}}` placeholders.
- Replace the templated pattern-generator script with a **static, pre-baked**
  SVG node pattern (pick one density/seed and hardcode the coordinates) —
  no need for the live prop-editor logic on a public site.
- Extract the repeated inline logo SVG into one `<symbol>` in a `<defs>` block,
  reused via `<use href="#logo-mark">` — currently it's pasted ~10 times inline.
- Remove Cloudflare email-obfuscation cruft (`__cf_email__`, `/cdn-cgi/...`) —
  not needed outside the original doc's hosting environment. Use plain mailto
  links, or a simple JS obfuscation if spam is a real concern.
- Keep all copy (positioning, voice, personality words) as-is — it's already final.

## 5. Repo structure
```
vocab-ai-brand/
├── index.html              # the whole site (single file is fine for this size)
├── assets/
│   ├── logo-network.svg    # exported standalone version of the current mark
│   ├── favicon.svg         # simplified 3-node version (already specced, 16–48px)
│   └── og-image.png        # social preview card (optional, 1200×630)
├── README.md               # what this repo is, how to update it
└── CNAME                   # only if a custom domain is wanted later
```

## 6. Build steps for Claude Code (in order)
1. Scaffold repo folder + `index.html` skeleton with the sticky nav and section
   anchors listed in §3.
2. Define CSS custom properties for every token in §2 at `:root`.
3. Build the `#logo-mark` SVG symbol once; wire up primary/stacked/icon-only
   lockups in `#logo` using `<use>`.
4. Port `#cover`, `#positioning`, `#color`, `#type` sections — these are static
   content, direct port from the guidelines doc with cleanup per §4.
5. Port `#pattern` — bake one static node/edge layout (reuse the coordinate
   logic already in the Canvas/guidelines script, just run it once and paste
   the resulting SVG rather than keeping it live).
6. Port `#applications` (social banner, pitch deck cover, business card,
   website hero mockups) and `#signature` (spec + example, matching the
   already-agreed field order: name → title → contact → tagline).
7. Add footer with version tag (`v1.0 · 2026`) and contact.
8. Test responsiveness at 375px / 768px / 1280px — source doc is desktop-fixed
   width (1240px max), needs basic mobile stacking at minimum for the nav and
   the 3–4 column grids (color swatches, don'ts, applications).
9. Add a favicon using the simplified 3-node mark already specced in the source.
10. Push to GitHub, enable Pages (root or `/docs`, whichever matches the repo setup).

## 7. Out of scope for v1 (call out, don't build yet)
- The live prop-editable pattern density / logo style toggle (`markStyle`,
  `patternDensity`) — that was for internal design review, not needed publicly.
- A separate employee signature *generator* tool (like the ESTECO one built
  earlier in this chat) — the VOCAB.AI signature section here is currently a
  static spec/example only. Flag as a fast follow-up once the mark is locked.
- Dark-mode toggle for the site itself (the Brand Deck has a "Dark Mode"
  slide, but that's about the *product* supporting dark mode, not the
  guidelines site needing one).

## 8. Before Claude Code starts, confirm
- [x] Logo: keep current network mark for v1 — confirmed.
- [ ] GitHub org/repo name and whether Pages should serve from a custom domain?
- [ ] use git@github.com:somashaker23/somashaker23.github.io.git to host in github pages
