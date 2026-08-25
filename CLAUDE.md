# engineering-agents

A Claude Code plugin that packages focused engineering personas as on-demand slash commands.

## What this repo is

This is a plugin repo, not a project repo. It defines reusable personas that can be installed across projects. It does not have a build system, tests, or application code.

## Available personas

| Command | Purpose |
|---------|---------|
| `/engineering-agents:advisor` | Strategic analysis, architecture review, decision-making |
| `/engineering-agents:implementer` | Pair programming with plan-first, TDD workflow |
| `/engineering-agents:recruiter` | Recruiter email drafting |
| `/engineering-agents:interview` | FAANG interview coaching (mock, guided, review, stress test) |
| `/engineering-agents:dsa-tutor` | Socratic DSA coaching for senior/staff prep |
| `/engineering-agents:prompt-architect` | Audit and tighten a repo instruction file (AGENTS.md / CLAUDE.md) |

## Structure

```
.claude/commands/   ← slim activation stubs (frontmatter + @import + $ARGUMENTS)
.claude/skills/     ← full persona instructions (loaded on invocation)
.gemini/skills/     ← synced Gemini CLI equivalents
.claude-plugin/     ← plugin manifest
foundation.md       ← global CLAUDE.md content (copy to ~/.claude/CLAUDE.md)
```

## Installation

```bash
claude plugins add /path/to/this/repo
cp foundation.md ~/.claude/CLAUDE.md
```
