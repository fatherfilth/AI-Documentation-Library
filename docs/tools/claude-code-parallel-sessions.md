---
title: "Running Multiple Claude Code Instances in Parallel"
status: stable
category: "tools"
slug: "claude-code-parallel-sessions"
created: 2026-02-14
updated: 2026-02-14
author: "claude"
tags: [claude-code, parallel, agent-teams, git-worktrees, multi-agent, tmux]
---

# Running Multiple Claude Code Instances in Parallel

---

## Abstract

Claude's desktop app processes one response at a time, but Claude Code in the terminal supports genuinely parallel sessions. This document covers three approaches to running multiple Claude Code instances on the same project: same-directory with task separation, git worktrees for branch isolation, and the experimental Agent Teams feature for coordinated multi-agent work. Agent Teams, enabled with a single setting flag, allow a lead session to spawn teammates that communicate directly, share a task list, and self-coordinate — representing a shift from sequential to multi-threaded AI development.

---

## 1. Introduction

### 1.1 Problem Statement

Users of Claude's desktop app discover they cannot run multiple AI tasks simultaneously. The desktop interface is sequential — one message in, one response out. For developers who want Claude working on multiple parts of a project at once (backend, frontend, tests), this is a significant productivity bottleneck.

### 1.2 Scope

This document covers three parallel execution strategies for Claude Code: same-directory task splitting, git worktree isolation, and Agent Teams. It does NOT cover API-level parallelism, third-party orchestrators (Conductor, Claude Squad), or Docker-based isolation setups.

### 1.3 Key Terms

| Term | Definition |
|------|------------|
| Git worktree | A Git feature that lets you check out multiple branches simultaneously in separate directories, sharing the same repository history |
| Agent Teams | An experimental Claude Code feature that coordinates multiple instances with shared task lists and inter-agent messaging |
| Team Lead | The main Claude Code session in an Agent Teams setup that creates the team, assigns tasks, and synthesizes results |
| Teammate | A separate Claude Code instance spawned by the team lead, working independently in its own context window |
| tmux | A terminal multiplexer that lets you run multiple terminal sessions in split panes within a single window |
| Subagent | A lightweight helper agent within a single Claude Code session (contrast with Agent Teams teammates which are full independent instances) |

---

## 2. Background & Prior Work

Before Agent Teams shipped as a native feature in February 2026 alongside Opus 4.6, developers used several workarounds for parallel Claude Code sessions. The community built tools like Claude Squad (tmux-based), ccswitch (worktree management CLI), and Vibe Kanban (kanban UI overlay) [1]. GitButler integrated Claude Code lifecycle hooks to automatically sort simultaneous sessions into separate branches [2]. These approaches all shared a common limitation: no coordination between instances.

Anthropic's own documentation recommended git worktrees as the standard approach, keeping instances isolated on separate branches to avoid file conflicts [3]. The Agent Teams feature formalized multi-agent coordination at the tool level, adding shared task lists, inter-agent messaging, and dependency tracking [4].

---

## 3. Methodology / Approach

### 3.1 Setup

```
Prerequisites:
- Claude Code installed (npm install -g @anthropic-ai/claude-code)
- Git initialized in your project
- For Agent Teams: enable experimental flag
- For split panes: tmux or iTerm2 installed

Enable Agent Teams:
claude config set -g experimental.agentTeams true
# OR
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1

Install tmux (optional, for split-pane visibility):
brew install tmux     # Mac
sudo apt install tmux # Ubuntu/Debian
```

### 3.2 Process

**Approach 1: Same directory, multiple terminals**

Open separate terminal tabs, `cd` into the same project, run `claude` in each. Assign each instance to work on different files. No isolation — if two instances edit the same file, conflicts will occur.

**Approach 2: Git worktrees**

```bash
# Create isolated workspaces sharing the same repo
git worktree add -b feature-auth ../my-project-auth main
git worktree add -b feature-tests ../my-project-tests main

# Launch Claude Code in each
cd ../my-project-auth && claude   # "Add login flow"
cd ../my-project-tests && claude  # "Write unit tests"

# Clean up when done
git worktree remove ../my-project-auth && git branch -D feature-auth
```

**Approach 3: Agent Teams**

```bash
tmux          # Start tmux session for split panes
claude        # Launch Claude Code

# Then in the Claude session, describe your team:
> I'm building an e-commerce checkout flow. Create an agent team:
> - One teammate for the backend API routes
> - One teammate for the React frontend components
> - One teammate writing tests for both
```

Claude creates the team, spawns teammates, and coordinates via a shared task list.

---

## 4. Findings

### 4.1 The Desktop App Is Architecturally Sequential; the Terminal Enables True Parallelism

Claude's desktop and web interfaces allow multiple conversations (tabs), but Claude can only actively generate one response at a time per account. Claude Code sessions are independent processes — each is a full Claude instance with its own context window. Running three terminal tabs with `claude` in each means three genuinely concurrent AI workers.

### 4.2 Git Worktrees Are the Recommended Middle Ground

Anthropic recommends worktrees over same-directory parallelism because each instance gets its own branch and working copy while sharing the same Git history. This eliminates the primary failure mode (two instances editing the same file) while being simpler than Agent Teams. The merge step is standard Git — the same workflow human teams use.

### 4.3 Agent Teams Add Coordination, Not Just Parallelism

The key differentiator of Agent Teams over manual parallel sessions is inter-agent communication. Teammates can message each other directly, share discoveries, challenge assumptions, and coordinate through a shared task list with dependency tracking. This enables patterns like adversarial debugging (multiple agents testing competing hypotheses) and multi-perspective code review that pure parallelism cannot achieve [4].

### 4.4 Agent Teams Cost Roughly 5x Tokens Per Teammate

Each teammate is a full Claude instance with its own context window. A 5-person team uses approximately 5 times the tokens of a single session. This cost is only justified for tasks where parallel exploration adds real value — code reviews, debugging with competing hypotheses, multi-module features. For sequential tasks or same-file edits, a single session is more effective.

### 4.5 Split-Pane Mode Requires tmux or iTerm2

Agent Teams can run in-process (all teammates share one terminal, navigated with Shift+Up/Down) or in split-pane mode (each teammate visible simultaneously). Split-pane mode requires tmux or iTerm2 and does NOT work in VS Code's integrated terminal, Windows Terminal, or Ghostty.

---

## 5. Confusion Points

| # | What I assumed | What's actually true | Impact |
|---|----------------|----------------------|--------|
| 1 | Claude's desktop app can run multiple tasks simultaneously | The desktop app is sequential; only the terminal (Claude Code) supports true parallel sessions | High |
| 2 | Running multiple Claude Code instances in the same folder is safe | It works but risks file conflicts if two instances edit the same file; git worktrees or Agent Teams are safer | High |
| 3 | Agent Teams is just "multiple terminals" with a fancy name | Agent Teams adds a coordination layer: shared task lists, inter-agent messaging, dependency tracking, and a lead/teammate hierarchy | Medium |
| 4 | You need complex configuration files or YAML to set up Agent Teams | You enable one flag and describe the team in natural language; Claude handles all orchestration | Medium |

---

## 6. Failure Modes & Gotchas

| # | Trigger | Symptom | Fix | Severity |
|---|---------|---------|-----|----------|
| 1 | Two instances edit the same file in same-directory mode | Conflicting changes, data loss | Use git worktrees or assign instances to strictly separate files | Critical |
| 2 | Agent Teams teammates request too many permissions | Constant interruptions to approve file writes | Pre-approve common operations in permission settings before spawning teammates | High |
| 3 | Using split-pane mode in VS Code integrated terminal | Panes don't appear; falls back to in-process mode | Use a standalone terminal with tmux or iTerm2 | Medium |
| 4 | Agent team lead declares work "finished" prematurely | Tasks left incomplete | Tell the lead to keep going or to wait for all teammates to finish before proceeding | Medium |
| 5 | tmux session persists after team ends | Orphaned tmux sessions consuming resources | Run `tmux ls` to list sessions and `tmux kill-session -t <name>` to clean up | Low |

---

## 7. Analysis

### 7.1 Strengths

The three-tier approach (same-directory → worktrees → Agent Teams) provides a natural progression path. Users can start with the simplest method and upgrade as their needs grow. Agent Teams' natural-language interface for defining teams removes the usual boilerplate of multi-agent orchestration systems. The shared task list and messaging system enable collaboration patterns that previously required custom tooling.

### 7.2 Limitations

Agent Teams is experimental with known limitations around session resumption, task coordination, and shutdown behavior. Token cost scales linearly with team size. The feature requires the latest Claude Code version and is not available in the desktop app's embedded Claude Code sessions with the same level of control. Complex tasks with heavy interdependencies between files still work better in a single session.

### 7.3 Comparison to Alternatives

| Alternative | Key difference | When to prefer it |
|-------------|----------------|--------------------|
| Single Claude Code session | No parallelism but no coordination overhead | Sequential tasks, same-file edits, simple changes |
| Subagents | Run within a single session, report back to main agent only | Focused tasks that don't need inter-agent coordination |
| Git worktrees (manual) | Full isolation, no coordination | When you want to manage parallel work yourself without automated coordination |
| Third-party tools (Claude Squad, ccswitch) | Community-built wrappers with UI or management features | When you need specific UX features that Agent Teams doesn't provide |

---

## 8. Recommendations

### 8.1 Decision Checklist

- [ ] You have multiple independent tasks that can run concurrently
- [ ] The tasks touch different files or different parts of the codebase
- [ ] You're comfortable with the token cost (~5x per teammate for Agent Teams)
- [ ] You have tmux or iTerm2 installed (for split-pane visibility with Agent Teams)

> If any box is unchecked, see [Comparison to Alternatives](#73-comparison-to-alternatives).

### 8.2 Next Steps

1. Start with git worktrees for your next multi-task session
2. Try Agent Teams with a read-only task first (e.g., three-perspective code review)
3. Scale up to parallel implementation once comfortable with coordination patterns
4. Explore pre-approving permissions to reduce friction in Agent Teams workflows

---

## 9. Open Questions & Unknowns

- When will Agent Teams move from experimental to stable? No timeline announced.
- How does Agent Teams token cost compare to equivalent work done sequentially? No published benchmarks on total-cost-to-completion.
- Can Agent Teams work across remote and local sessions simultaneously? The desktop app embeds Claude Code sessions but the interaction with Agent Teams is not well documented.

---

## References

1. [GitButler Blog — Managing Multiple Claude Code Sessions Without Worktrees](https://blog.gitbutler.com/parallel-claude-code)
2. [Addy Osmani — Claude Code Swarms](https://addyosmani.com/blog/claude-code-agent-teams/)
3. [Anthropic — Claude Code Agent Teams Documentation](https://code.claude.com/docs/en/agent-teams)
4. [Anthropic Engineering — Building a C Compiler with Parallel Claudes](https://www.anthropic.com/engineering/building-c-compiler)
5. [Serenities AI — Claude Code Agent Teams Setup Guide](https://serenitiesai.com/articles/claude-code-agent-teams-documentation)

---

## Appendix

### A. Raw Data / Configs

**Minimal Agent Teams settings.json entry:**

```json
{
  "experimental": {
    "agentTeams": true
  }
}
```

**Git worktree quick-start:**

```bash
# Create worktree
git worktree add -b <branch-name> ../<folder-name> main

# Launch Claude Code in it
cd ../<folder-name> && claude

# Clean up
git worktree remove ../<folder-name> && git branch -D <branch-name>
```

### B. Related Documents

- [terminal-vs-desktop-for-ai](../skills/terminal-vs-desktop-for-ai.md) — created from the same conversation
- [claude-code-project-setup](../tools/claude-code-project-setup.md) — created from the same conversation

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial research article | claude |