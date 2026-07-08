---
name: report-html
description: >-
  Turn a report, spec, findings write-up, research brief, analysis, memo,
  proposal, or postmortem into ONE rich, interactive, self-contained HTML page —
  executive summary, key-findings cards, sortable data tables with inline SVG
  charts, numbered citations with popovers, tabbed comparisons, status/severity
  badges, a sticky table of contents, collapsible detail, and callouts. Reach for
  this whenever the user wants to write, format, present, or hand off written or
  analytical material as a polished shareable document rather than a wall of
  Markdown — e.g. "write up these findings", "turn my notes into a report",
  "draft a spec", "make a research brief on X", "put together a readout for
  leadership". Use it whether they supply the substance (notes, data, a draft) or
  just a topic to draft from, and especially when they want it detailed, visual,
  interactive, or shareable. For pure code explanations use explain-code-html;
  for chart design load dataviz.
---

# Reports, specs, findings & research as interactive HTML

## What this produces

One **self-contained `.html` file** — all CSS and JavaScript inline, no network —
that presents written and analytical work the way a good briefing does: the bottom
line first, the evidence organized and navigable, the numbers shown as tables and
charts, and the depth tucked away until wanted. It works for a findings report, a
technical spec, a research brief, an analysis, a memo, a proposal, or a postmortem.

## Why HTML instead of a Markdown wall

A long Markdown document forces every reader down the same linear path and shows
numbers as raw text. A browser page lets the reader **land on the conclusion first,
jump to the section they care about, sort a table, hover a chart, expand the detail
they doubt, and check a citation in place.** Modern models aren't token-constrained,
so spend the tokens where they buy comprehension. The goal isn't decoration — it's
that a busy reader gets the point in ten seconds and can drill into any claim without
losing their place.

## The workflow

Substance first. A beautiful layout around a vague or wrong argument is worse than
plain text. Work in this order.

### 1. Understand the material and the reader

If the user handed you substance (notes, data, a draft, findings), read all of it and
find the **real story**: the one conclusion, the two or three findings that matter,
the numbers that prove them, the decision on the table. If they gave you only a topic,
draft the content first — but never invent findings, data, or citations. If a number
or claim isn't supported by what you were given, say so plainly rather than dressing
it up.

Decide **who reads this and what they need**. A leadership readout leads with the
decision and hides the methodology; an engineering spec needs the requirements and the
trade-offs; a research brief needs its sources visible. Match the shape to the reader.

### 2. Pick the document type, then structure it

Identify the type and use its spine (details in `references/patterns.md`):

- **Findings / report** → executive summary → key-findings cards → the data → analysis
  → recommendation → references.
- **Spec** → summary/goal → requirements → design/approach (tabs for options) → open
  questions → decision.
- **Research brief** → question → what we found (summary) → evidence with citations →
  limitations → sources.
- **Analysis / memo** → bottom line → the ask → the reasoning → what happens next.

### 3. Build from the template, then save

Copy `assets/template.html` and fill it in. It carries the design system, light/dark
theming, and the components already wired: sticky TOC scroll-spy, sortable tables,
tabs, and auto-numbered footnotes/citations, plus `Doc.barChart` / `Doc.lineChart`
for inline charts. You spend your effort on the argument, not the scaffolding. Read
`references/patterns.md` for a copy-paste recipe per component and the doc-type spines.

Save the file with a clear name (e.g. `q3-findings.html`), tell the user the path, and
offer to open it (`open` on macOS, `xdg-open` on Linux).

## Output requirements

Each one keeps the document usable — they're not style preferences.

- **Fully self-contained.** All CSS in a `<style>` tag, all JS in a `<script>` tag, no
  CDN links, no web fonts, no remote images. It'll be emailed, committed, or opened
  offline; a page that needs the network is a page that breaks. Charts and diagrams are
  inline SVG, not linked images.
- **Escape any literal `<`, `>`, `&`** you show as text (in code/config blocks or data)
  so the markup doesn't corrupt the page.
- **Readable in light and dark**, via the template's `prefers-color-scheme` variables,
  with comfortable contrast.
- **Responsive and keyboard-friendly.** Reads fine on a phone; every control (tabs,
  sortable headers, footnotes, collapsibles) works by keyboard, using real
  `<button>`/`<a>`/`<details>` elements.
- **Honest.** Don't fabricate data or sources to fill a chart or a citation. An empty
  or caveated section beats a confident fiction.

## What makes it land

A good page reads top-to-bottom as a briefing, with depth on demand:

- **Executive summary first** (`.lede`). The bottom line in 2–4 sentences, before
  anything else. If the reader stops here, they still got the point.
- **Key-findings cards** (`.cards`) for the two-to-five headline results — a metric, a
  one-line why, a status/severity badge. This is what a skimmer actually reads.
- **Data as tables and charts.** Put numbers in a sortable table *and*, when a trend or
  comparison matters, an inline chart — a reader grasps "APAC is lagging" from a bar far
  faster than from a column of figures. Keep charts honest and simple; for real color/
  labeling decisions consult the `dataviz` skill.
- **Citations in place** (`<sup class="fn" data-note="…">`). Numbered automatically,
  shown on hover/click, and collected into a references list — so claims are checkable
  without breaking the reading flow. Essential for research and findings.
- **Tabs** for parallel options (spec option A/B/C, scenarios) so they compare in one
  spot instead of scrolling between them. **Badges** for status, priority, or severity.
- **Callouts** for the one insight, risk, or recommendation the reader must not miss —
  used sparingly, or they stop meaning anything.
- **Sticky TOC + collapsible detail** so a long document stays navigable and the
  methodology/caveats live behind a `<details>` instead of clogging the main thread.

## Keep the taste high

Calm and structured beats loud and busy. Restrained palette with one accent, generous
whitespace, a clear hierarchy, one idea per section. Every interactive element should
earn its place by making the document easier to use — if a tab, chart, or badge doesn't
help the reader, cut it. Lead with substance; let the design serve it.

## Failure modes to avoid

- **Burying the conclusion.** If the reader has to hunt for the point, the format failed.
  Lead with the summary and the findings.
- **Decoration over substance.** Charts with no insight, badges on everything, five
  callouts per section. Rich ≠ noisy.
- **Fabricated evidence.** Inventing numbers, trends, or sources to fill a component is
  the worst outcome here. Show only what the material supports.
- **External dependencies.** A CDN or web font that fails to load takes the page down.
  Inline everything.

## Bundled resources

- `assets/template.html` — the scaffold. Copy it, replace the demo content. Carries the
  design system, theming, and all components wired (TOC, sortable tables, tabs,
  footnotes/citations) plus `Doc.barChart` / `Doc.lineChart`.
- `references/patterns.md` — copy-paste recipe for every component (exec summary, cards,
  badges, sortable tables, charts, citations, tabs, callouts, diagrams) and the
  structure spine for each document type. Read it while building the page.
