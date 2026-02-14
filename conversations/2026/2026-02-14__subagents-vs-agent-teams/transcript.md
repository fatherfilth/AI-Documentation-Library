---
topic: subagents-vs-agent-teams
category: agents
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "Comprehensive comparison of Claude Code sub-agents vs agent teams — architecture differences, activation methods, decision criteria, and known gotchas."
related_topics: []
---

# Transcript: Sub-Agents vs Agent Teams in Claude Code

## Opening Question

**User:** Sub agents vs Agent teams via claude and how to activate

## Research & Exploration

Claude conducted extensive research across Anthropic's official documentation, community blog posts, and practitioner write-ups to compile a comprehensive comparison.

### Sources Consulted

- Anthropic official docs: Claude Code sub-agents guide, agent teams guide
- Addy Osmani's "Claude Code Swarms" write-up
- alexop.dev "From Tasks to Swarms: Agent Teams in Claude Code"
- Scott Spence "Enable Team Mode in Claude Code"
- marc0.dev "Claude Code Agent Teams: Multiple AI Agents, One Repo"
- claudefa.st agent teams guide
- Medium articles from Alireza Rezvani, Yee Fei, Isaac Kargar, David Haberlah, Dára Sobaloju
- PubNub "Best practices for Claude Code sub-agents"
- VoltAgent/awesome-claude-code-subagents repository
- Shipyard subagents quickstart guide

## Core Findings

### Sub-Agents — The Focused Worker Model

Sub-agents are specialized AI assistants that run within a single Claude Code session. Each gets its own context window, custom system prompt, specific tool access, and independent permissions. When Claude encounters a matching task, it delegates to the subagent, which works independently and returns results.

Key characteristics:
- Built into Claude Code, no activation needed
- Three built-in subagents: Explore (read-only codebase search), Plan (research during plan mode), general-purpose (complex multi-step tasks)
- Custom subagents defined as markdown files with YAML frontmatter in `~/.claude/agents/` or `.claude/agents/`
- Can also be created via the `/agents` interactive command
- Communication is one-way: subagent → parent only
- Subagents cannot spawn other subagents (prevents infinite nesting)
- Model can be overridden per subagent (e.g., `model: haiku` for cost savings)

### Agent Teams — The Collaborative Multi-Session Model

Agent teams coordinate multiple full Claude Code instances working together. One session acts as team lead, spawning teammates that communicate directly via shared task lists and an inbox-based messaging system.

Key characteristics:
- Experimental feature, disabled by default
- Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` flag
- Each teammate is a separate full Claude Code session with its own context window
- Teammates load project context (CLAUDE.md, MCP servers, skills) but NOT the lead's conversation history
- Communication is peer-to-peer: teammates message each other directly
- Shared task list with three states: pending, in progress, completed
- Task dependencies are tracked; blocked work unblocks automatically
- Display modes: in-process, split panes (tmux/iTerm2), auto

### Activation Methods

**Sub-agents:**
- Always available, no flag needed
- Create via `/agents` command or by placing markdown files in agents directories

**Agent Teams:**
- Option 1: `~/.claude/settings.json` → `{ "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }`
- Option 2: Shell export → `export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`
- Option 3: Per-session → `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 claude`

### Confusion Points Surfaced

1. Delegate mode passes restrictions to teammates — they inherit the lead's permission mode and can't do actual work unless permissions are pre-configured
2. Teammates don't inherit conversation history — only project context files load automatically
3. No file locking between teammates — last write wins on concurrent file edits
4. Agent teams burn ~Nx tokens where N = number of teammates
5. The lead sometimes implements instead of delegating — needs explicit instruction or delegate mode
6. `/resume` and `/rewind` don't restore in-process teammates — fresh sessions only

### Decision Criteria

Use sub-agents when:
- Tasks are focused and independent
- Workers don't need to communicate with each other
- Cost sensitivity is high
- Sequential pipeline is sufficient (PM → architect → implementer)

Use agent teams when:
- Workers need to share findings and challenge each other
- Cross-domain coordination is required (frontend + backend + tests)
- Bug investigation benefits from competing hypotheses
- QA needs multiple simultaneous review perspectives

Best practice: Plan first with plan mode (cheap), then hand plan to team for parallel execution.
