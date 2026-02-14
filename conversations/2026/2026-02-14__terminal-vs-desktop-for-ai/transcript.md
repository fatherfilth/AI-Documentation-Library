---
topic: terminal-vs-desktop-for-ai
category: skills
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "Conversation covering terminal fundamentals for AI tools, parallel Claude Code sessions, agent teams, and project setup via CLAUDE.md"
related_topics:
  - claude-code-parallel-sessions
  - claude-code-project-setup
---

# Transcript: Terminal vs Desktop for AI — and Claude Code Fundamentals

## Part 1: Terminal Fear and the Desktop-to-CLI Mental Shift

**User:** I'm scared to use the terminal — how is it different to the desktop application?

**Claude:** The terminal and desktop apps do the same thing underneath — they both talk to your operating system. The difference is purely the interface: desktop apps give you buttons, icons, and menus you click; the terminal gives you a text prompt where you type commands.

The fear usually comes from three things: no visible undo button, no visual feedback about what's available, and worry about breaking something by typing the wrong thing. But most commands are harmless — `ls` (list files), `cd` (change directory), `pwd` (print where you are) just *look* at things and can't break anything.

Key translations provided:

| Desktop action | Terminal equivalent |
|---|---|
| Open a folder | `cd folder-name` |
| See what's in a folder | `ls` |
| Where am I? | `pwd` |
| Create a folder | `mkdir my-folder` |
| Copy a file | `cp file.txt backup.txt` |
| Move/rename a file | `mv old.txt new.txt` |
| Delete a file | `rm file.txt` (no trash!) |

**User:** Specifically for Claude use.

**Claude:** For Claude, the main terminal use case is Claude Code — Anthropic's command-line tool. Installation is one command: `npm install -g @anthropic-ai/claude-code` (requires Node.js).

Day-to-day usage is conversational — you type `claude`, then chat naturally: "fix the bug in my login page," "add a dark mode toggle," etc. The terminal commands needed are minimal: `cd my-project`, `claude`, `ls`, and the one-time install command covers ~90% of usage.

Claude Code asks before doing anything destructive, so it has a safety net the raw terminal doesn't.

## Part 2: Running Multiple Claude Instances

**User:** Can you run multiple Claude instances? It doesn't allow me to on desktop.

**Claude:** The desktop app lets you have multiple conversations open, but Claude only actively works on one response at a time per account. It's sequential.

Claude Code in the terminal unlocks genuinely parallel work. You can run multiple instances working on the same project simultaneously — one fixing backend bugs, another writing frontend tests, another updating docs.

The simplest approach is opening multiple terminal tabs and running `claude` in each. For safety (avoiding file conflicts), Anthropic recommends git worktrees to give each instance its own branch and working copy.

There's also an experimental Agent Teams feature where instances coordinate, communicate, and share a task list.

**User:** They can all engage in the same project?

**Claude:** Yes — that's the whole point. Three approaches from simple to sophisticated:

1. **Same directory, different tasks** — works but risky if they touch the same file
2. **Git worktrees** (recommended) — each instance gets its own branch and folder, sharing the same Git history
3. **Agent Teams** (experimental) — coordinated instances with a lead that assigns tasks and teammates that communicate directly

## Part 3: Agent Teams Setup

**User:** How do you use the teams?

**Claude:** Three steps:

**Step 1 — Enable the feature:**
```bash
claude config set -g experimental.agentTeams true
```
Or set environment variable `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`.

**Step 2 — (Optional) Set up split panes with tmux:**
```bash
brew install tmux  # Mac
tmux
claude
```
Split-pane mode doesn't work in VS Code's integrated terminal, Windows Terminal, or Ghostty.

**Step 3 — Ask Claude to create a team in natural language:**
```
I'm building an e-commerce checkout flow. Create an agent team:
- One teammate for the backend API routes
- One teammate for the React frontend components
- One teammate writing tests for both
```

Claude creates the team, spawns teammates, sets up a shared task list, and they coordinate. The lead assigns tasks with dependency tracking. Teammates can message each other directly.

Key tips: pre-approve common permissions to reduce prompts, start with read-only tasks like code reviews, be aware of ~5x token cost per teammate, and watch for file conflicts when multiple teammates edit the same file.

## Part 4: Projects and CLAUDE.md in the Terminal

**User:** How do you create a project + add files to it + set project instructions in the terminal?

**Claude:** Claude Code doesn't have "projects" the way the desktop app does. The concept maps differently:

| Desktop app | Claude Code equivalent |
|---|---|
| Create project | `mkdir my-app && cd my-app` |
| Upload files | Files are already there — Claude sees the whole folder |
| Project instructions | `CLAUDE.md` file in project root (or run `/init`) |
| Start a chat in the project | `claude` (from inside the folder) |

**CLAUDE.md** is the equivalent of the desktop app's custom instructions. Three ways to create it:

1. **Auto-generate:** Run `/init` inside a Claude Code session — it scans your codebase and generates a starter file
2. **Write it yourself:** Create `CLAUDE.md` in project root with coding standards, architecture notes, commands
3. **On the fly:** Press `#` during a session to add instructions that get saved automatically

CLAUDE.md files work hierarchically:
- `~/.claude/CLAUDE.md` → global (all projects)
- `~/my-app/CLAUDE.md` → project-specific
- `~/my-app/src/CLAUDE.md` → directory-specific

The file becomes part of Claude's system prompt — every conversation starts with that context loaded.

Additional directories can be added with `claude --add-dir ../other-folder` at startup or `/add-dir /path` mid-session.

**User:** Nice.
