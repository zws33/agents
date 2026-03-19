# Engineering Agents

A Claude Code plugin that applies separation of concerns to AI agent prompting — splitting a monolithic system prompt into a shared foundation layer and specialized, on-demand personas.

## Motivation

As I integrated Claude Code into my daily workflow across multiple projects, I ran into a problem: the single `CLAUDE.md` system prompt was trying to do too much. Instructions optimized for implementation (TDD cycles, commit discipline, plan-before-code) actively conflicted with what I needed during strategic advising (exploratory thinking, decision frameworks, don't-write-code-unless-asked). Stuffing both into one prompt meant the agent was always carrying irrelevant context, and the competing instructions diluted its effectiveness in both modes.

To fix this I applied to a separation of concerns. Factor out the shared behavioral core into a foundation layer that's always loaded, and extract the mode-specific instructions into focused personas that are activated on demand. Each persona inherits the foundation and adds its own instructions without conflicting with the others.

## Design Decisions

**Why personas over a single adaptive prompt?**
A monolithic prompt that says "if advising, do X; if implementing, do Y" forces the agent to carry all instructions all the time and route between them implicitly. In practice, this led to mode bleed — advisory sessions would produce unsolicited code, implementation sessions would over-deliberate instead of building. Explicit persona activation eliminates the ambiguity: the agent knows exactly what mode it's in because it only has one set of instructions loaded.

**Why a plugin architecture?**
Claude Code's plugin system provides slash commands that activate personas with a single invocation. The alternative — maintaining multiple `CLAUDE.md` files and swapping them manually — is friction that kills adoption. The plugin structure means I can type `/advisor` or `/implementer` mid-session and the agent shifts modes cleanly. It also makes this shareable: anyone can install the plugin and get the same workflow.

**Why these two personas specifically?**
The advisor/implementer split maps to the two fundamental modes of engineering work: _thinking_ and _building_. The advisor persona is optimized for when I need to understand, evaluate, or decide — codebase ramp-ups, architecture reviews, tech debt assessment, prioritization. The implementer persona is optimized for when I need to ship — structured planning, TDD, disciplined commits. The boundary is clean because the outputs are different: analysis vs. code.

**Why a separate foundation layer?**
Some behaviors should be consistent regardless of mode — communication style, decision-making principles, how the agent handles uncertainty. Duplicating these across personas would create a maintenance problem and risk drift. The foundation lives in `~/.claude/CLAUDE.md` (always loaded) and encodes the shared engineering identity. Personas build on top of it.

## Personas

| Persona         | Purpose                                                     | Activation                                   |
| --------------- | ----------------------------------------------------------- | -------------------------------------------- |
| **Foundation**  | Shared engineering identity, communication, decision-making | Global `~/.claude/CLAUDE.md` (always loaded) |
| **Implementer** | Hands-on coding partner for active development              | `/implementer [task]`                        |
| **Advisor**     | Strategic technical thinking and analysis partner           | `/advisor [question]`                        |

### Foundation

The behavioral core that loads into every conversation. Defines the agent's engineering identity (Staff+ engineer, design partner, not a code generator), communication principles (lead with context, be specific about trade-offs, name your assumptions), and decision-making heuristics (surface trade-offs, push back on over-engineering, distinguish reversible from irreversible decisions).

### Implementer

A pair programmer that follows a strict workflow: clarify the task, research the codebase (read-only), propose a plan for approval, then implement step by step. Enforces TDD (red-green-refactor), requires plan approval before writing code, and maintains disciplined commit hygiene. Optimized for producing correct, well-tested code with minimal rework.

### Advisor

A design partner and strategic advisor whose default output is analysis and recommendations, not code. Covers codebase ramp-up, strategic advising, architecture review, technology evaluation, code review, and decision documentation. Only writes code when explicitly asked. Optimized for helping me understand, evaluate, and decide.

## How I Use This

A typical workflow looks like this:

**Ramping up on a new codebase:** I start with `/advisor Help me understand the architecture of this project`. The advisor explores systematically — entry points, data flow, key abstractions — and produces a structured summary. I follow up with targeted questions until I have a working mental model.

**Planning a feature:** I use `/advisor` to think through the approach — what are the options, what are the trade-offs, what should we build first. Once I have a plan I'm confident in, I switch to `/implementer` with the specific task. The implementer proposes a concrete implementation plan, waits for my approval, then executes it with tests.

**Reviewing and refactoring:** `/advisor` for code review and identifying what to change. `/implementer` for making the changes with proper test coverage and clean commits.

The key pattern is: **think first, then build**. The advisor helps me make better decisions; the implementer helps me execute them cleanly.

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
claude plugins add /path/to/this/repo
```

Then copy the foundation persona to your global config:

```bash
cp foundation.md ~/.claude/CLAUDE.md
```

## Roadmap

- Validate personas across more project types and team contexts
- Iterate on persona instructions based on real usage patterns
- Explore additional personas (e.g., a reviewer persona, a debugging specialist)

## License

[MIT](LICENSE)
