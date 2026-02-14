---
title: "Get Shit Done (GSD) — Meta-Prompting System for Claude Code"
status: stable
category: "tools"
slug: "get-shit-done-gsd"
created: 2026-02-14
updated: 2026-02-14
author: "claude"
tags: [gsd, claude-code, spec-driven-development, meta-prompting, context-engineering]
---

# Get Shit Done (GSD) — Meta-Prompting System for Claude Code

---

## Abstract

Get Shit Done (GSD) is a meta-prompting and context engineering system for Claude Code, OpenCode, and Gemini CLI that solves the "context rot" problem — quality degradation as an AI's context window fills during long coding sessions. It breaks multi-phase projects into atomic tasks, each executed in a fresh 200k-token context window with automatic git commits. This document covers installation, the core workflow loop, when to use GSD vs. alternatives, and the broader spec-driven development ecosystem it belongs to. The key finding is that GSD's value comes from its simplicity (one `npx` install, five slash commands) compared to heavier alternatives, but it only supports three runtimes vs. competitors that support eight or more.

---

## 1. Introduction

### 1.1 Problem Statement

AI coding assistants like Claude Code produce high-quality code in short sessions but degrade in longer ones as the context window fills with prior conversation, stale code references, and accumulated instructions. Developers building multi-feature projects need a way to maintain consistent output quality across dozens of tasks without manually managing context resets.

### 1.2 Scope

This document covers GSD installation on Windows and macOS, the five-command workflow loop, when GSD adds value vs. when it's overhead, and comparison to five alternatives in the spec-driven development space. It does NOT cover GSD internals (prompt templates, XML task format), API-level usage, or GSD's OpenCode/Gemini integrations in detail.

### 1.3 Key Terms

| Term | Definition |
|------|------------|
| Context rot | Quality degradation in AI output as a conversation's context window fills up |
| Spec-driven development (SDD) | A workflow where AI coding agents follow structured specifications rather than ad-hoc prompts |
| Meta-prompting | Using prompts to generate and manage other prompts — GSD's approach to task planning |
| Subagent | A separate Claude Code instance spawned to execute a single atomic task in a fresh context window |
| Atomic commit | A git commit that represents one logical change, automatically created by GSD after each task |

---

## 2. Background & Prior Work

GSD emerged from the broader spec-driven development movement that gained momentum in early 2026 alongside Claude Code's growing adoption. Before tools like GSD, developers managed long AI sessions manually — copying context between conversations, restarting sessions when quality dropped, and writing their own planning documents.

The SDD ecosystem includes several approaches: cc-sdd supports eight different AI agents with Kiro-style commands [1], spec-kit follows a formal constitution-to-tasks pipeline [2], and claude-code-spec-workflow provides an MCP-based alternative with a real-time dashboard [3]. GSD differentiates by prioritizing minimal setup — one npx install, five slash commands — over breadth of features [4].

Native Claude Code features (CLAUDE.md, the Task tool, subagents) can achieve similar results without any framework, but require more manual orchestration.

---

## 3. Methodology / Approach

### 3.1 Setup

```bash
# Install GSD globally for Claude Code
npx get-shit-done-cc@latest
# Prompts: 1) Runtime → Claude Code  2) Scope → Global

# Open Claude Code and verify
claude
/gsd:help
```

### 3.2 Process

The GSD workflow follows a five-command loop per project phase:

1. `/gsd:new-project` — Describe your idea in plain language. GSD asks clarifying questions, researches the domain, and generates a phased roadmap.
2. `/gsd:discuss-phase N` — Clarify implementation preferences before any planning begins. This is where architectural decisions happen.
3. `/gsd:plan-phase N` — GSD researches the phase, then generates atomic XML-structured task plans small enough for a single context window.
4. `/gsd:execute-phase N` — Tasks run in parallel via subagents, each with a fresh 200k-token context. Each completed task gets an automatic atomic git commit.
5. `/gsd:verify-work N` — Manual confirmation that features work. Auto-diagnoses failures and can re-plan broken tasks.

For quick ad-hoc tasks that don't warrant the full loop, `/gsd:quick` provides a lightweight path.

---

## 4. Findings

### 4.1 Installation Is Genuinely One Command

The `npx get-shit-done-cc@latest` installer requires exactly two choices (runtime and scope), then completes with all green checkmarks. No config files to edit, no environment variables to set, no build step. After restarting Claude Code, all `/gsd:*` commands are immediately available. This is the lowest-friction install of any SDD tool tested.

### 4.2 GSD Solves Context Rot via Fresh Subagent Contexts

The core mechanism is straightforward: each atomic task runs in a new Claude Code subagent with a clean 200k-token context window. This means task #47 gets the same quality as task #1. The tradeoff is higher total token usage — each subagent consumes its own context allocation — but the consistency benefit is significant for projects with 20+ tasks.

### 4.3 The SDD Ecosystem Is Fragmented but Growing

Six distinct tools compete in the spec-driven development space, each with different strengths: GSD (simplicity), cc-sdd (multi-agent breadth), spec-kit (formal methodology), claude-code-spec-workflow (MCP integration), BMAD (enterprise workflows), and DIY with native features (full control). No single tool has emerged as the clear winner. Discovery of these tools is fragmented across GitHub awesome lists, the Claude Code plugin marketplace, aggregator sites like claudefa.st, Discord communities, and developer blog posts [5].

### 4.4 GSD Is Overkill for Small Tasks, Essential for Multi-Phase Projects

The five-command loop adds real overhead for bug fixes, one-liners, or single-file changes. The `/gsd:quick` command mitigates this but is still heavier than raw Claude Code. GSD's value appears at the boundary where a project is too complex for a single Claude Code session but too small for a real engineering team — roughly 5-50 tasks across 3+ phases.

---

## 5. Confusion Points

| # | What I assumed | What's actually true | Impact |
|---|----------------|----------------------|--------|
| 1 | GSD is a standalone coding tool | GSD is a layer on top of Claude Code / OpenCode / Gemini CLI — it provides slash commands and agents, not its own runtime | High |
| 2 | After installing GSD you start building immediately | You need to open Claude Code first (`claude` in terminal), then run `/gsd:help` to verify, then `/gsd:new-project` to begin | Medium |
| 3 | GSD requires complex configuration | Install is one command (`npx get-shit-done-cc@latest`), two prompts (runtime + scope), done | Medium |
| 4 | GSD is the only option for structured AI development | There's a growing ecosystem of SDD tools — cc-sdd, spec-kit, claude-code-spec-workflow, or DIY with native Claude Code features | Medium |
| 5 | There's a central app store for Claude Code tools | Discovery is fragmented across GitHub awesome lists, Claude Code plugin marketplace, aggregator sites, Discord communities, and blog posts | Low |
| 6 | You need to pick a runtime carefully during install | The default (Claude Code, option 1) is correct for most users | Low |

---

## 6. Failure Modes & Gotchas

| # | Trigger | Symptom | Fix | Severity |
|---|---------|---------|-----|----------|
| 1 | Not restarting Claude Code after install | `/gsd:*` commands not found | Restart Claude Code to reload slash commands | High |
| 2 | Running in Docker with tilde paths | File read failures on `~/.claude/` | Set `CLAUDE_CONFIG_DIR=/home/youruser/.claude` before installing | High |
| 3 | Not using `--dangerously-skip-permissions` | Constant approval prompts for basic operations like `git commit` and `date` | Run `claude --dangerously-skip-permissions` or configure granular permissions in `.claude/settings.json` | Medium |
| 4 | Using GSD for tiny tasks | Overhead outweighs benefit for bug fixes or one-liners | Use `/gsd:quick` for small tasks, or just use Claude Code directly | Medium |
| 5 | Stale GSD version | Missing new commands or features | Re-run `npx get-shit-done-cc@latest` to update | Low |

---

## 7. Analysis

### 7.1 Strengths

GSD's primary strength is its minimal friction: one install command, five core slash commands, and no configuration files to maintain. The fresh-context-per-task model genuinely solves context rot, producing consistent quality across long projects. The automatic atomic git commits create a clean, bisectable history without any developer effort. The tool works on Mac, Windows, and Linux with the same install path.

### 7.2 Limitations

GSD only supports three runtimes (Claude Code, OpenCode, Gemini CLI) while cc-sdd supports eight. The `--dangerously-skip-permissions` flag is effectively required for smooth operation, which disables Claude Code's safety checks. The multi-subagent architecture uses more tokens than manual prompting. The workflow is opinionated — you follow GSD's loop rather than your own. There is no web UI, no dashboard, and no visual feedback beyond terminal output.

### 7.3 Comparison to Alternatives

| Alternative | Key difference | When to prefer it |
|-------------|----------------|--------------------|
| cc-sdd | Supports 8 agents; Kiro-style commands | When you use Cursor, Codex, Copilot, or other non-Claude agents |
| spec-kit | Formal constitution → specification → planning → tasks pipeline | When you need rigorous documentation alongside code |
| claude-code-spec-workflow | MCP-based with real-time dashboard | When you want visual monitoring of task progress |
| DIY with native Claude Code | CLAUDE.md + subagents + Task tool, no framework | When you want full control and no dependency |
| No framework at all | Incremental prompting without specs | When tasks are small enough for single sessions |
| BMAD | Enterprise-oriented with sprint ceremonies | When you have stakeholders and formal review processes |

---

## 8. Recommendations

### 8.1 Decision Checklist

- [ ] You're using Claude Code, OpenCode, or Gemini CLI as your AI coding tool
- [ ] You're building something with multiple features/phases (not a one-off script)
- [ ] You can clearly describe what you want built
- [ ] You want structured planning without enterprise overhead
- [ ] You don't need cross-tool support beyond Claude Code / OpenCode / Gemini

> If any box is unchecked, see [Comparison to Alternatives](#73-comparison-to-alternatives).

### 8.2 Next Steps

1. Install GSD and run through `/gsd:new-project` on a small test project to learn the loop
2. Compare token usage between GSD's subagent approach and a single long Claude Code session on the same task
3. Evaluate cc-sdd if multi-agent breadth becomes important
4. Revisit this doc as GSD adds new runtimes or features

---

## 9. Open Questions & Unknowns

- What is GSD's actual token overhead vs. manual Claude Code sessions on equivalent tasks? No published benchmarks exist.
- Will GSD expand runtime support beyond three agents? The cc-sdd comparison suggests market pressure to do so.
- How does `/gsd:quick` compare to native Claude Code subagents for ad-hoc tasks? No direct comparison has been done.
- Is the `--dangerously-skip-permissions` requirement a dealbreaker for enterprise users? No guidance from GSD maintainers.

---

## References

1. [cc-sdd — Multi-Agent Spec-Driven Development](https://github.com/gotalab/cc-sdd)
2. [spec-kit — Formal Specification-Driven Development](https://developer.microsoft.com/blog/spec-driven-development-spec-kit)
3. [claude-code-spec-workflow — MCP-Based SDD](https://github.com/Pimzino/claude-code-spec-workflow)
4. [GSD — GitHub Repository](https://github.com/gsd-build/get-shit-done)
5. [awesome-claude-code — Community Curated List](https://github.com/hesreallyhim/awesome-claude-code)
6. [GSD npm Package](https://www.npmjs.com/package/get-shit-done-cc)
7. [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
8. [claudefa.st — Claude Code Extensions](https://claudefa.st/blog/tools/mcp-extensions/best-addons)
9. [Claude Code Plugin Marketplace](https://code.claude.com/docs/en/discover-plugins)
10. [GSD Discord Community](https://discord.gg/5JJgD5svVS)

---

## Appendix

### A. Raw Data / Configs

**Minimal install sequence (Windows PowerShell):**

```powershell
npx get-shit-done-cc@latest
# Select: 1) Claude Code → 1) Global
claude
/gsd:help
```

**Tool discovery channels:**

| Channel | URL / Access |
|---------|-------------|
| awesome-claude-code | github.com/hesreallyhim/awesome-claude-code |
| Claude Code plugins | `/plugin` command inside Claude Code |
| claudefa.st | claudefa.st |
| claudemarketplaces.com | claudemarketplaces.com |
| awesomeclaude.ai | awesomeclaude.ai |
| Claude Developers Discord | discord.gg links in Anthropic docs |
| Reddit | r/ClaudeAI |

### B. Related Documents

N/A — This was a single-topic conversation with no related documents in this library.

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial research article from installation walkthrough session | claude |
| 2026-02-14 | Expanded with alternatives comparison, tool discovery channels, and decision guidance | claude |
| 2026-02-14 | Restructured to match topic.md research article template (audit remediation) | claude |
