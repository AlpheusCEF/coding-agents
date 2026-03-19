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
│ Local checkout alongside project repos      │
│ Contains: AlpheusCEF-specific commands      │
│           (release-alph, doc-sync,          │
│            config-scout)                    │
└──────────────────┬──────────────────────────┘
                   │ symlinked into project .claude/
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
git clone git@github-personal:chasemp/coding-agents.git ~/git/chasemp/coding-agents
ln -sf ~/git/chasemp/coding-agents ~/.claude/coding-agents
# Then add to ~/.claude/CLAUDE.md:
#   @coding-agents/CLAUDE.md
#   @coding-agents/agents.md
```

### Wiring an AlpheusCEF repo

All AlpheusCEF repos live under a shared local tree alongside this repo. The
expected layout is:

```
~/git/chasemp/AlpheusCEF/
  coding-agents/          ← this repo
  alph-cli/
  fin-cli/
  ...
```

From the new project repo root:

```bash
PERSONAL="$HOME/.claude/coding-agents"
ORG="$HOME/git/chasemp/AlpheusCEF/coding-agents"

# 1. Create .claude structure
mkdir -p .claude/agents .claude/commands

# 2. Symlink agents from the personal repo
for agent in tdd-guardian py-enforcer pr-reviewer refactor-scan \
             progress-guardian adr docs-guardian learn use-case-data-patterns; do
  ln -sf "${PERSONAL}/${agent}.md" ".claude/agents/${agent}.md"
done

# 3. Symlink generic commands from the personal repo
for cmd in pr generate-pr-review; do
  ln -sf "${PERSONAL}/commands/${cmd}.md" ".claude/commands/${cmd}.md"
done

# 4. Symlink org-specific commands from this repo
for cmd in release-alph doc-sync config-scout; do
  ln -sf "${ORG}/commands/${cmd}.md" ".claude/commands/${cmd}.md"
done

# 5. Add project-specific CLAUDE.md (personal layer loads globally)
# .claude/CLAUDE.md should contain only project-specific instructions
```

**Alternative for contributors without the local tree:** Clone this repo into
the project directly:

```bash
git clone git@github-personal:AlpheusCEF/coding-agents.git .claude/org-agents
echo '.claude/org-agents/' >> .gitignore
# Then symlink from .claude/org-agents/commands/ instead of $ORG/commands/
```

### Syncing

```bash
git -C ~/git/chasemp/coding-agents pull --ff-only                          # personal layer
git -C ~/git/chasemp/AlpheusCEF/coding-agents pull --ff-only               # org layer
```
