# Agents

A collection of reusable AI agent personas designed for software engineering workflows with Claude Code.

## Goals

Modern AI coding agents benefit from well-crafted personas that shape how they reason, communicate, and approach work. However, a single monolithic persona creates problems: instructions optimized for implementation conflict with those needed for strategic advising, wasted context on irrelevant instructions dilutes agent performance, and maintenance becomes difficult as the persona grows.

This project solves that with a **hierarchical separation of concerns**:

1. **Foundation layer** — A shared behavioral core loaded globally. Encodes engineering identity, communication principles, and decision-making heuristics that apply regardless of task type.
2. **Specialized personas** — Focused, composable personas activated on demand via Claude Code skills. Each persona inherits the foundation and adds mode-specific instructions without conflicting defaults.

### Design Principles

- **Composable over monolithic.** Each persona is focused and unconditional — no "if advising, do X; if implementing, do Y" branching.
- **Principles over mechanics.** The foundation layer describes *how to think*. Project-level CLAUDE.md files describe *what to know*. Personas describe *how to operate*.
- **Context-efficient.** Only load instructions relevant to the current mode. A strategic advising session doesn't need TDD cycle details; an implementation session doesn't need roadmap prioritization frameworks.
- **Maintainable.** Shared behavior lives in one place. Persona-specific behavior lives in the persona. No duplication.

## Personas

| Persona | Purpose | Activation |
|---|---|---|
| **Foundation** | Shared engineering identity, communication, decision-making | Global `~/.claude/CLAUDE.md` (always loaded) |
| **Implementer** | Hands-on coding partner for active development | `/implementer` skill |
| **Advisor** | Strategic technical thinking and analysis partner | `/advisor` skill |

## Architecture

```
~/.claude/CLAUDE.md                  <- Foundation (always active)
~/.claude/skills/implementer.md      <- Implementation partner skill
~/.claude/skills/advisor.md          <- Technical advisor skill
./CLAUDE.md (per-project)            <- Project-specific context
```

The foundation provides behavioral DNA that both personas inherit. Project-level CLAUDE.md files supply tech stack, structure, commands, and domain context. Skills activate the appropriate operational mode.

## Status

- [ ] Foundation persona — draft
- [ ] Implementer persona — draft
- [ ] Advisor persona — draft
- [ ] Skill integration and activation testing
- [ ] Validation across multiple project types
