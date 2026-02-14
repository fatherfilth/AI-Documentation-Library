# Get Shit Done (GSD)

> **Status:** `stable`
> **Created:** 2026-02-14
> **Updated:** 2026-02-14
> **Author:** Claude (documentation agent)

---

## Purpose / When to Use

GSD is a meta-prompting, context engineering, and spec-driven development system for Claude Code, OpenCode, and Gemini CLI. Reach for it when you're building a project with an AI coding agent and want structured planning, fresh context windows per task, and automatic atomic git commits — without the overhead of enterprise project management tools.

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

---

## Gotchas / Failure Modes

| # | Trigger | Symptom | Fix |
|---|---------|---------|-----|
| 1 | Not restarting Claude Code after install | `/gsd:*` commands not found | Restart Claude Code to reload slash commands |
| 2 | Running in Docker with tilde paths | File read failures on `~/.claude/` | Set `CLAUDE_CONFIG_DIR=/home/youruser/.claude` before installing |
| 3 | Not using `--dangerously-skip-permissions` | Constant approval prompts for basic operations like `git commit` and `date` | Run `claude --dangerously-skip-permissions` or configure granular permissions in `.claude/settings.json` |
| 4 | Stale version | Missing new commands or features | Re-run `npx get-shit-done-cc@latest` to update |

---

## Pros / Cons

| Pros | Cons |
|------|------|
| Dead simple install — one `npx` command | Requires Claude Code (or OpenCode/Gemini CLI) already installed |
| Solves context rot with fresh subagent contexts | Uses more tokens than manual prompting due to multi-agent orchestration |
| Atomic git commits per task — clean, bisectable history | `--dangerously-skip-permissions` flag recommended, which disables safety checks |
| Works on Mac, Windows, and Linux | Opinionated workflow — you follow its loop, not your own |
| No enterprise ceremony — just describe and build | Still requires clear descriptions of what you want |

---

## Alternatives

| Alternative | Key difference |
|-------------|----------------|
| BMAD | More enterprise-oriented with sprint ceremonies and stakeholder workflows |
| SpecKit | Spec-driven but less focus on context engineering and subagent orchestration |
| OpenSpec | Specification-first approach but lacks GSD's multi-agent execution model |
| Taskmaster | Task management focus; GSD users report better results for end-to-end building |
| Manual Claude Code | No structure — works for small tasks, falls apart at scale due to context rot |

---

## Decision Checklist

- [ ] You're using Claude Code, OpenCode, or Gemini CLI as your AI coding tool
- [ ] You're building something with multiple features/phases (not a one-off script)
- [ ] You want structured planning without enterprise project management overhead
- [ ] You're comfortable with the discuss → plan → execute → verify loop

> **Rule:** If any box is unchecked, see [Alternatives](#alternatives).

---

## References

- [GSD GitHub Repository](https://github.com/gsd-build/get-shit-done)
- [GSD npm Package](https://www.npmjs.com/package/get-shit-done-cc)
- [GSD Discord Community](https://discord.gg/5JJgD5svVS)
- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial draft from installation walkthrough session | Claude (documentation agent) |
