---
topic: cleo-memory-and-statusline
category: tools
date: 2026-02-15
participants:
  - user
  - claude
source: claude.ai
summary: "How to use CLEO for persistent AI agent memory and how to show context window usage in Claude Code terminal via /statusline"
related_topics: []
---

# Transcript: CLEO Memory System & Claude Code Statusline

## Topic: Using CLEO for Memory Persistence

**User:** How to use CLEO (repo from kryptobaseddev) to save memory and how to show memory/context for Claude in the terminal — is it /statusline?

**Claude:** Researched the `kryptobaseddev/cleo` repository (v0.98.3), the statusline setup library (`lib/session/statusline-setup.sh`), the context monitor script (`lib/session/context-monitor.sh`), the context safeguard guide (`docs/guides/context-safeguard.mdx`), and Claude Code's official `/statusline` documentation.

### Key Findings from Research

**CLEO's memory model:** CLEO persists agent state across sessions using structured JSON files in `.cleo/` — `todo.json` for active tasks, `todo-archive.json` for completed tasks, and `todo-log.json` as an append-only audit trail. The session protocol (`cleo session start/end`) creates checkpoints that Claude can read on resume via `cleo dash` and `cleo focus show`.

**The `/statusline` command:** Confirmed that `/statusline` is a built-in Claude Code slash command that generates a bash script for a persistent terminal status bar. It receives JSON via stdin from Claude Code on every turn, including `context_window.used_percentage`, model info, costs, and workspace data.

**CLEO's statusline integration:** CLEO provides its own context monitor script at `~/.cleo/lib/session/context-monitor.sh` that extends the basic statusline by writing a `.context-state.json` file. This enables CLEO commands to react to context thresholds (warning at 70%, caution at 85%, critical at 90%, emergency at 95%) with automatic alerts.

**Setup path:** Either use Claude Code's built-in `/statusline` for a quick context display, or install CLEO's context-aware statusline via `cleo upgrade --setup-statusline` for threshold-based alerts integrated with CLEO's session system.

### User Confusion Points Surfaced

1. User wasn't sure if `/statusline` was the right command — it is the correct Claude Code slash command for terminal status bars.
2. The relationship between CLEO's memory system (task/session persistence) and the statusline (context window monitoring) are related but separate features that CLEO bridges.
