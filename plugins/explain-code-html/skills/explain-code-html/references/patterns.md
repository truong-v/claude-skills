# Patterns: building the explanation

Copy-paste recipes for the interactive pieces. All of them assume you started from
`assets/template.html`, which already defines the CSS classes and the `Explain`
helpers (`annotate`, `stepper`, `boxes`) plus the on-load syntax highlighter. Keep the
parts you need, delete the demo content, and wire your own calls at the bottom `<script>`.

## Contents
1. [Code blocks & the highlighter](#1-code-blocks--the-highlighter)
2. [Annotated code (hover/click a line)](#2-annotated-code)
3. [Step-through execution & live box tracks](#3-step-through-execution)
4. [SVG diagrams](#4-svg-diagrams)
5. [Callouts](#5-callouts)
6. [Collapsible deep-dives & the TOC](#6-collapsible-deep-dives--the-toc)
7. [Structure & taste checklist](#7-structure--taste-checklist)

---

## 1. Code blocks & the highlighter

Put source inside `<pre><code data-lang="...">`. On load, the highlighter escapes it,
colors it, and wraps each line in `<span class="ln" data-ln="N">` so annotations and
the stepper can target lines by number.

```html
<pre><code data-lang="python">def greet(name):
    return f"hello {name}"</code></pre>
```

**Escaping.** Any literal `<`, `>`, or `&` in the source must be written as `&lt;`,
`&gt;`, `&amp;` — otherwise the browser parses your code as markup and the page
breaks *before* the highlighter runs. This is the single most common way these pages
break, so escape as you paste.

**Languages built in:** `python`, `javascript`, `typescript`, `java`, `c`, `cpp`,
`go`, `rust`, `ruby`, `shell`, `sql`. Unknown languages still get strings, numbers,
and `//` comments — just no keyword coloring. To add a language, add one line to the
`LANGS` map in the template: `lang:{line:'#', block:false, kw:'word1 word2 ...'}`
(`line` = line-comment marker, `block` = whether `/* */` applies).

**Line numbers** come from the `.ln::before` CSS automatically. If a block shouldn't
show them (a one-liner, or shell output), drop `data-lang` and use a plain `<pre>`.

The highlighter is intentionally small — it colors tokens, it is not a full parser.
If one specific line needs perfect coloring, you can hand-write its spans using the
`tok-kw`, `tok-str`, `tok-num`, `tok-com`, `tok-fn` classes.

---

## 2. Annotated code

Show the real source, then attach a note to the lines that need one. The reader
hovers, clicks, or tabs to a marked line to reveal its explanation right under it.
This beats a prose paragraph because the note sits on the exact line it describes.

```html
<pre><code id="parse-src" data-lang="javascript">function parse(input) {
  const tokens = input.trim().split(/\s+/);
  return tokens.map(Number);
}</code></pre>
<script>
  Explain.annotate('#parse-src', {
    2: 'trim() drops surrounding whitespace; split on one-or-more spaces gives the words.',
    3: 'Number coerces each token; a non-numeric token becomes NaN, which matters downstream.'
  });
</script>
```

Keys are 1-based line numbers **within that code block**. Annotate the lines that
carry the insight — not every line. Three good notes beat ten obvious ones.

---

## 3. Step-through execution

The highest-value widget. Pick the one algorithm or flow that's hard to simulate in
your head, and let the reader walk it a step at a time while the state updates. Far
more convincing than describing a loop is watching it iterate.

```html
<pre><code id="loop-src" data-lang="python">total = 0
for x in nums:
    total += x
return total</code></pre>
<div id="loop-step" class="stepper"></div>
<script>
  Explain.stepper({
    code: '#loop-src',
    mount: '#loop-step',
    steps: [
      {ln:1, note:'Start the accumulator at zero.',        state:{total:0}},
      {ln:2, note:'First item: x = 3.',                    state:{x:3, total:0}},
      {ln:3, note:'Add it in.',                            state:{x:3, total:3}},
      {ln:2, note:'Next item: x = 5.',                     state:{x:5, total:3}},
      {ln:3, note:'Add it in.',                            state:{x:5, total:8}},
      {ln:4, note:'Loop is done — return the sum.',        state:{total:8}}
    ]
  });
</script>
```

- `ln` is the line to highlight; pass an array like `ln:[3,4]` to highlight several.
- `note` is one plain sentence about what just happened.
- `state` is any set of name → value pairs shown as chips. Use it to expose the
  variables, a growing data structure, the call stack — whatever the reader should
  watch. Values are shown verbatim, so you can write `state:{stack:'[a, b]'}`.
- Prev/Next work by mouse, and by arrow keys once the widget has focus.

Keep steps to the interesting stretch. Ten focused steps teach; forty exhaustive
ones bore.

### Live box track — data structures & pipelines

When the state is a **data structure or a pipeline** (a Map reordering, a queue
filling, a stack growing, a request moving stage to stage), don't just list variable
values — paint it as a row of boxes that visibly changes each step. Add a `boxes`
field to any step and the stepper renders it above the state chips:

```js
Explain.stepper({
  code: '#lru-src', mount: '#lru-step',
  steps: [
    {ln:3, note:'get(1) — re-insert key 1 so it becomes most-recently-used.',
      boxes:{caption:'Map (oldest → newest)', cells:[
        {label:2, sub:'B'}, {label:1, sub:'A', state:'active'}]}},
    {ln:8, note:'put(3) at capacity — evict the oldest (key 2), then add 3 at the back.',
      boxes:{caption:'Map (oldest → newest)', cells:[
        {label:2, sub:'B', state:'evicted'}, {label:1, sub:'A'}, {label:3, sub:'C', state:'new'}]}},
  ]
});
```

- `boxes` is either an array of cells or `{caption, cells}`.
- Each cell is `{label, sub, state}`: `label` is the big value (the key, the stage
  name), `sub` the small line under it (an index, a stored value). `state` colours the
  box — `'active'` (accent, lifts), `'new'` (green), `'done'` (green, settled),
  `'evicted'` (dashed, struck through), `'muted'` (dimmed / out of play).
- Rebuild the whole `cells` array each step to show the new arrangement; the CSS
  transitions handle the fade and lift so changes read as motion.

Reviewers singled this out as the clearest piece: for a Map, queue, stack, or a
`logger → auth → handler` pipeline, a moving box track beats a static picture. Pair it
with an SVG (section 4) for the structural overview — use **both**, not one or the
other. You can also drop a standalone track outside a stepper with
`Explain.boxes('#mount', {caption:'…', cells:[…]})`, handy for a before/after.

---

## 4. SVG diagrams

One clear picture of how the parts relate lands better than a list of files. Draw
inline SVG using the template's `d-box`, `d-label`, `d-edge`, `d-note` classes so the
diagram inherits the light/dark theme. Wrap each diagram in
`<div class="diagram">` and give the `<svg>` a `role="img"` and an `aria-label`
describing it, so it's meaningful to screen readers and when printed.

**SVG or box track?** Reach for SVG when the structure is *fixed* — the shape of the
system, who calls whom, the pipeline laid out. When the thing *changes over the run*
(a data structure mutating, a request flowing through stages), use the interactive box
track from section 3 instead — or, best, use both: the SVG for the map, the boxes for
the motion. The two answer different questions and reinforce each other.

**Boxes and arrows (flow / pipeline).** Reuse the arrowhead marker from the template
(`url(#arrow)`), or paste this `defs` block once per page:

```html
<div class="diagram">
  <svg viewBox="0 0 460 90" role="img" aria-label="Request flows through middleware to the handler">
    <defs><marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7"
      markerHeight="7" orient="auto"><path d="M0 0 L10 5 L0 10 z" fill="#8a8f9a"/></marker></defs>
    <rect class="d-box" x="10"  y="25" width="120" height="40" rx="8"/>
    <text class="d-label" x="70"  y="50" text-anchor="middle">Request</text>
    <rect class="d-box" x="170" y="25" width="120" height="40" rx="8"/>
    <text class="d-label" x="230" y="50" text-anchor="middle">Middleware</text>
    <rect class="d-box" x="330" y="25" width="120" height="40" rx="8"/>
    <text class="d-label" x="390" y="50" text-anchor="middle">Handler</text>
    <path class="d-edge" d="M130 45 H170" marker-end="url(#arrow)"/>
    <path class="d-edge" d="M290 45 H330" marker-end="url(#arrow)"/>
  </svg>
</div>
```

**Layered stack** (e.g. an architecture): stack full-width `d-box` rects vertically,
each with a centered `d-label`. **Sequence**: draw vertical lifelines as thin
`d-edge` lines and horizontal `d-edge` arrows between them with a `d-note` label per
message. Keep `viewBox` tight to the drawing so it scales cleanly; let CSS
(`max-width:100%`) handle responsiveness — don't hard-code a pixel width on the svg.

Keep diagrams simple: a handful of boxes, clear labels, few crossings. A diagram the
reader has to decode is worse than none.

---

## 5. Callouts

Pull the eye to what matters. Four flavors, same markup, different second class:

```html
<div class="call note"><div class="ic">&#8505;&#65039;</div>
  <p><b>Note.</b> Neutral context worth surfacing.</p></div>

<div class="call tip"><div class="ic">&#128161;</div>
  <p><b>Tip.</b> The insight that makes it click.</p></div>

<div class="call warn"><div class="ic">&#9888;&#65039;</div>
  <p><b>Watch out.</b> A gotcha or edge case that bites people.</p></div>

<div class="call sec"><div class="ic">&#128274;</div>
  <p><b>Security.</b> A safety-relevant consideration.</p></div>
```

Use them for the real insight, not decoration. A page that's all callouts has none.

---

## 6. Collapsible deep-dives & the TOC

Let readers control depth. Keep the main thread readable and tuck the low-level
detail — a proof, a perf note, an alternative implementation — behind a `<details>`
so only the curious open it.

```html
<details>
  <summary>Deep dive: why this is O(log n)</summary>
  <div class="body">
    <p>Each step halves the search window, so ...</p>
  </div>
</details>
```

The sticky sidebar `#toc` is just links to your `section` ids. When you add,
rename, or remove a `<section id="...">`, update the matching `<a href="#...">` in
the `#toc` nav. The template auto-highlights the current section as the reader
scrolls — no wiring needed.

---

## 7. Structure & taste checklist

A good page reads top to bottom as a story, with optional depth anywhere:

- **Title + one line** — what this code *is*.
- **Mental model first** — the big idea in 2–4 sentences, before any code.
- **One diagram** — how the parts relate.
- **Annotated walkthrough** — the real source, notes on the lines that matter.
- **Step-through** — the one hard path, simulated.
- **Gotchas** — callouts for what surprises people.
- **Summary / glossary** — a table that recaps the pieces or defines key terms.

Before you finish, sanity-check:

- Does it open with **no network**? (No CDN links, web fonts, or remote images.)
- Is every displayed `<`, `>`, `&` in the source **escaped**?
- Does it read cleanly in **dark mode** and on a **narrow screen**?
- Does every interactive element **remove a question** rather than just move? If a
  widget doesn't teach, cut it.
- Did you explain what the code **actually does**, having traced it — not what the
  names suggest it does?
