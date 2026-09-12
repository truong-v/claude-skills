# truong-skills

A Claude Code plugin marketplace of skills:

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
- **orchestrate** — executes a plan you already approved in tech-lead mode: it turns the
  plan into a delegation map, dispatches self-contained briefs to specialist subagents in
  parallel waves, then verifies the results itself before reporting. Ships the three
  delegate agents it uses (`deep-reasoner`, `balanced-implementer`, `fast-worker`).

## Install (for users)

```
/plugin marketplace add truong-v/claude-skills
/plugin install explain-code-html@truong-skills
/plugin install report-html@truong-skills
/plugin install orchestrate@truong-skills
```

Then just ask Claude to explain some code, or to write up a report / spec / findings /
research brief. You can also invoke a skill directly, e.g.
`/explain-code-html:explain-code-html` or `/report-html:report-html`.

`orchestrate` is opt-in by design: approve a plan first, then run
`/orchestrate:orchestrate` to have Claude execute it through subagents.

## Repo layout

```
claude-skills/
├── .claude-plugin/
│   └── marketplace.json                     # the catalog users add
└── plugins/
    ├── explain-code-html/
    │   ├── .claude-plugin/plugin.json       # this plugin's manifest
    │   └── skills/explain-code-html/        # SKILL.md + assets/ + references/
    ├── report-html/
    │   ├── .claude-plugin/plugin.json
    │   └── skills/report-html/              # SKILL.md + assets/ + references/
    └── orchestrate/
        ├── .claude-plugin/plugin.json
        ├── agents/                          # the three delegate subagents
        └── skills/orchestrate/              # SKILL.md
```
