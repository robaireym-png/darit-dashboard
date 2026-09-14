# Darit Capital Dashboard — Redesign Plan

Decided 2026-09-13. Design only; `index.html` unmodified.

---

## 1. Current inventory (what is on the page today)

Single file, `.wrap` max-width 940px, 8 sections + footer. Approximate vertical cost at 390px width (phone):

| # | Section | Contents | ~px on phone |
|---|---|---|---|
| 0 | Header | h1, sub line w/ regime chip, 7-link nav, blue banner | 260 |
| 1 | `#portfolio` | h2, 6-line explainer note, chartwrap (mode toggle, **2× 200px SVG panels** + legends, cadence-ladder note, range line), then **4 system cards** (SPEAR, JAVELIN, DART, grokmesa) stacked 1-up | 1,500 |
| 2 | `#ledger` | h2, note, **15 decision cards** (each: tag, date, mono id, what, rule checklist, why-not, risk) | 2,900 |
| 3 | `#options` | h2, note, 3 metric cards, SENTINEL card, grokmesa watch card (sparkline + 4 rung chips + 2 notes), "Open rungs" **4 payoff cards** | 1,700 |
| 4 | `#holdings` | 5 SPEAR mini-charts, tap-to-expand | 700 |
| 5 | `#weather` | Grokbot card (sparkline + 6 rows + note), open-positions table (2 rows) | 620 |
| 6 | `#systems` | Tracker table **10 rows**, options table **5 rows**, risk-overlay note | 780 |
| 7 | `#research` | `<details>`, collapsed — nightly battery, rotating experiment, account fleet table (5 rows), 3 gate cards | 45 collapsed |
| 8 | Footer | credits | 60 |

**Total ≈ 8,500px on a phone. The first screen shows the title, the banner, and half of one paragraph.**

### The two real defects

1. **11 of 15 named systems have no representation above the tracker table.** `saty_paper`, `MAINSAIL`, `LOOM`, `polyagent v2`, `btc_scalper` are all tagged `live paper` but have no card, no number, no graph anywhere on the page. `Grokbot` is live and funded but its card is buried in section 5. Only 4 systems (SPEAR / JAVELIN / DART / grokmesa) exist in the portfolio view. That is why "is anything actually running" was an open question — the page cannot answer it.
2. **Status is text-only and colour-inconsistent.** `.p-live` / `.p-paper` / `.p-design` / `.p-gate` pills are legible only by reading them, and card heading colours are decorative (grokmesa, MAINSAIL and LOOM all render green regardless of state). There is no heartbeat — nothing distinguishes "live and traded today" from "live and silent for three weeks."

---

## 2. The decision

**Fleet Status board at the top, family-grouped detail below, no tab strip.**

One responsive tile grid — one tile per named system, every system, no exceptions — sits directly under a portfolio equity curve. Below the fold, detail lives in **four fixed family sections**, reached by a sticky 4-chip jump bar. Systems grow; families do not.

**Why this and not the alternatives:** the count of systems is the design constraint. Robert's lifecycle rule (S1: a rule change spawns a new named system, winners are never edited in place) guarantees the list only grows — 15 today, 25 next quarter. Any layout that allocates *chrome* per system (tabs, nav links, accordion headers in the top nav) degrades linearly with that growth. A tile grid allocates *content area* per system and reflows: 2-up on a phone, 4-up on a laptop, identical code at 10 systems and at 30. The four families are the only thing about the fleet that is stable.

---

## 3. Tabs: **No.**

Four reasons, in order of weight:

1. **The count grows.** A tab strip is a horizontal list. At 15 systems a phone shows three tabs and a scroll hint; at 25 it is unusable. The tile grid is the same component at any count.
2. **Tabs hide the thing Robert asked for.** The stated need is "how is everything doing" at a glance. Tabs are mutually exclusive by definition — they answer "how is *one* thing doing." A grid where every system is simultaneously visible is the literal request.
3. **Tabs break the page.** Ctrl-F stops finding content in inactive panels; the existing `#ledger` / `#options` anchors and any link Robert has saved stop resolving; and a tab panel cannot be deep-linked without JS routing this file does not have.
4. **Nothing is gained.** The problem tabs solve is vertical length. That is solved here by collapsing per-system detail into `<details>` accordions inside each family section — same length reduction, no state, no lost search, no growth ceiling.

**What replaces them:** a **sticky 4-chip jump bar** — `Directional · Options · Events · Research` — pinned below the header once the status board scrolls past. Four chips fit one phone line with room to spare, and the count never changes because families are fixed. It scrolls the page rather than swapping panels, so search, anchors and back-button all keep working.

---

## 4. Section order, top to bottom

### 0 — Header strip (compact)
Title, `paper only`, regime chip, `as of <date>`. **The existing blue banner moves out of the fold** — its text becomes a one-line `<details>` under the status board. It costs 90px and says nothing that changes daily.

### 1 — FLEET STATUS *(the fold — this is the whole brief)*

**1a. Counter ribbon.** One line, first thing after the title:

> `6 LIVE · 1 OVERRIDE · 2 RESEARCH · 5 DESIGNED · 0 ARCHIVED · last decision 2026-09-11`

This single line answers "is anything actually running" before any graph renders.

**1b. Portfolio equity curve** *(the outstanding ask).* One chart, full width, 180px tall.
- Plots the **equal-weight blended % return of all live paper books**: each book's own % return from its own start, averaged with equal weight, rebased to 0%.
- **Not** dollar-aggregated. grokmesa's $400k notional would swamp four $25k books and one $500 book; the line would just be grokmesa. Equal-weight % is the only honest single line across books of different size.
- Individual system lines drawn behind at 25% opacity, unlabelled, for texture.
- Keep the existing `% return / rebased-to-100` toggle; move it here.
- Caption states the method in one sentence, plus the book count it covers.

**1c. Status board.** `grid-template-columns: repeat(auto-fill, minmax(150px, 1fr))`. One tile per named system, sorted LIVE → OVERRIDE → RESEARCH → DESIGNED → ARCHIVED, then by return within group. Each tile:

```
┌──────────────────────┐
│ ● SPEAR       equity │   status glyph + name + venue
│ +9.8%                │   return since start, colour-coded
│ ▁▂▃▅▄▆▇  (sparkline) │   28px inline sparkline
│ 7d +1.8% · 2d ago    │   week move · decision heartbeat
└──────────────────────┘
```
Designed / research systems show no number and no sparkline — a dashed empty slot and the reason (`no book yet`). **The absence is the information.** Whole tile is an anchor into that system's detail block.

Above-the-fold budget, 390×844 phone (~700px usable): header 70 + ribbon 34 + equity chart 200 + jump bar 40 = 344px, leaving ~350px = the first **six tiles** (2-up × 3 rows) visible without scrolling. The six are the live ones, by sort order. Full board lands within one thumb-flick. On a 1440×900 laptop the equity chart and the full 15-tile board sit side by side, all above the fold.

### 2 — Sticky jump bar
`Directional · Options · Events · Research`. Pins on scroll, 40px, highlights the section in view.

### 3 — Directional Equities
Cadence-ladder panel (SPEAR/JAVELIN/DART on one axis, the existing `dir` panel + the cadence explainer + the DART override note — this comparison is the point of the family and stays expanded). Then one `<details>` accordion per system: SPEAR (with its 5 holdings mini-charts), JAVELIN, DART, saty_paper, MAINSAIL, LOOM. **The last three get accordions with their own numbers — currently they have none.** Accordions open on desktop, closed on mobile.

### 4 — Options Income
grokmesa panel (`opt` chart, premium/deployment/book cards, SENTINEL, watch card, open rungs) — as today, unchanged content. Then one collapsed stub each for WHEELHOUSE, BULWARK, RANGEKEEPER, CANARY reading `designed — not running, no book`.

### 5 — Event Markets
Grokbot (existing card + open-positions table), polyagent v2, btc_scalper as accordions. **The latter two currently appear only as tracker-table rows.**

### 6 — Decision Ledger
Stays cross-system and chronological — it is the one place the whole fleet's behaviour is comparable, and splitting it per family would destroy that. Additions: a family filter chip row, **latest 8 cards shown, rest behind `show all`**, and on mobile each card collapses to its headline (`tag · date · what`) with rule-chain / why-not / risk behind a tap. Every status tile's "N days ago" heartbeat deep-links to that system's newest ledger card.

### 7 — Research & Overlays
Existing `#research` `<details>`, collapsed, unchanged. Absorbs the SENTINEL / CANARY overlay note and the systems tracker table (which becomes the full canonical registry, now redundant as a *status* surface because the board above owns that job).

### 8 — Footer
Unchanged.

---

## 5. Status encoding

Colour alone is insufficient — it fails for colour-blind readers, fails in sunlight, and is already spent on P&L (green/red). **Every status is encoded in glyph shape *and* colour *and* sort position, and paired with a heartbeat.**

| Status | Glyph | Colour | Meaning | Tile shows |
|---|---|---|---|---|
| **LIVE** | ● filled circle | green `#3fb950` | Running now, book moving | return, sparkline, heartbeat |
| **OVERRIDE** | ◈ filled diamond | purple `#a371f7` | Running, but failed its pre-registered gate | same + `failed gate` label |
| **RESEARCH** | ▣ filled square | blue `#58a6ff` | Runs nightly, no book | last run date, no return |
| **DESIGNED** | ○ hollow ring | grey `#8b949e` | Specified, not running | dashed empty slot, `no book yet` |
| **ARCHIVED** | ◌ dashed ring | dim grey, tile at 55% opacity | Retired with a regime note | frozen final return, greyed |

**Heartbeat (this is the part that answers Robert's question).** Every LIVE and OVERRIDE tile carries `last decision: Nd ago`, read from the newest ledger entry for that system.
- 0–2 days → normal weight
- 3–7 days → amber text
- 8+ days → **amber warning triangle ⚠ on the tile** and the system drops below fresh ones in the sort

A green dot means "declared live." A green dot with `today` means **actually running**. A green dot with `⚠ 14d` is exactly the state the current page renders as identical to a healthy system, and is the failure being fixed.

Legend row sits under the counter ribbon, five glyphs, one line.

---

## 6. Mobile behaviour

- Single column throughout; `.wrap` padding unchanged.
- Status board: 2 tiles per row (`minmax(150px,1fr)` at 390px yields 2). At 20+ systems this is 10 rows ≈ 640px — one flick. No change to markup or CSS needed as the count grows.
- Equity chart 200px tall, `preserveAspectRatio="none"` as existing; sparklines 28px.
- Jump bar sticky, 4 chips, one line, `position:sticky; top:0`.
- All family accordions **closed by default under 768px**, open above it (`<details open>` toggled by a 3-line media-query script).
- Ledger cards collapsed to headline on mobile.
- Long explainer paragraphs (the 6-line portfolio note, the cadence note) become `<details><summary>why this chart looks like this</summary>` — text preserved verbatim, 300px of fold reclaimed.
- Touch targets ≥44px. Tiles are the anchors, so the whole tile is the target.

---

## 7. Implementation checklist

1. Add a `window.FLEET` JS object next to the existing `window.IDX`: one entry per named system — `{name, family: dir|opt|evt|res, status: live|override|research|designed|archived, venue, pct, week, lastDecision, spark: [...]}`. **This is the only new data contract**; everything below reads from it.
2. Extend the generator that emits `window.IDX` to also emit `window.FLEET`, including the systems that currently have no equity series (`saty_paper`, `MAINSAIL`, `LOOM`, `polyagent v2`, `btc_scalper`, `Grokbot`) and the designed ones (null return, null spark).
3. Add CSS: `.board` grid, `.tile`, `.glyph` (5 variants), `.heartbeat` (3 freshness states), `.jumpbar` sticky, `.ribbon`.
4. Build the counter ribbon + glyph legend — derive counts from `FLEET`, do not hardcode.
5. Build the portfolio equity curve: equal-weight mean of live books' `pct` series, rebased to 0; faint individual lines behind; reuse the existing `drawPanel` scaling math and the `%/rebased` toggle. Insert as new `#fleet` section before `#portfolio`.
6. Build the status board renderer: sort by status rank then return, emit one tile per `FLEET` entry, 28px sparkline via the existing `.mchart` path-builder, `href="#sys-<name>"`.
7. Add the sticky jump bar with 4 anchors + scroll-spy highlight.
8. Restructure sections into `#fam-dir`, `#fam-opt`, `#fam-evt`, `#fam-res`. Move existing markup in wholesale — **no content is deleted in this pass.**
9. Wrap each system's detail in `<details id="sys-<name>">` with a summary line matching its tile.
10. Add accordion stubs for the 9 systems that currently have no detail block.
11. Ledger: add family filter chips, truncate to 8 with `show all`, collapse card bodies under 768px.
12. Move the blue banner and the two long explainer paragraphs into `<details>`.
13. Move the systems tracker table into `#fam-res` as the canonical registry.
14. Media-query script: `<details>` open above 768px, closed below.
15. Verify at 390×844, 768×1024, 1440×900: fold contents match §4 §1b/§1c budget; every `FLEET` entry has exactly one tile and one detail block; heartbeat states render at 1d / 5d / 20d; every glyph is distinguishable in greyscale.
16. Add 5 fake systems to `FLEET` locally and confirm the board and jump bar are unchanged at 20. Remove before commit.

---

*No options above. If any line here turns out wrong in build, change it there and note the change — do not reopen the layout decision.*
