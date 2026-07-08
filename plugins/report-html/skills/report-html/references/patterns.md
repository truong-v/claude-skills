# Patterns: building the document

Copy-paste recipes for every component. All assume you started from
`assets/template.html`, which defines the CSS and the `Doc` helpers and auto-wires the
sticky TOC, sortable tables, tabs, and footnotes on load. Keep what you need, delete the
rest, and swap in your content.

## Contents
1. [Structure — a spine per document type](#1-structure)
2. [Executive summary (the lede)](#2-executive-summary)
3. [Key-findings cards & badges](#3-key-findings-cards--badges)
4. [Data tables (sortable)](#4-data-tables)
5. [Charts (inline SVG)](#5-charts)
6. [Citations & footnotes](#6-citations--footnotes)
7. [Tabs](#7-tabs)
8. [Callouts, collapsibles & the TOC](#8-callouts-collapsibles--toc)
9. [Diagrams](#9-diagrams)
10. [Taste checklist](#10-taste-checklist)

---

## 1. Structure

Pick the type, follow its spine, adapt freely. Every type leads with the bottom line
and keeps methodology/caveats behind a `<details>`.

- **Findings / report** — Executive summary → Key-findings cards → The data (tables +
  charts) → Analysis → Recommendation → References.
- **Spec** — Summary / goal → Requirements → Design or approach (**tabs** for options
  A/B/C) → Trade-offs → Open questions → Decision.
- **Research brief** — The question → What we found (summary) → Evidence (with
  **citations**) → Limitations → Sources.
- **Analysis / memo** — Bottom line → The ask / decision → The reasoning (data) → What
  happens next.
- **Postmortem** — What happened (timeline) → Impact → Root cause → What went well /
  badly (**callouts**) → Action items (table with **badges** + owners).

Rebuild the `#toc` list so its links match your `<section id="...">` ids; the TOC
highlights the current section automatically.

---

## 2. Executive summary

Always first, before anything else. The bottom line in 2–4 sentences.

```html
<div class="lede">
  <span class="k">Executive summary</span>
  <p>What we looked at, what we found, and what we recommend — the point, up front.</p>
</div>
```

---

## 3. Key-findings cards & badges

The two-to-five results a skimmer actually reads. A metric, a one-line why, a badge.

```html
<div class="cards">
  <div class="card">
    <h3>Signup conversion <span class="badge high">High</span></h3>
    <div class="metric">2.1% <span class="delta down">&minus;0.6pt</span></div>
    <p>Down from 2.7% last quarter; checkout is the main drop-off.</p>
  </div>
  <!-- more .card blocks -->
</div>
```

Badge classes: `high` (red), `med` (amber), `low` (teal), `done` (green), `info`
(blue), `open` (grey). Use them for severity, priority, or status — on cards, in table
cells, or inline after a heading. `.delta.up` is green, `.delta.down` is red (flip the
class if "down" is the good direction).

---

## 4. Data tables

Add `class="sortable"` and every column becomes click-to-sort (numbers sort
numerically, text alphabetically; click again to reverse). Put `class="num"` on numeric
cells and their header to right-align with tabular figures.

```html
<table class="sortable">
  <thead><tr><th>Region</th><th class="num">Signups</th><th class="num">Conv. %</th><th>Status</th></tr></thead>
  <tbody>
    <tr><td>North America</td><td class="num">18,240</td><td class="num">2.4</td><td><span class="badge done">On track</span></td></tr>
    <tr><td>Asia-Pacific</td><td class="num">9,510</td><td class="num">1.6</td><td><span class="badge high">At risk</span></td></tr>
  </tbody>
</table>
```

---

## 5. Charts

Numbers land faster as a picture. Drop a mount `<div>` and call the helper. Charts are
inline SVG, theme-aware, with native hover tooltips — no library, no network.

```html
<div id="rev-chart" class="chart"></div>
<script>
  Doc.barChart('#rev-chart', {
    title: 'Signups by region',                 // aria-label
    caption: 'This quarter, by region.',        // shown under the chart
    data: [{label:'N. America', value:18240}, {label:'Europe', value:12880},
           {label:'APAC', value:9510}, {label:'LatAm', value:4220}]
  });
  Doc.lineChart('#trend', {
    caption: 'Conversion rate (%), last 6 months.',
    min: 0, max: 3,
    data: [{label:'Jan', value:2.7}, {label:'Feb', value:2.6}, /* … */ {label:'Jun', value:2.1}]
  });
</script>
```

Use a **bar** chart to compare categories, a **line** chart for a trend over time. Keep
it to one series and a handful of points — these helpers are for the common case, not
dashboards. For anything beyond that, or for real color/labeling/accessibility choices,
load the **`dataviz`** skill and follow it. Never invent data to fill a chart.

---

## 6. Citations & footnotes

Mark a claim with a `<sup class="fn" data-note="…">`. On load they auto-number in
document order, reveal the note on hover/click, and collect into a references list.

```html
<p>Conversion drives revenue downstream.<sup class="fn"
   data-note="Internal revenue model v3, finance/, 2026. Assumes 400k monthly visits."></sup></p>

<!-- put this where the reference list should render (leave it empty): -->
<section id="references"><h2>References</h2><div id="ref-list"></div></section>
```

Leave the `<sup>` empty — the number is filled in for you. The `data-note` is the full
citation. This keeps the claim checkable without breaking the reading flow.

---

## 7. Tabs

For parallel options or scenarios the reader should compare in one place (spec options,
what-ifs). Button `data-tab` must match its panel's `data-panel`.

```html
<div class="tabs">
  <div class="tablist" role="tablist">
    <button class="tab-btn" data-tab="a">A — Hire</button>
    <button class="tab-btn" data-tab="b">B — Automate</button>
  </div>
  <div class="tab-panel" data-panel="a"><p>Fastest relief, highest ongoing cost.</p></div>
  <div class="tab-panel" data-panel="b"><p>Slower to land, cheaper long-term.</p></div>
</div>
```

The first tab shows by default; arrow keys move between tabs.

---

## 8. Callouts, collapsibles & TOC

**Callouts** pull the eye to the one thing that matters — use sparingly.

```html
<div class="call tip"><div class="ic">&#9989;</div>
  <p><b>Recommendation.</b> Do B, then reassess next quarter.</p></div>
```

Classes: `note` (blue), `tip` (teal), `warn` (amber), `danger` (red). Swap the emoji in
`.ic` to suit.

**Collapsibles** keep methodology, caveats, and long tables out of the main thread:

```html
<details><summary>Assumptions &amp; methodology</summary>
  <div class="body"><p>The detail a doubting reader can open to verify.</p></div>
</details>
```

The **sticky TOC** is just links to your section ids; it tracks the current section as
the reader scrolls. Update `#toc` whenever you add or rename a `<section id="...">`.

---

## 9. Diagrams

For a process, timeline, or how-things-relate picture, hand-draw inline SVG using the
`d-box`, `d-label`, `d-edge` classes so it inherits the theme. Wrap it in
`<div class="diagram">` and give the `<svg>` a `role="img"` + `aria-label`.

```html
<div class="diagram">
  <svg viewBox="0 0 460 90" role="img" aria-label="Intake flows to triage then resolution">
    <rect class="d-box" x="10" y="25" width="120" height="40" rx="8"/>
    <text class="d-label" x="70" y="50" text-anchor="middle">Intake</text>
    <!-- more boxes + <path class="d-edge" ...> arrows -->
  </svg>
</div>
```

For data-driven pictures use a chart (section 5); for structure/flow use a diagram.

---

## 10. Taste checklist

Before finishing:

- Does the **conclusion come first** (lede + cards), so a ten-second read gets the point?
- Does it open with **no network**? (No CDN, web fonts, or remote images.)
- Is every **number honest** — real data, real sources, nothing invented to fill a
  chart or citation? Caveat what you're unsure of.
- Does each **interactive element earn its place** (a tab, chart, or badge that helps),
  or is it decoration? Cut what doesn't help.
- Any literal `<`, `>`, `&` shown as text (in code/config) **escaped**?
- Reads cleanly in **dark mode** and on a **narrow screen**?
