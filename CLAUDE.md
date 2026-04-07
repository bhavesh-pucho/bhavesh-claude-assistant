# Bhavesh Claude Assistant

This repository holds Claude Code configuration, settings, and hooks for **Bhavesh's personal assistant system** at Pucho AI.

## Purpose

Central hub for all Claude Code session configuration — global CLAUDE.md, settings, hooks, and a queryable LLM knowledge base (wiki).

## Structure

```
.
├── CLAUDE.md              # This file — project context
├── global.CLAUDE.md       # Version-tracked copy of ~/.claude/CLAUDE.md
├── .claude/
│   └── settings.json      # Claude Code settings (permissions, hooks)
└── wiki/                  # LLM-queryable knowledge base (Obsidian vault)
    ├── _templates/        # Note templates
    ├── products/          # Pucho Office, Studio, PuchoSetu, Sportix, etc.
    ├── infrastructure/    # EC2, Dokploy, Traefik, Nginx, SSL, ZeroTier
    ├── ml/                # Text-to-SQL, QLoRA, vLLM, IndicF5
    ├── api/               # Pucho API schema, tool-call format, SSE events
    ├── architecture/      # Design decisions, policies, ADRs
    ├── reference/         # Tally schema, Activepieces internals
    └── inbox/             # Quick captures to triage later
```

## Wiki Usage

The `wiki/` folder is an **Obsidian vault** and an **LLM knowledge base** (Karpathy-style).

- Each note has a one-line `**Summary**` at the top — Claude reads this to determine relevance
- Notes use `[[wiki links]]` for cross-references
- Consistent `**Tags**` across notes enable search
- For deep lookups, Claude should search the `wiki/` folder before answering domain questions

**To query:** Navigate to this repo and ask Claude Code any question — it will search the wiki notes for answers.

**To add knowledge:** Create a new note from `_templates/note.md`, file it in the right folder.

**To triage inbox:** Ask Claude to organize `wiki/inbox/` notes into proper folders.

## Obsidian MCP Integration

This repo uses [obsidian-claude-code-mcp](https://github.com/iansinnott/obsidian-claude-code-mcp) to let Claude Code read/write the Obsidian vault directly.

### Setup (one-time)

1. Open `wiki/` folder as an Obsidian vault
2. Install **Claude Code** plugin from Obsidian Community Plugins
3. Enable the plugin — MCP server starts on `localhost:22360`
4. Claude Code auto-connects via `.claude/settings.json` (already configured)

### Available MCP Tools

| Tool | What it does |
|---|---|
| `view` | Read file contents from vault |
| `str_replace` | Find and replace text in notes |
| `create` | Create new notes |
| `insert` | Insert content into existing notes |
| `get_current_file` | Get the currently active file in Obsidian |
| `get_workspace_files` | List vault structure |
| `obsidian_api` | Direct Obsidian API access |

### Multiple Vaults

If running multiple vaults, each needs a unique port. Update `.claude/settings.json` accordingly.

## Conventions

- All config changes go through git — no ad-hoc edits to production config
- Follow the Ralph loop (read → plan → confirm → execute → verify → update)
- MIT-first rule applies even to tooling choices
- Keep notes focused — one topic per file, ~1000 words max
- Use consistent terminology across notes
