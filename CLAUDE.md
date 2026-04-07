# Bhavesh Claude Assistant

This repository holds Claude Code configuration, settings, and hooks for **Bhavesh's personal assistant system** at Pucho AI.

## Purpose

Central hub for all Claude Code session configuration — global CLAUDE.md, settings, hooks, and any shared scripts/tools used across Pucho AI projects.

## Structure

```
.
├── CLAUDE.md              # This file — project context
├── .claude/
│   └── settings.json      # Claude Code settings (permissions, hooks)
└── README.md              # (optional) public-facing docs
```

## Usage

- Global CLAUDE.md (`~/.claude/CLAUDE.md`) is the single source of truth for all sessions
- This repo tracks version history of that config
- Any changes to global Claude Code behavior should be committed here first

## Conventions

- All config changes go through git — no ad-hoc edits to production config
- Follow the Ralph loop (read → plan → confirm → execute → verify → update)
- MIT-first rule applies even to tooling choices
