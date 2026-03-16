# Mycelium Agents

Framework-specific Claude Code agents for the Mycelium Agent Framework.

These agents are distributed to ring repos via `make setup` (copied to `.claude/`).

## Agents

- `spore-validator` — Validates spore JSONL files against schema before operations
- `ring-inspector` — Inspects ring state, manifest, and memory integrity

## Usage

These agents are consumed by ring repos. The `Makefile` in each ring copies them into `.claude/agents/` alongside the base AlpheusCEF agents.
