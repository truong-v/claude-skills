---
name: explain-code-html
description: >-
  Generate a rich, interactive, self-contained HTML page that explains code —
  annotated syntax-highlighted source, collapsible sections, a table of contents,
  an interactive step-through of the key path, and inline SVG diagrams. Reach for
  this whenever the user wants to understand, explain, walk through, visualize, or
  document how code works — a snippet, a function, a whole file, an algorithm, a
  subsystem across files, or a concept or behavior you must first track down in a
  codebase (e.g. "how does caching work in this service", "walk me through the
  request lifecycle"). Especially use it when they want the explanation detailed,
  visual, interactive, or shareable rather than a few lines of terminal text, or
  when they ask for an "HTML explanation", "interactive walkthrough", or "visual
  explanation" — even if they never say the word "HTML". Prefer it over plain text
  whenever a diagram or clickable annotations would make code click faster than prose.
---

# Explain code as a rich, interactive HTML page

## What this produces

One **self-contained `.html` file** — all CSS and JavaScript inlined, no external
downloads — that a person opens in a browser to *understand* a piece of code far
faster than they could from a wall of prose. Think of the output as a small
teaching page: a clear mental model up front, a diagram of how the pieces fit, the
real source annotated line by line, an interactive step-through of the tricky path,
and the gotchas that bite people.

## Why HTML instead of a text answer

Plain Markdown in a terminal can only stack paragraphs and code blocks. A browser
page can do the things that actually make code *click*: draw the data flow as a
diagram, let the reader hover a line to see what it does, collapse the deep details
until they're wanted, and animate an algorithm one step at a time while showing the
variables change. Older models avoided HTML to save tokens; that constraint is
gone, so spend the tokens where they buy comprehension. The goal is not a pretty
page for its own sake — it is a page where **every interactive element removes a
question the reader would otherwise have to hold in their head.**

## The workflow

Understanding has to come before presentation. A beautiful page that explains the
code wrong is worse than useless. Work in this order.

### 1. Understand the code for real

**If the request names a concept or behavior instead of a file** — "how does
caching work here", "explain the auth flow", "walk me through how retries happen" —
your first job is to *find the code that concept lives in.* Search the repo, follow
the entry points and the calls, and figure out which handful of files and functions
actually implement it. The finding is often the hard part; a reader asks about a
concept precisely because it's spread across places they can't easily assemble in
their head. Explain the real code you located, not a generic textbook version of the
concept.

Read the actual source — the file(s) named or the ones you found, plus whatever they
call into that matters. Trace the real path of execution, not the happy-path story
you'd guess from names. As you read, hunt specifically for the **non-obvious parts**: the line
that does the clever thing, the invariant that isn't stated, the edge case the code
quietly handles, the reason it's written this weird way. Those are what a reader
most needs explained, and what a lazy explanation skips.

Also decide **who this is for and how deep to go.** "Explain this regex" wants one
tight page; "walk me through the auth subsystem" wants an architecture diagram and
several collapsible layers. Match the depth to the ask.

If anything about the code's behavior is genuinely unclear, say so in the page
rather than inventing an explanation. Confident-sounding fiction is the worst
failure mode here.

### 2. Plan the explanation, then build

Before writing HTML, know your answers to: *What is the one-sentence purpose? What's
the mental model I want the reader to leave with? What single diagram captures the
structure? Which stretch of code deserves the step-through?* Then build the page by
copying `assets/template.html` and filling it in. The template already carries the
design system, the light/dark theming, the collapsible + table-of-contents behavior,
a dependency-free syntax highlighter, and a step-through widget — so you spend your
effort on the explanation, not on reinventing scaffolding.

For the how-to of each interactive piece — annotated code blocks, the stepper,
SVG diagrams, callouts — read `references/patterns.md`. It has copy-paste recipes.

### 3. Save it and hand it over

Write the file next to the code it explains (or in the working directory) with a
clear name like `binary-search-explained.html`. Tell the user the path and offer to
open it. On macOS that's `open file.html`; on Linux `xdg-open file.html`.

## Output requirements

These aren't style preferences — each one keeps the page usable.

- **Fully self-contained.** All CSS in a `<style>` tag, all JS in a `<script>` tag,
  no CDN links, no web fonts, no remote images. The reader may open this offline, mail
  it to a colleague, or commit it to a repo. A page that needs the network to render
  is a page that breaks. Draw diagrams as inline SVG rather than linking images.
- **Escape the code you display.** Source shown on the page must be HTML-escaped
  (`<` → `&lt;`, `&` → `&amp;`) so it renders as text instead of being parsed as
  markup — otherwise a snippet containing `<div>` or `&&` silently corrupts the page.
  The template's highlighter handles this; if you hand-write a code block, do it there.
- **Readable in light and dark.** Use the template's `prefers-color-scheme` variables
  so the page suits whoever opens it. Keep text contrast comfortable.
- **Responsive and keyboard-friendly.** It should read fine on a laptop or a phone,
  and every interactive control should work by keyboard, not mouse only. Use real
  `<button>`/`<a>`/`<details>` elements rather than click-handlers on `<div>`s.
- **The code is the star.** Monospace, generous line height, and never so much
  decoration that the source is hard to read.

## What makes the explanation rich

A good page has a spine the reader can follow top to bottom, with the option to dive
deeper anywhere. A reliable order (adapt to the code):

1. **Title + one line** saying what this code *is*.
2. **Mental model / TL;DR** — the big idea in 2–4 sentences, before any code. If the
   reader remembers only this, they should still be better off.
3. **A diagram** — one clear picture of how the parts relate. Use inline **SVG** for
   the *static structure* (architecture, data flow, a sequence). When what you're
   explaining is a **flow, pipeline, or data structure whose state changes**, also plan
   an **interactive box track** (see step 5) that shows that state moving. The two
   complement each other — include both when both help; the SVG gives the overview,
   the boxes show it in motion.
4. **Annotated walkthrough** — the real source, syntax-highlighted, with notes tied
   to specific lines (hover or click a line to reveal its note). Group into logical
   chunks with a sentence of context each.
5. **Step-through of the key path** — the one algorithm or flow that's hard to hold
   in your head, as a prev/next stepper that highlights the active line and shows the
   state changing. When that state is a data structure or a pipeline (a Map reordering,
   a queue filling, a request moving stage to stage), render it as a live **box track**
   that visibly updates each step — watching the boxes move, evict, and highlight
   teaches far more than a list of variable values. The template's stepper takes a
   `boxes` field per step for exactly this. This is where interactivity earns its keep
   — put the effort here.
6. **Gotchas & edge cases** — what surprises people, in callout boxes.
7. **Summary / glossary** — a table recapping the pieces, or key terms defined.

Use these rich features deliberately, each to answer a question prose answers
poorly:

- **Annotated code** answers *"what does this specific line do?"* — the note is right
  there instead of buried in a paragraph the reader has to map back onto the code.
- **Collapsible sections + sticky nav** answer *"where am I and how do I control the
  depth?"* — the reader expands the low-level detail only when they want it, and can
  jump around a long page.
- **Step-through execution** answers *"what actually happens when this runs?"* — far
  more convincing than describing a loop is letting the reader watch it iterate.
- **SVG diagrams** answer *"how do these pieces relate?"* — a static picture of the
  structure, in a way a list of files never will.
- **Interactive box tracks** answer *"what does the state look like right now?"* — a
  row of labelled boxes (a Map, a queue, a stack, pipeline stages) that reorders,
  fills, evicts, or highlights as the reader steps. For a flow or data structure this
  beats a static SVG because it *moves*; pair it with the SVG overview rather than
  choosing one.

Callout boxes (note / tip / warning / security) pull the eye to the things that
matter most. Use them for the real insight, not decoration.

## Keep the taste high

Clean and calm beats busy and loud. Restrained palette with a single accent color,
generous whitespace, clear type hierarchy, one idea per section. The interactivity
should feel purposeful — if an animation or widget doesn't remove a question, cut it.
If the explanation involves data or metrics you're charting, load the `dataviz`
skill for the chart itself.

## Failure modes to avoid

- **Dumping the code with a thin paragraph on top.** That's not an explanation. The
  value is in the annotations, the diagram, and the step-through.
- **Explaining what you wish the code did.** Explain what it *actually* does; trace it.
- **Interactivity theater.** Widgets that move but don't teach. Every interactive bit
  should earn its place by making something clearer.
- **External dependencies.** A CDN script or web font that fails to load takes the
  page down with it. Inline everything.

## Bundled resources

- `assets/template.html` — the starting scaffold. Copy it, then replace the demo
  content. Carries the design system, light/dark theming, TOC + collapsibles, a
  self-contained highlighter, and a working step-through widget.
- `references/patterns.md` — copy-paste recipes and worked examples for annotated
  code blocks, the step-through widget, inline SVG diagrams, and callouts, plus tips
  on using the highlighter. Read it while building the page.
