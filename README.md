# Engineering Personas

A Claude Code plugin providing reusable engineering personas as slash commands.

## Goals

Modern AI coding agents benefit from well-crafted personas that shape how they reason, communicate, and approach work. However, a single monolithic persona creates problems: instructions optimized for implementation conflict with those needed for strategic advising, wasted context on irrelevant instructions dilutes agent performance, and maintenance becomes difficult as the persona grows.

This project solves that with a **hierarchical separation of concerns**:

1. **Foundation layer** — A shared behavioral core loaded globally via `~/.claude/CLAUDE.md`. Encodes engineering identity, communication principles, and decision-making heuristics that apply regardless of task type.
2. **Specialized personas** — Focused, composable personas activated on demand via Claude Code commands. Each persona inherits the foundation and adds mode-specific instructions without conflicting defaults.

### Design Principles

- **Composable over monolithic.** Each persona is focused and unconditional — no "if advising, do X; if implementing, do Y" branching.
- **Principles over mechanics.** The foundation layer describes *how to think*. Project-level CLAUDE.md files describe *what to know*. Personas describe *how to operate*.
- **Context-efficient.** Only load instructions relevant to the current mode. A strategic advising session doesn't need TDD cycle details; an implementation session doesn't need roadmap prioritization frameworks.
- **Maintainable.** Shared behavior lives in one place. Persona-specific behavior lives in the persona. No duplication.

## Personas

| Persona | Purpose | Activation |
|---|---|---|
| **Foundation** | Shared engineering identity, communication, decision-making | Global `~/.claude/CLAUDE.md` (always loaded) |
| **Implementer** | Hands-on coding partner for active development | `/implementer [task]` |
| **Advisor** | Strategic technical thinking and analysis partner | `/advisor [question]` |

## Architecture

This repo is structured as a Claude Code plugin:

```
.claude-plugin/
└── plugin.json               # Plugin manifest
commands/
├── implementer.md            # /implementer command (self-contained persona + task)
└── advisor.md                # /advisor command (self-contained persona + task)
foundation.md                 # Global CLAUDE.md content (copy to ~/.claude/CLAUDE.md)
```

### How it works

1. **Foundation** (`foundation.md`) — Copy this content into `~/.claude/CLAUDE.md`. It loads into every conversation and provides shared behavioral DNA: communication style, decision-making principles, and engineering identity.

2. **Commands** (`commands/implementer.md`, `commands/advisor.md`) — Self-contained persona definitions with frontmatter for Claude Code's plugin system. Each file includes the full behavioral instructions and passes through `$ARGUMENTS`. When you invoke `/implementer Fix the auth bug`, Claude receives the implementer persona instructions plus your task.

3. **Project CLAUDE.md** (per-project, not in this repo) — Each project's own `CLAUDE.md` supplies tech stack, structure, commands, and domain context. This layer is independent of the personas.

### Installation

Install the plugin by adding it to your Claude Code settings:

```bash
claude plugins add ~/workspace/agents
```

Then copy the foundation persona to your global config:

```bash
cp foundation.md ~/.claude/CLAUDE.md
```

## Usage

```
/implementer Fix the race condition in the session middleware
/implementer Add pagination to the user list endpoint
/advisor Help me understand the data flow in this codebase
/advisor What should we prioritize for the next sprint?
```

## Status

- [x] Foundation persona — draft
- [x] Implementer persona — draft
- [x] Advisor persona — draft
- [x] Plugin structure and command wiring
- [ ] Validation across multiple project types
- [ ] Iteration based on real usage
