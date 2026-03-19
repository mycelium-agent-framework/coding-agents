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
│ Local checkout alongside project repos      │
│ Contains: spore-validator, ring-inspector   │
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

All Mycelium repos live under a shared local tree alongside this repo. The
expected layout is:

```
~/git/chasemp/mycelium-agent-framework/
  coding-agents/          ← this repo
  mycelium-osx/
  vivian-core/
  ...
```

From the new project repo root:

```bash
PERSONAL="$HOME/.claude/coding-agents"
ORG="$HOME/git/chasemp/mycelium-agent-framework/coding-agents"

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

# 4. Symlink org-specific agents from this repo
for agent in spore-validator ring-inspector; do
  ln -sf "${ORG}/agents/${agent}.md" ".claude/agents/${agent}.md"
done

# 5. Add project-specific CLAUDE.md (personal layer loads globally)
# .claude/CLAUDE.md should contain only project-specific instructions
```

**Alternative for contributors without the local tree:** Clone this repo into
the project directly:

```bash
git clone git@github-personal:mycelium-agent-framework/coding-agents.git .claude/org-agents
echo '.claude/org-agents/' >> .gitignore
# Then symlink from .claude/org-agents/agents/ instead of $ORG/agents/
```

### Syncing

```bash
git -C ~/.claude/coding-agents pull --ff-only                                          # personal layer
git -C ~/git/chasemp/mycelium-agent-framework/coding-agents pull --ff-only             # org layer
```
