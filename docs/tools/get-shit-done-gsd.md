# Get Shit Done (GSD)

> **Status:** `stable`
> **Created:** 2026-02-14
> **Updated:** 2026-02-14
> **Author:** Claude (documentation agent)

---

## Purpose / When to Use

GSD is a meta-prompting, context engineering, and spec-driven development system for Claude Code, OpenCode, and Gemini CLI. Reach for it when you're building a multi-phase project with an AI coding agent and want structured planning, fresh context windows per task, and automatic atomic git commits — without enterprise project management overhead. Best for projects too complex for a single Claude Code session but too small for a real engineering team.

---

## Quickstart

```bash
# 1. Install GSD globally for Claude Code
npx get-shit-done-cc@latest
# Select: 1) Claude Code → 1) Global

# 2. Open Claude Code
claude

# 3. Verify installation
/gsd:help

# 4. Start a new project
/gsd:new-project

# 5. Follow the loop for each phase:
/gsd:discuss-phase 1
/gsd:plan-phase 1
/gsd:execute-phase 1
/gsd:verify-work 1

# 6. For quick ad-hoc tasks that don't need full planning:
/gsd:quick
```

---

## Mental Model

Think of GSD as a project manager sitting between you and Claude Code. You describe what you want in plain language. GSD breaks that into a roadmap of phases, then each phase into atomic tasks small enough to fit in a fresh context window. Each task is executed by a subagent with a clean 200k-token context, so you never hit the "context rot" problem where Claude's quality degrades as the conversation gets long. The result is consistent, high-quality code with clean git history.

---

## Confusion Points

| # | What I thought | What's actually true |
|---|----------------|----------------------|
| 1 | GSD is a standalone coding tool | GSD is a layer on top of Claude Code / OpenCode / Gemini CLI — it provides slash commands and agents, not its own runtime |
| 2 | You need to pick a runtime carefully | The default (Claude Code, option 1) is correct for most users; the installer defaults to it |
| 3 | After installing GSD you start building immediately | You need to open Claude Code first (`claude` in terminal), then run `/gsd:help` to verify, then `/gsd:new-project` to begin |
| 4 | GSD requires complex configuration | Install is one command (`npx get-shit-done-cc@latest`), two prompts (runtime + location), and you're done |
| 5 | GSD is the only option for structured Claude Code development | There's a growing ecosystem of spec-driven development tools — cc-sdd, spec-kit, claude-code-spec-workflow, or even DIY with native Claude Code features |
| 6 | There's a central app store for Claude Code tools | Discovery is fragmented across GitHub awesome lists, the Claude Code plugin marketplace, aggregator sites, Discord communities, and blog posts |

---

## Gotchas / Failure Modes

| # | Trigger | Symptom | Fix |
|---|---------|---------|-----|
| 1 | Not restarting Claude Code after install | `/gsd:*` commands not found | Restart Claude Code to reload slash commands |
| 2 | Running in Docker with tilde paths | File read failures on `~/.claude/` | Set `CLAUDE_CONFIG_DIR=/home/youruser/.claude` before installing |
| 3 | Not using `--dangerously-skip-permissions` | Constant approval prompts for basic operations like `git commit` and `date` | Run `claude --dangerously-skip-permissions` or configure granular permissions in `.claude/settings.json` |
| 4 | Stale version | Missing new commands or features | Re-run `npx get-shit-done-cc@latest` to update |
| 5 | Using GSD for tiny tasks | Overhead outweighs benefit for bug fixes or one-liners | Use `/gsd:quick` for small tasks, or just use Claude Code directly |

---

## Pros / Cons

| Pros | Cons |
|------|------|
| Dead simple install — one `npx` command | Requires Claude Code (or OpenCode/Gemini CLI) already installed |
| Solves context rot with fresh subagent contexts | Uses more tokens than manual prompting due to multi-agent orchestration |
| Atomic git commits per task — clean, bisectable history | `--dangerously-skip-permissions` flag recommended, which disables safety checks |
| Works on Mac, Windows, and Linux | Opinionated workflow — you follow its loop, not your own |
| No enterprise ceremony — just describe and build | Still requires clear descriptions of what you want |
| Simplest install and UX of the SDD tools | Only supports Claude Code, OpenCode, and Gemini CLI (cc-sdd supports 8 agents) |

---

## Alternatives

| Alternative | Key difference |
|-------------|----------------|
| cc-sdd | Supports 8 agents (Claude Code, Cursor, Gemini, Codex, Copilot, Qwen, OpenCode, Windsurf); Kiro-style commands |
| spec-kit (GitHub) | More formal: constitution → specification → planning → tasks; heavier process but thorough |
| claude-code-spec-workflow | Shifting to MCP version with real-time dashboard; broader tool compatibility |
| DIY with native Claude Code | Use CLAUDE.md, subagents, and Task tool directly — full control, no framework |
| No framework (incremental) | Skip specs entirely; build small features one by one — faster for simple work, fragile at scale |
| BMAD | Enterprise-oriented with sprint ceremonies and stakeholder workflows |

---

## Decision Checklist

- [ ] You're using Claude Code, OpenCode, or Gemini CLI as your AI coding tool
- [ ] You're building something with multiple features/phases (not a one-off script)
- [ ] You can clearly describe what you want built
- [ ] You want structured planning without enterprise project management overhead
- [ ] You don't need cross-tool support beyond Claude Code / OpenCode / Gemini

> **Rule:** If any box is unchecked, see [Alternatives](#alternatives).

---

## Where to Discover Tools Like GSD

- **awesome-claude-code** — Primary community-curated list on GitHub for Claude Code tools, workflows, and extensions
- **Claude Code plugin marketplace** — Official Anthropic marketplace, available via `/plugin` command inside Claude Code
- **Aggregator sites** — claudefa.st, claudemarketplaces.com, awesomeclaude.ai
- **Community channels** — Claude Developers Discord, Reddit (r/ClaudeAI), X/Twitter
- **Developer blog posts** — Medium, personal blogs, and dev.to articles sharing workflows

---

## References

- [GSD GitHub Repository](https://github.com/gsd-build/get-shit-done)
- [GSD npm Package](https://www.npmjs.com/package/get-shit-done-cc)
- [GSD Discord Community](https://discord.gg/5JJgD5svVS)
- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code)
- [cc-sdd GitHub Repository](https://github.com/gotalab/cc-sdd)
- [spec-kit (GitHub Blog)](https://developer.microsoft.com/blog/spec-driven-development-spec-kit)
- [claude-code-spec-workflow](https://github.com/Pimzino/claude-code-spec-workflow)
- [claudefa.st — Claude Code Extensions](https://claudefa.st/blog/tools/mcp-extensions/best-addons)
- [Claude Code Plugin Docs](https://code.claude.com/docs/en/discover-plugins)

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial draft from installation walkthrough session | Claude (documentation agent) |
| 2026-02-14 | Expanded with alternatives comparison, when-to-use guidance, and tool discovery channels | Claude (documentation agent) |
