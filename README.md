# Mycelium Coding Agents

Org-specific Claude Code agents for the Mycelium Agent Framework.

## Relationship to personal coding-agents

This repo contains **only Mycelium-specific** agents. Universal coding agent
instructions (TDD enforcement, type safety, code style, shared agents, skills,
and commands) live in the personal
[chasemp/coding-agents](https://github.com/chasemp/coding-agents) repo.

The layering model:

```
┌─────────────────────────────────────────────┐
│ Layer 1: Personal (always loaded globally)  │
│ ~/.claude/coding-agents/                    │
│ Source: chasemp/coding-agents               │
└──────────────────┬──────────────────────────┘
                   │ loaded via ~/.claude/CLAUDE.md @includes
                   ▼
┌─────────────────────────────────────────────┐
│ Layer 2: Org (this repo)                    │
│ Cloned into project .claude/org-agents/     │
│ Contains: spore-validator, ring-inspector   │
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
| `agents/spore-validator.md` | Validates spore JSONL files against schema before operations |
| `agents/ring-inspector.md` | Inspects ring state, manifest, and memory integrity |

## Setup for Mycelium project repos

### Prerequisites

Personal coding-agents must be installed globally first:

```bash
git clone git@github-personal:chasemp/coding-agents.git ~/.claude/coding-agents
# Then add to ~/.claude/CLAUDE.md:
#   @coding-agents/CLAUDE.md
#   @coding-agents/agents.md
```

### Wiring a Mycelium repo

From the project repo root:

```bash
# 1. Clone this org repo into the project (gitignored)
git clone git@github-personal:mycelium-agent-framework/coding-agents.git .claude/org-agents
echo '.claude/org-agents/' >> .gitignore

# 2. Symlink org-specific agents
for agent in spore-validator ring-inspector; do
  ln -sf "org-agents/agents/${agent}.md" ".claude/agents/${agent}.md"
done
```

### Syncing

```bash
git -C ~/.claude/coding-agents pull --ff-only          # personal layer
git -C .claude/org-agents pull --ff-only                # org layer
```
