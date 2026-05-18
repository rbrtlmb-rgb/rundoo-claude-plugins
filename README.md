# Rundoo Claude Code Plugins

Internal Claude Code plugins for the Rundoo team. Bundles workflow skills that automate common Notion / GTM operations from inside Claude Code.

## What's inside

### `rundoo` plugin

| Skill | Invocation | What it does |
|---|---|---|
| Bug ticket | `/rundoo:bug-ticket` | Files a bug in the Notion **🪲 CX Tickets** database using the standard `[Bug] - [Actual Issue]` template. Pulls details from conversation context, confirms with you, then creates the page. |

## Install

In Claude Code, run:

```
/plugin marketplace add rundoo/rundoo-claude-plugins
/plugin install rundoo@rundoo-plugins
```

Replace `rundoo/rundoo-claude-plugins` with the actual `<owner>/<repo>` once this is pushed to GitHub.

After install, the skill is available as `/rundoo:bug-ticket` (or just describe what you want — "file a bug for X" — and Claude will trigger it).

## Prerequisites

The bug-ticket skill calls the **Notion MCP** to create pages. Each coworker needs to connect Notion to their own Claude separately:

1. Go to https://claude.ai → Settings → Connectors
2. Add the **Notion** connector and authorize it against the Rundoo workspace
3. Confirm access by running `/mcp` inside Claude Code and seeing Notion listed

Without this, the skill loads but the create-page call will fail.

## Updates

This plugin omits an explicit `version` field, so every commit on `main` is a new version. Users get the latest with:

```
/plugin update rundoo@rundoo-plugins
```

(Claude Code also checks for updates on startup.)

## Adding more skills

To add a new skill to the `rundoo` plugin:

1. Create `plugins/rundoo/skills/<skill-name>/SKILL.md`
2. Top of the file: YAML frontmatter with `name` (matches directory) and `description` (what triggers it)
3. Body: instructions for Claude — what to do, what tools to call, edge cases
4. Commit and push — coworkers pick it up on next `/plugin update`

To spin up a *separate* plugin in this marketplace (e.g., `rundoo-eng`):

1. Create `plugins/<plugin-name>/.claude-plugin/plugin.json`
2. Add it to the `plugins[]` array in `.claude-plugin/marketplace.json`

## Layout

```
rundoo-claude-plugins/
├── .claude-plugin/
│   └── marketplace.json          # registers this repo as a marketplace
├── plugins/
│   └── rundoo/                    # the "rundoo" plugin
│       ├── .claude-plugin/
│       │   └── plugin.json
│       └── skills/
│           └── bug-ticket/
│               └── SKILL.md
└── README.md
```
