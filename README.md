# truong-skills

A Claude Code plugin marketplace of interactive-HTML skills:

- **explain-code-html** — turns any "explain this code" request into a single
  self-contained, interactive HTML page: annotated syntax-highlighted source,
  collapsible sections, a sticky table of contents, an interactive step-through
  with live "box track" state views, and inline SVG diagrams. Works on a snippet,
  a file, a multi-file subsystem, or a concept you name that it finds in the codebase.
- **report-html** — turns a report, spec, findings write-up, research brief, memo, or
  postmortem into a single self-contained, interactive HTML document: executive
  summary, key-findings cards, sortable data tables, inline SVG charts, numbered
  citations with popovers, tabbed comparisons, status/severity badges, a sticky table
  of contents, and callouts.

## Install (for users)

```
/plugin marketplace add truong-v/claude-skills
/plugin install explain-code-html@truong-skills
/plugin install report-html@truong-skills
```

Then just ask Claude to explain some code, or to write up a report / spec / findings /
research brief. You can also invoke a skill directly, e.g.
`/explain-code-html:explain-code-html` or `/report-html:report-html`.

## Repo layout

```
claude-skills/
├── .claude-plugin/
│   └── marketplace.json                     # the catalog users add
└── plugins/
    ├── explain-code-html/
    │   ├── .claude-plugin/plugin.json       # this plugin's manifest
    │   └── skills/explain-code-html/        # SKILL.md + assets/ + references/
    └── report-html/
        ├── .claude-plugin/plugin.json
        └── skills/report-html/              # SKILL.md + assets/ + references/
```
