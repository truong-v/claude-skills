# truong-skills

A Claude Code plugin marketplace. Right now it ships one plugin:

- **explain-code-html** — turns any "explain this code" request into a single
  self-contained, interactive HTML page: annotated syntax-highlighted source,
  collapsible sections, a sticky table of contents, an interactive step-through
  with live "box track" state views, and inline SVG diagrams. Works on a snippet,
  a file, a multi-file subsystem, or a concept you name that it finds in the codebase.

## Install (for users)

```
/plugin marketplace add truong-v/claude-skills
/plugin install explain-code-html@truong-skills
```

Then just ask Claude to explain, walk through, or visualize some code. You can also
invoke it directly with `/explain-code-html:explain-code-html`.

## Repo layout

```
claude-skills/
├── .claude-plugin/
│   └── marketplace.json                     # the catalog users add
└── plugins/
    └── explain-code-html/
        ├── .claude-plugin/
        │   └── plugin.json                  # this plugin's manifest
        └── skills/
            └── explain-code-html/
                ├── SKILL.md                 # the skill
                ├── assets/template.html     # ready-to-fill HTML scaffold
                └── references/patterns.md   # copy-paste recipes
```

## Ready to publish

Author name, email, marketplace name, and the GitHub `owner/repo` (`truong-v/claude-skills`)
are all filled in. Change any of them only if you want to — otherwise just push.

## Publish

```
# create an EMPTY repo named claude-skills on GitHub first, then:
cd claude-skills
git remote add origin https://github.com/truong-v/claude-skills.git
git push -u origin main
```

That's it — anyone can now install with the two commands under "Install" above.

## Optional: get it listed in Anthropic's official community marketplace

Once the repo is public on GitHub, submit its URL at
<https://platform.claude.com/plugins/submit> (or via the Console plugins submission
form). After automated + human review it becomes installable as
`explain-code-html@claude-plugins-community`, pinned to a commit and auto-updated as
you push. Run `claude plugin validate .` locally first — the same checks run on submit.
