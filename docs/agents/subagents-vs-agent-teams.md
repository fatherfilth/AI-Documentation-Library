---
title: "Sub-Agents vs Agent Teams in Claude Code"
status: stable
category: "agents"
slug: "subagents-vs-agent-teams"
created: 2026-02-14
updated: 2026-02-14
author: "claude"
tags: [claude-code, sub-agents, agent-teams, multi-agent, orchestration, parallel-execution]
---

# Sub-Agents vs Agent Teams in Claude Code

---

## Abstract

Claude Code offers two distinct delegation models for parallelizing AI work: sub-agents and agent teams. Sub-agents are focused workers that run within a single session, execute a task, and report results back to the parent — they cannot communicate with each other. Agent teams are an experimental feature (shipped with Opus 4.6, February 2026) that spawn multiple full Claude Code sessions capable of peer-to-peer messaging, shared task lists, and autonomous coordination. This document compares architectures, activation methods, cost profiles, and decision criteria, drawing on official Anthropic documentation and practitioner experience reports.

---

## 1. Introduction

### 1.1 Problem Statement

Developers using Claude Code frequently need to parallelize work — reviewing code from multiple angles, refactoring across layers, or investigating bugs with competing hypotheses. Claude Code provides two mechanisms for this, but the differences between sub-agents and agent teams are poorly understood. Choosing the wrong model wastes tokens, adds unnecessary complexity, or leaves coordination gaps.

### 1.2 Scope

This document covers:
- Architecture and communication models of both sub-agents and agent teams
- Step-by-step activation instructions for each
- Decision criteria for choosing between them
- Known limitations and failure modes
- Programmatic usage via the Claude Agent SDK

This document does NOT cover:
- Manual parallel sessions using git worktrees (see [claude-code-parallel-sessions](../tools/claude-code-parallel-sessions.md))
- Third-party orchestration tools (Conductor, Gas Town, OpenClaw)
- The Ralph Loop pattern (see [ralph-loop](../agents/ralph-loop.md))

### 1.3 Key Terms

| Term | Definition |
|------|------------|
| Sub-agent | A specialized AI assistant running within a single Claude Code session with its own context window, system prompt, and tool access. Reports results back to the parent only. |
| Agent team | Multiple full Claude Code sessions coordinated by a team lead, with shared task lists and peer-to-peer messaging. Experimental feature. |
| Team lead | The main Claude Code session that creates the team, spawns teammates, assigns tasks, and synthesizes results. |
| Teammate | An independent Claude Code instance spawned by the team lead. Has its own context window and can message other teammates directly. |
| Delegate mode | A mode (Shift+Tab) that restricts the team lead to coordination-only tools, forcing it to delegate rather than implement. |
| Shared task list | A file-backed task board used by agent teams with states: pending, in progress, completed. Supports dependency tracking. |

---

## 2. Background & Prior Work

Before agent teams shipped officially, the community had been building multi-agent patterns independently for months. Developers discovered feature-flagged capabilities in Claude Code's binary and built workarounds using sub-agents, bash scripts, and tools like OpenClaw and claude-sneakpeek [1]. The "30 Specialized SubAgents" series by Alireza Rezvani demonstrated sophisticated sub-agent pipelines [2]. Projects like the VoltAgent/awesome-claude-code-subagents repository cataloged 100+ specialized sub-agents across development domains [3].

Anthropic recognized the demand and shipped agent teams as a first-class feature with Claude Code 2.1.32 and the Opus 4.6 release in February 2026 [4]. The feature uses seven internal tools — TeamCreate, TeammateTool, TaskCreate, TaskUpdate, TaskList, SendMessage, and TeamClose — that Claude can call to orchestrate multi-session work [5].

Prior work on multi-agent systems in other frameworks (AutoGPT, CrewAI, LangGraph) informed the design but Claude Code's implementation is native — no external dependencies, no fragile scripts [1].

---

## 3. Methodology / Approach

### 3.1 Setup

```
Claude Code version: 2.1.32+
OS: macOS / Linux (agent teams split-pane mode requires tmux or iTerm2)
For agent teams: CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 in settings.json or environment
For sub-agents: No additional setup required
```

### 3.2 Process

1. Reviewed Anthropic's official documentation for both sub-agents and agent teams [4][6]
2. Analyzed 10+ practitioner blog posts and experience reports published between August 2025 and February 2026
3. Cross-referenced activation methods, architecture details, and limitations across sources
4. Compiled confusion points from community reports of failed configurations (delegate mode permission issues, teammate stalling, etc.)
5. Synthesized decision criteria based on cost/benefit tradeoffs documented by practitioners

---

## 4. Findings

### 4.1 Communication Architecture Is the Fundamental Difference

Sub-agents communicate in one direction only: from sub-agent back to parent. They cannot message each other, share discoveries mid-task, or coordinate without the main agent acting as intermediary. Agent teams remove this bottleneck entirely — teammates message each other via an inbox-based system, claim tasks from a shared list, and work through problems collaboratively [4].

The analogy that best captures this: sub-agents are contractors you send on separate errands. Agent teams are a project squad with a shared Kanban board and a Slack channel [7].

### 4.2 Sub-Agents Are Built-In; Agent Teams Require Explicit Activation

Sub-agents are available immediately in any Claude Code installation. Three are built in — Explore (read-only codebase search), Plan (research during plan mode), and general-purpose (complex multi-step tasks) [6]. Custom sub-agents are defined as markdown files with YAML frontmatter placed in `~/.claude/agents/` (user-level) or `.claude/agents/` (project-level), or created interactively via the `/agents` command.

Agent teams are experimental and completely hidden without the `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` flag. Three activation methods exist:

1. **settings.json** (recommended, persists): Add `{ "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }` to `~/.claude/settings.json` [8]
2. **Shell export**: `export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` in `.bashrc`/`.zshrc` [4]
3. **Per-session**: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 claude` [9]

### 4.3 Agent Teams Have Significantly Higher Token Costs

Each teammate is a full Claude Code session with its own context window. A 5-teammate team uses approximately 5x the tokens of a single session [1]. Practitioners consistently recommend the "plan first, team second" approach: use plan mode (cheap) to design the work breakdown, then hand the plan to a team for parallel execution [5].

Sub-agents, by contrast, run within the parent session's token budget and can be routed to cheaper models (e.g., `model: haiku`) for further cost savings [6].

### 4.4 Context Inheritance Differs Significantly

Sub-agents inherit the parent's permissions (with additional tool restrictions per sub-agent). Agent team teammates load project context automatically — CLAUDE.md files, MCP servers, and skills — but do NOT inherit the lead's conversation history [4]. This means the spawn prompt must contain all task-specific context. The more specific the brief, the better the output.

### 4.5 The Seven Agent Team Primitives

Agent teams are built from seven tool calls that Claude can invoke [5]:

1. **TeamCreate** — Creates team directory and config file on disk
2. **TeammateTool** — Spawns a new teammate with role-specific instructions
3. **TaskCreate** — Adds a task to the shared task list with optional dependencies
4. **TaskUpdate** — Updates task status (pending → in progress → completed)
5. **TaskList** — Reads current task states
6. **SendMessage** — Sends structured JSON to a teammate's inbox file
7. **TeamClose** — Cleans up team resources

### 4.6 Sub-Agent Definition Format

Custom sub-agents are markdown files with YAML frontmatter defining name, description, tools, optional model override, and a system prompt body [6]:

```markdown
---
name: code-reviewer
description: Expert code review specialist for security audits.
tools: Read, Grep, Glob
model: sonnet
---

You are a code review expert. When invoked:
1. Analyze the code for security issues
2. Provide structured findings with severity ratings
3. Suggest specific fixes
```

Tool scoping is critical: read-only agents get `Read, Grep, Glob`; code writers get `Read, Write, Edit, Bash, Glob, Grep`; research agents add `WebFetch, WebSearch` [3].

---

## 5. Confusion Points

| # | What I assumed | What's actually true | Impact |
|---|----------------|----------------------|--------|
| 1 | Delegate mode would restrict only the lead while leaving teammates free to work | Delegate mode passes its restrictions to teammates too — they inherit the lead's permission mode and cannot read files, run commands, or do actual work unless permissions are pre-configured [8] | High |
| 2 | Agent team teammates would have access to the lead's conversation context | Teammates load project context (CLAUDE.md, MCP servers, skills) but NOT the lead's conversation history — the spawn prompt is the only way to pass task-specific context [4] | High |
| 3 | Agent teams would be a faster version of sub-agents — same pattern, just parallel | Agent teams are architecturally different — peer-to-peer communication, shared task lists, inbox-based messaging. They solve different problems than sub-agents [4] | Medium |
| 4 | `/resume` and `/rewind` would work with agent team sessions | These commands do not restore in-process teammates. The lead may try to message teammates that no longer exist. Fresh sessions only [4] | Medium |
| 5 | Two teammates could safely edit the same file | No built-in file locking. Two teammates writing to the same file results in last-write-wins. Tasks must be structured so each teammate owns different files [9] | High |
| 6 | Setting the env var at the root of settings.json would work | The flag must be in the `env` block inside settings.json, not at the root level [9] | Medium |

---

## 6. Failure Modes & Gotchas

| # | Trigger | Symptom | Fix | Severity |
|---|---------|---------|-----|----------|
| 1 | Enabling agent teams without pre-approving permissions | Teammates stall on permission prompts with nobody to approve them | Add common operations to permission allow-lists in settings before spawning teammates [8] | Critical |
| 2 | Lead implements instead of delegating | Team lead does the work itself instead of assigning to teammates | Tell it explicitly to wait, or use delegate mode (with pre-configured permissions) [1] | High |
| 3 | Spawning too many teammates for a simple task | Token costs explode with no coordination benefit | Start with sub-agents for focused tasks; only use teams when workers genuinely need to coordinate [5] | High |
| 4 | tmux session persists after team ends | Orphaned tmux sessions consuming resources | List sessions with `tmux ls` and kill the team's session manually [4] | Medium |
| 5 | Sub-agent prompt too long on Windows | Subagents with very long prompts fail due to 8191 char command line limit | Keep prompts concise or use filesystem-based agents for complex instructions [10] | Medium |
| 6 | Sub-agent tries to spawn another sub-agent | Fails — sub-agents cannot spawn other sub-agents (prevents infinite nesting) | Design pipelines where the parent orchestrates all sub-agent calls [6] | Low |

---

## 7. Analysis

### 7.1 Strengths

**Sub-agents** excel at cost-effective specialization. They keep exploration and implementation out of the main conversation, enforce tool constraints per task, and are reusable across projects. The built-in Explore agent alone saves significant context by running codebase searches in an isolated window.

**Agent teams** shine when parallel workers need to coordinate. The shared task list with dependency tracking, peer-to-peer messaging, and autonomous work assignment create a genuine collaborative dynamic. Practitioners report that the "debate" pattern — spawning teammates to investigate competing hypotheses and actively try to disprove each other — produces higher-quality root cause analysis than sequential investigation [2].

### 7.2 Limitations

**Sub-agents** cannot communicate with each other. For cross-domain work (frontend changes that depend on backend API changes), the parent becomes a bottleneck, manually relaying information between sub-agents.

**Agent teams** are experimental with known limitations: no session resumption, no file locking, high token cost, and rough edges around teammate lifecycle management. The lead sometimes finishes before all tasks are complete, requiring manual nudging.

### 7.3 Comparison to Alternatives

| Alternative | Key difference | When to prefer it |
|-------------|----------------|--------------------||
| Single Claude Code session | No delegation overhead, full context continuity | Simple tasks, same-file edits, sequential work |
| Manual parallel sessions (git worktrees) | Human coordinates, no automated task list or messaging | When you want full control and agent teams feel too experimental |
| Third-party orchestration (Conductor, CompoundEng) | Plugin-based workflow with plan → work → review cycles | When you want opinionated structure beyond raw agent teams |
| Ralph Loop (bash-driven) | Fresh context per iteration, backpressure-driven | Autonomous long-running tasks where simplicity beats coordination |

---

## 8. Recommendations

### 8.1 Decision Checklist

- [ ] Does the task require workers to communicate with each other? → If yes, use agent teams
- [ ] Is the task focused and independent (research, review, single-domain)? → If yes, use sub-agents
- [ ] Is cost sensitivity high? → If yes, prefer sub-agents with `model: haiku`
- [ ] Do you need competing perspectives or debate? → If yes, use agent teams
- [ ] Is the work sequential with clear handoff points? → If yes, use sub-agent pipelines

> If any box is unchecked, see [Comparison to Alternatives](#73-comparison-to-alternatives).

### 8.2 Next Steps

- Test agent teams on a read-only task first (code review from 3 angles) before committing to parallelized implementation
- Build a library of reusable sub-agent definitions in `~/.claude/agents/` for common workflows
- Explore the Claude Agent SDK for programmatic agent team orchestration via Python [10]
- Monitor the experimental flag — agent teams will likely become stable and enabled by default in a future release
- Investigate the Compound Engineering Plugin for structured plan → work → review → compound cycles with agent teams [1]

---

## 9. Open Questions & Unknowns

- Will Anthropic add built-in file locking for agent teams to prevent concurrent write conflicts?
- What is the optimal number of teammates before coordination overhead exceeds parallelism benefits? (Community reports suggest 3-5 is the sweet spot, but no formal benchmarks exist)
- Will delegate mode be fixed to allow the lead to be restricted while leaving teammates unrestricted?
- How will agent teams interact with upcoming features like server-side compaction and adaptive thinking?
- Will `/resume` and `/rewind` support for agent teams be added in a future release?
- What is the exact token cost multiplier per teammate when factoring in coordination overhead (task list reads, message passing)?

---

## References

1. [Addy Osmani — Claude Code Swarms](https://addyosmani.com/blog/claude-code-agent-teams/)
2. [Alireza Rezvani — From Subagents to Agent Teams: Claude Code's Multi-Agent Leap](https://alirezarezvani.medium.com/from-subagents-to-agent-teams-claude-codes-multi-agent-leap-and-what-i-actually-change-97edf83a4d5e)
3. [VoltAgent/awesome-claude-code-subagents — GitHub](https://github.com/VoltAgent/awesome-claude-code-subagents)
4. [Anthropic — Orchestrate teams of Claude Code sessions (Official Docs)](https://code.claude.com/docs/en/agent-teams)
5. [alexop.dev — From Tasks to Swarms: Agent Teams in Claude Code](https://alexop.dev/posts/from-tasks-to-swarms-agent-teams-in-claude-code/)
6. [Anthropic — Create custom subagents (Official Docs)](https://code.claude.com/docs/en/sub-agents)
7. [claudefa.st — Claude Code Agent Teams: Multi-Session Orchestration](https://claudefa.st/blog/guide/agents/agent-teams)
8. [Scott Spence — Enable Team Mode in Claude Code](https://scottspence.com/posts/enable-team-mode-in-claude-code)
9. [marc0.dev — Claude Code Agent Teams: Multiple AI Agents, One Repo](https://www.marc0.dev/en/blog/claude-code-agent-teams-multiple-ai-agents-working-in-parallel-setup-guide-1770317684454)
10. [Anthropic — Subagents in the SDK (Official Docs)](https://platform.claude.com/docs/en/agent-sdk/subagents)

---

## Appendix

### A. Raw Data / Configs

**Minimal settings.json for agent teams:**

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

**Example sub-agent definition (data-scientist.md):**

```markdown
---
name: data-scientist
description: Data analysis expert for SQL queries, BigQuery operations, and data insights.
tools: Bash, Read, Write
model: sonnet
---

You are a data scientist specializing in SQL and BigQuery analysis.
When invoked:
1. Understand the data analysis requirement
2. Write efficient SQL queries
3. Analyze and summarize results
4. Present findings clearly
```

**Example agent team prompt:**

```
Create an agent team to refactor the auth module.
Spawn three teammates:
- one for the API layer
- one for database migrations
- one for test coverage
Have them coordinate through the shared task list.
```

### B. Related Documents

- [claude-code-parallel-sessions](../tools/claude-code-parallel-sessions.md) — Running multiple Claude Code instances manually using terminal tabs and git worktrees
- [ralph-loop](../agents/ralph-loop.md) — Autonomous AI coding pattern with fresh context per iteration
- [multi-agent-design-generation](../agents/multi-agent-design-generation.md) — Deploying parallel Claude Code Agent Teams for frontend design generation

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial research article | claude |
