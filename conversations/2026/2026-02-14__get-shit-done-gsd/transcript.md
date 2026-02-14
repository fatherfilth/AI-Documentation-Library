---
topic: get-shit-done-gsd
category: tools
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "Understanding what GSD is, installing it on Windows for Claude Code, and first steps after installation."
---

# Transcript: Get Shit Done (GSD) — What It Is and How to Install It

## What is GSD?

**User:** What is this used for? *(links to https://github.com/gsd-build/get-shit-done)*

**Claude:** Get Shit Done (GSD) is a meta-prompting and context engineering system designed for AI coding tools like Claude Code, OpenCode, and Gemini CLI. Its main purpose is to make AI-assisted coding more reliable and structured, especially for solo developers or small teams.

The core problem it solves is "context rot" — quality degradation as the AI's context window fills up. GSD breaks work into atomic tasks executed in fresh context windows, so quality stays consistent.

The workflow follows a loop:

1. `/gsd:new-project` — describe your idea; system asks questions, researches, creates a roadmap
2. `/gsd:discuss-phase` — clarify implementation preferences before planning
3. `/gsd:plan-phase` — research + create atomic XML-structured task plans
4. `/gsd:execute-phase` — run plans in parallel with fresh 200k-token contexts, atomic git commits
5. `/gsd:verify-work` — manually confirm features work; auto-diagnoses failures

Key features: multi-agent orchestration, XML prompt formatting, fresh context per task, automatic atomic git commits.

Installed via `npx get-shit-done-cc@latest`. MIT licensed.

## Installation on Windows

**User:** *(shows screenshot of GSD installer running in PowerShell — runtime selection prompt)*
So now what do I do with this?

**Claude:** You're at the runtime selection prompt. Choose based on which AI coding tool you use:

- **1 (Claude Code)** — most common, press Enter since it's the default
- **2 (OpenCode)** — open source, free models
- **3 (Gemini CLI)** — Google's CLI
- **4 (All)** — install for everything

After that it asks global vs local install. Global makes it available for all projects.

**User:** *(shows screenshot — installation complete with all green checkmarks)*

**Claude:** You're all set. GSD is installed globally for Claude Code. Next steps:

1. Open Claude Code by typing `claude` in your terminal
2. Run `/gsd:help` to confirm installation
3. Run `/gsd:new-project` when ready to start building

## Key Decision Point

**User asked** whether this was already documented in the AI Documentation Library.

**Claude confirmed** the library index was empty — GSD would be the first entry.
