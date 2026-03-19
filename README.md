# AlpheusCEF Coding Agents

Org-specific Claude Code commands and configuration for AlpheusCEF repos.

## Relationship to personal coding-agents

This repo contains **only AlpheusCEF-specific** guidance. Universal coding agent
instructions (TDD enforcement, type safety, code style, shared agents, skills,
and commands) live in the personal
[chasemp/coding-agents](https://github.com/chasemp/coding-agents) repo.

The layering model:

```
┌─────────────────────────────────────────────┐
│ Layer 1: Personal (always loaded globally)  │
│ ~/.claude/coding-agents/                    │
│ Source: chasemp/coding-agents               │
│ Contains: CLAUDE.md, agents, skills,        │
│           commands (pr, generate-pr-review)  │
└──────────────────┬──────────────────────────┘
                   │ loaded via ~/.claude/CLAUDE.md @includes
                   ▼
┌─────────────────────────────────────────────┐
│ Layer 2: Org (this repo)                    │
│ Cloned into project .claude/org-agents/     │
│ Contains: AlpheusCEF-specific commands      │
│           (release-alph, doc-sync,          │
│            config-scout)                    │
└──────────────────┬──────────────────────────┘
                   │ loaded via project CLAUDE.md @includes
                   ▼
┌─────────────────────────────────────────────┐
│ Layer 3: Project                            │
│ Each repo's own CLAUDE.md                   │
└─────────────────────────────────────────────┘
```

**Current status (2026-03-19):** The personal layer handles all universal
guidance via the global `~/.claude/CLAUDE.md`. If the org grows beyond a single
contributor and needs self-contained bootstrapping (without relying on a personal
global install), this repo would absorb or vendor the personal content. The
architecture supports that evolution without restructuring.

## What's here

| Path | Purpose |
|------|---------|
| `commands/release-alph.md` | Cut versioned releases of alph-cli |
| `commands/doc-sync.md` | Sync AlpheusCEF docs against alph-cli changes |
| `commands/config-scout.md` | Compare external agent configs against local setup |
| `config-scout/` | Config-scout findings and analysis reports |

## Design context

Full project design, architecture decisions, and implementation plan are in the
[overview repo](../overview/STATE.md).

## Setup for AlpheusCEF project repos

### Prerequisites

Personal coding-agents must be installed globally first:

```bash
git clone git@github-personal:chasemp/coding-agents.git ~/.claude/coding-agents
# Then add to ~/.claude/CLAUDE.md:
#   @coding-agents/CLAUDE.md
#   @coding-agents/agents.md
```

### Wiring an AlpheusCEF repo

From the project repo root:

```bash
# 1. Clone this org repo into the project (gitignored)
git clone git@github-personal:AlpheusCEF/coding-agents.git .claude/org-agents
echo '.claude/org-agents/' >> .gitignore

# 2. Symlink org-specific commands
for cmd in release-alph doc-sync config-scout; do
  ln -sf "org-agents/commands/${cmd}.md" ".claude/commands/${cmd}.md"
done

# 3. Project CLAUDE.md can @-include org content as needed
```

### Syncing

```bash
git -C ~/.claude/coding-agents pull --ff-only          # personal layer
git -C .claude/org-agents pull --ff-only                # org layer
```
