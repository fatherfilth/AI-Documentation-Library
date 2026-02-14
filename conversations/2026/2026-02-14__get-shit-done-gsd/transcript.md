---
topic: get-shit-done-gsd
category: tools
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "Understanding what GSD is, installing it on Windows, evaluating alternatives, and discovering Claude Code tools."
---

# Transcript: Get Shit Done (GSD) — Install, Evaluate, and Discover

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

## When to Use GSD

**User:** When is the best time to use get shit done?

**Claude:** GSD shines when you're building something with multiple features or phases and want Claude Code to do the heavy lifting reliably.

**Best fit:**
- Full project idea (app, SaaS, tool) — concept to working code
- Solo dev or small team avoiding enterprise ceremony
- Project big enough that a single Claude Code session would hit context rot
- You want clean git history with atomic, bisectable commits

**Skip GSD when:**
- Quick bug fix or one-off script — use `/gsd:quick` at most
- Exploring or prototyping without a clear goal
- Task fits comfortably in a single context window

**Key insight from the README:** GSD is for people who can clearly describe what they want built. If you don't yet know what you want, brainstorm first and bring GSD in once you have a clear vision.

## Alternatives to GSD

**User:** Are there any good alternatives to GSD?

**Claude:** The main alternatives in the spec-driven development space:

- **cc-sdd** — Closest direct competitor. Supports 8 agents (Claude Code, Cursor, Gemini CLI, Codex CLI, GitHub Copilot, Qwen Code, OpenCode, Windsurf). Kiro-style commands for requirements → design → tasks workflow. Best if you need cross-tool support.

- **spec-kit (GitHub)** — More formal approach: constitution → specification → planning → tasks. One user built a full CLI in under 3 days. Trade-off: overkill for small changes.

- **claude-code-spec-workflow (Pimzino)** — Development focus has shifted to an MCP version with real-time dashboard.

- **DIY with native Claude Code features** — Use CLAUDE.md, subagents, and the Task tool without any framework. Viable if you want full control.

- **No framework at all** — One developer argues SDD tools generate diminishing returns as apps grow, and incremental building without specs is faster for simple features. Agile + coding agents vs. waterfall-style documentation.

**Bottom line:** GSD's advantage is simplicity — one install, a few commands, complexity handled behind the scenes. Alternatives tend to be more formal (spec-kit), more cross-tool (cc-sdd), or fully DIY.

## Where to Discover Claude Code Tools

**User:** Where do people find tools like GSD?

**Claude:** Several discovery channels:

- **awesome-claude-code** (GitHub) — Curated list of slash commands, CLAUDE.md files, CLI tools, and workflows. Primary community discovery channel.
- **Claude Code plugin marketplace** — Official Anthropic marketplace, automatically available in Claude Code. Browse with `/plugin` command.
- **Aggregator sites** — claudefa.st (50+ MCP servers/extensions), claudemarketplaces.com, awesomeclaude.ai
- **Community channels** — Claude Developers Discord, Reddit (r/ClaudeAI), X/Twitter
- **Blog posts and word of mouth** — Developer blogs, Medium articles sharing workflows

No single "app store" yet — discovery is still fragmented across these channels.
