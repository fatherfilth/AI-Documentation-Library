---
topic: get-shit-done-gsd
category: tools
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "Understanding what GSD is, installing it on Windows, evaluating alternatives, discovering Claude Code tools, full command reference, workflow clarification, and applying GSD to bugfixes and existing codebases."
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

## Full Command Reference

**User:** What are the get shit done commands?

**Claude:** Full command list organized by category:

**Core Workflow:**
- `/gsd:new-project [--auto]` — Full init: questions → research → requirements → roadmap
- `/gsd:discuss-phase [N]` — Capture implementation decisions before planning
- `/gsd:plan-phase [N]` — Research + plan + verify for a phase
- `/gsd:execute-phase <N>` — Execute all plans in parallel waves, verify when complete
- `/gsd:verify-work [N]` — Manual user acceptance testing
- `/gsd:audit-milestone` — Verify milestone hit its definition of done
- `/gsd:complete-milestone` — Archive milestone, tag release
- `/gsd:new-milestone [name]` — Start next version

**Quick Mode:** `/gsd:quick` — Ad-hoc task with GSD guarantees but no full planning

**Navigation:** `/gsd:progress`, `/gsd:help`, `/gsd:update`, `/gsd:join-discord`

**Brownfield:** `/gsd:map-codebase` — Analyze existing codebase before new-project

**Phase Management:** `/gsd:add-phase`, `/gsd:insert-phase [N]`, `/gsd:remove-phase [N]`, `/gsd:list-phase-assumptions [N]`, `/gsd:plan-milestone-gaps`

**Session:** `/gsd:pause-work`, `/gsd:resume-work`

**Utilities:** `/gsd:settings`, `/gsd:set-profile <profile>`, `/gsd:add-todo [desc]`, `/gsd:check-todos`, `/gsd:debug [desc]`

## new-project vs Phase Commands

**User:** Does new project initiate all the phases for discuss/plan?

**Claude:** No — `/gsd:new-project` only handles the setup stage:

1. Questions — asks until it understands your idea
2. Research — spawns parallel agents to investigate the domain (optional)
3. Requirements — extracts v1, v2, and out of scope
4. Roadmap — creates phases mapped to requirements

Creates: `PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `STATE.md`

After that, you drive each phase manually through the loop:

```
/gsd:discuss-phase 1 → /gsd:plan-phase 1 → /gsd:execute-phase 1 → /gsd:verify-work 1
/gsd:discuss-phase 2 → /gsd:plan-phase 2 → /gsd:execute-phase 2 → /gsd:verify-work 2
... and so on
```

The `discuss` step is optional but recommended. Once all phases are done, run `/gsd:complete-milestone` to archive and tag.

**Summary:** new-project sets up the "what"; you step through the "how" phase by phase.

## Bugfixing and Existing Codebases

**User:** How does get shit done apply for bugfixing / changes to an existing database etc?

**Claude:** GSD has a few paths depending on the size of the change:

**Bug fixes and small changes → `/gsd:quick`**
Lightweight path. Describe what's wrong, GSD gives same guarantees (atomic commits, state tracking) without full planning. No research, no plan checker, no verifier — just planner + executor. Lives in `.planning/quick/`.

**Systematic debugging → `/gsd:debug [desc]`**
Specifically for diagnosing broken things. Tracks what's been tried across the session with persistent state.

**Larger changes to existing code → `/gsd:map-codebase` first**
For substantial work (database migration, schema restructure, major feature on existing code):
1. `/gsd:map-codebase` — analyzes stack, architecture, conventions
2. `/gsd:new-project` — questions focus on what you're adding, not starting fresh
3. Then normal phase loop

**The honest gap:** GSD is optimized for building new things. For ongoing maintenance, incremental schema changes, or routine database work, `/gsd:quick` and `/gsd:debug` are lighter tools. For routine "change column X, add index Y" work, straight Claude Code might be all you need.

**Rule of thumb:**
- Bug fix or small DB change → `/gsd:quick` or `/gsd:debug`
- Major refactor or new feature on existing code → `map-codebase` then full workflow
- Routine maintenance → just use Claude Code directly
