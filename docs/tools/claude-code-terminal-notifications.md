---
title: "Claude Code Terminal Notifications"
status: stable
category: "tools"
slug: "claude-code-terminal-notifications"
created: 2026-02-14
updated: 2026-02-14
author: "claude"
tags: [claude-code, notifications, terminal-bell, hooks, productivity]
---

# Claude Code Terminal Notifications

---

## Abstract

Claude Code runs long agentic tasks in the terminal and periodically needs human input — permission approvals, clarifying questions, or simple acknowledgment that a task is done. Without notifications, you end up constantly checking the terminal or missing prompts entirely. This document covers the three main notification strategies: the built-in terminal bell (zero config), terminal-native notifications (iTerm2), and Claude Code hooks (full customization). The key finding is that the terminal bell is the fastest to set up but the hooks system is required for reliable, alert-fatigue-free notifications — especially on Windows/WSL where the bell often fails. A critical discovery: the widely-documented `claude config set --global` command is broken in Claude Code v2.0+ and must be replaced with direct `settings.json` editing.

---

## 1. Introduction

### 1.1 Problem Statement

When Claude Code is running a multi-step task (refactoring, test suites, code generation), you naturally switch to other work. The problem is knowing when Claude finishes or gets blocked waiting for your input. Without a notification, you either poll the terminal constantly (breaking flow) or miss the prompt and waste time.

### 1.2 Scope

This document covers notification configuration for Claude Code's CLI. It does NOT cover the VS Code extension's built-in notification panel, CI/CD notification integrations, or Slack/webhook-based alerting pipelines.

### 1.3 Key Terms

| Term | Definition |
|------|------------|
| Terminal bell | ASCII character `\a` (BEL, 0x07) that triggers the terminal emulator's alert behavior |
| Hook | A user-defined shell command that Claude Code executes at a specific lifecycle point |
| `idle_prompt` | Notification event that fires when Claude Code displays a new prompt (including after every normal response) |
| `permission_prompt` | Notification event that fires only when Claude needs explicit approval to proceed |
| Matcher | A string filter in hook config that determines which notification types trigger a specific hook |
| `settings.json` | Claude Code's configuration file at `~/.claude/settings.json` (user-level) or `.claude/settings.json` (project-level) |

---

## 2. Background & Prior Work

Claude Code has supported terminal bell notifications since early 2025 via a global config flag. The hooks system was introduced alongside lifecycle events (PreToolUse, PostToolUse, Notification, Stop, etc.) giving users arbitrary shell command execution at key points. Community tools like CCNotify [1] and the claude-code-notifications repo [2] emerged to fill the gap with richer macOS desktop notifications including project context and workspace detection. The official docs at code.claude.com cover both terminal configuration [3] and hooks [4]. However, the `claude config` CLI command was deprecated in v2.0, breaking the most commonly documented setup path [9].

---

## 3. Methodology / Approach

### 3.1 Setup

```
Prerequisites:
- Claude Code CLI installed (claude command available)
- A terminal emulator (iTerm2, Kitty, WezTerm, Ghostty, Windows Terminal, VS Code, or macOS Terminal.app)
- For hooks: jq (optional, for JSON parsing in hook commands)
- For macOS hooks: terminal-notifier (brew install terminal-notifier) OR osascript (built-in)
- For Linux hooks: notify-send (usually pre-installed)
```

### 3.2 Process

**Approach A: Terminal Bell (simplest)**

1. Open `~/.claude/settings.json` and add `"preferredNotifChannel": "terminal_bell"` (see Finding 4.5 — do NOT use the deprecated `claude config set --global` command)
2. Verify with `echo -e "\a"` — listen for beep or watch for flash
3. Configure terminal emulator to surface bell events as notifications (varies by terminal — see Findings)
4. Test end-to-end by starting a Claude Code session, giving it a task, and switching windows

**Approach B: Hooks (most flexible)**

1. Type `/hooks` in Claude Code CLI
2. Select the Notification event
3. Set matcher to `permission_prompt` (recommended) or `*` (all notifications)
4. Enter a platform-appropriate notification command
5. Save to User settings (global) or Project settings (scoped)
6. Test by asking Claude to do something requiring permission

---

## 4. Findings

### 4.1 Terminal Bell Is One Command But Requires Terminal-Side Config

The Claude Code side requires adding `"preferredNotifChannel": "terminal_bell"` to your settings.json. It takes effect immediately with no restart. However, most terminals have the bell muted or set to visual-only by default. The real work is configuring each terminal emulator:

- **iTerm2**: Settings → Profiles → Terminal → uncheck "Silence bell" → enable "Send escape sequence-generated alerts". This is the gold standard — it sends actual macOS notification banners.
- **macOS Terminal.app**: Settings → Profiles → Advanced → "Audible bell". Only produces a beep — no notification banner. Not suitable if you switch away from the terminal.
- **Ghostty**: Works out of the box with native notification support.
- **Kitty**: Requires `enable_audio_bell yes` in kitty.conf. Supports `command_on_bell` for OS-level notifications.
- **WezTerm**: `config.audible_bell = "SystemBeep"` in wezterm.lua.
- **Windows Terminal**: Settings → Profile → Advanced → Bell notification style. Supports taskbar flash which is useful when in another window.
- **VS Code terminal**: `terminal.integrated.enableBell` setting. Flashes the terminal tab.
- **tmux**: `set-option -g visual-bell on` plus `set-option -g bell-action any`. Flags the tmux window.

### 4.2 The `idle_prompt` Matcher Causes Alert Fatigue

The `idle_prompt` notification fires after every single response — not just when Claude is genuinely blocked. This was reported as a usability issue on GitHub [5]. If you set your Notification hook matcher to `idle_prompt` or `*`, you'll get notified after every file read, test run, and package install. The result is alert fatigue: you learn to ignore the notifications and then miss the ones that actually matter.

The practical fix: use `permission_prompt` as your matcher for the Notification hook (fires only when Claude needs approval), and add a separate `Stop` hook for task completion with a different, gentler sound.

### 4.3 Hooks Provide Full Control But Require More Setup

The hooks system lets you run any shell command at lifecycle points. For notifications, the key events are:

- **Notification** (with matchers: `permission_prompt`, `idle_prompt`, `auth_success`, `elicitation_dialog`)
- **Stop** (fires when Claude finishes responding)

Platform-specific commands:
- macOS native: `osascript -e 'display notification "..." with title "Claude Code" sound name "Ping"'`
- macOS terminal-notifier: `terminal-notifier -title 'Claude Code' -message 'Ready for input'`
- Linux: `notify-send 'Claude Code' 'Awaiting your input'`
- Windows: PowerShell MessageBox or SystemSounds

Config goes in `~/.claude/settings.json` (global) or `.claude/settings.json` (project-level).

### 4.4 WSL Breaks the Terminal Bell

The standard terminal bell does not work reliably in Windows WSL environments. If you're on WSL, skip the bell entirely and go straight to hooks with a PowerShell notification command, or add instructions to your CLAUDE.md telling Claude to execute `powershell.exe -c "[System.Media.SystemSounds]::Question.Play()"` when tasks complete.

### 4.5 `claude config set --global` Is Broken in v2.0+

The most widely documented command for enabling terminal bell — `claude config set --global preferredNotifChannel terminal_bell` — throws `error: unknown option '--global'` in Claude Code v2.0 and later [9]. The `claude config` subcommand was deprecated. This affects all platforms.

**Correct approach for v2.0+:**

Edit `~/.claude/settings.json` directly (on Windows: `C:\Users\<username>\.claude\settings.json`):

```json
{
  "preferredNotifChannel": "terminal_bell"
}
```

From PowerShell:
```powershell
notepad "$env:USERPROFILE\.claude\settings.json"
```

From macOS/Linux:
```bash
nano ~/.claude/settings.json
```

Alternatively, use the `/config` slash command inside a running Claude Code session, which opens an interactive settings menu and works correctly.

This is particularly important because the vast majority of blog posts, tutorials, and even Anthropic community posts still reference the deprecated `--global` flag syntax. Anyone following those guides on a current version of Claude Code will hit this error immediately.

---

## 5. Confusion Points

| # | What I assumed | What's actually true | Impact |
|---|----------------|----------------------|--------|
| 1 | The terminal bell would "just work" after the Claude Code config command | Most terminals have the bell muted by default — you need to configure the terminal emulator separately | High |
| 2 | `idle_prompt` means "Claude is idle and waiting for me" | It fires after every response, not just when Claude is genuinely blocked | High |
| 3 | macOS Terminal.app supports notification banners from bell events | Terminal.app only supports audible beep and visual flash — no OS notification banners. iTerm2 is needed for that. | Medium |
| 4 | One notification hook config works across all platforms | Each OS needs a different notification command (osascript, notify-send, PowerShell) | Medium |
| 5 | `claude config set --global` is the correct way to set preferences | The `--global` flag is broken in v2.0+. Must edit `~/.claude/settings.json` directly or use `/config` inside a session | High |

---

## 6. Failure Modes & Gotchas

| # | Trigger | Symptom | Fix | Severity |
|---|---------|---------|-----|----------|
| 1 | Using `claude config set --global` on v2.0+ | `error: unknown option '--global'` | Edit `~/.claude/settings.json` directly or use `/config` slash command | Critical |
| 2 | Using WSL with terminal bell | No notification fires — bell character is swallowed | Use hooks with PowerShell command instead of terminal bell | High |
| 3 | Setting matcher to `*` or `idle_prompt` | Notification after every single response, alert fatigue | Use `permission_prompt` matcher for Notification hook, add separate Stop hook | High |
| 4 | Running multiple Claude sessions with generic notifications | Can't tell which session needs attention | Use terminal-notifier or custom hook script that includes project/workspace context | Medium |
| 5 | iTerm2 "Silence bell" is checked (default) | Bell fires but produces no sound or notification | Uncheck "Silence bell" in Profiles → Terminal | Medium |
| 6 | claude-code v1.0.95+ with invalid settings.json | Hooks disabled entirely with no obvious error | Validate settings.json — any invalid field disables all hooks | Critical |

---

## 7. Analysis

### 7.1 Strengths

The terminal bell approach is genuinely zero-dependency once you edit settings.json. For iTerm2 users on macOS, it produces real OS notification banners with no additional tooling. The hooks system is extremely flexible — any shell command, per-event filtering, project-scoped or global. The `/hooks` interactive menu makes setup approachable for first-timers.

### 7.2 Limitations

The terminal bell is platform-dependent and fails silently on WSL. The `idle_prompt` naming is misleading and causes alert fatigue when used naively. There's no built-in "Claude is genuinely blocked" notification type — you have to approximate it by combining `permission_prompt` and `Stop` hooks. Multi-session awareness requires custom scripting. The deprecation of `claude config set --global` means most existing documentation is wrong, creating a poor first-run experience.

### 7.3 Comparison to Alternatives

| Alternative | Key difference | When to prefer it |
|-------------|----------------|--------------------|
| Terminal bell | Zero config, edit settings.json | Quick setup, iTerm2 on macOS, single session |
| Hooks with osascript/notify-send | Full control, per-event filtering | Need different alerts for different events |
| terminal-notifier | Clickable banners, custom icons, project context | Multiple sessions, need to identify which project |
| CCNotify | Turnkey macOS solution with DB logging | Want notifications + audit trail without custom scripting |
| OSC escape sequences | Works over SSH/Remote | Running Claude Code on a remote server via VS Code Remote SSH |

---

## 8. Recommendations

### 8.1 Decision Checklist

- [ ] You use iTerm2 or Ghostty on macOS → terminal bell is sufficient
- [ ] You run multiple Claude sessions simultaneously → use hooks with project context
- [ ] You're on WSL → skip bell, go straight to hooks with PowerShell
- [ ] You need different sounds for "needs input" vs "task done" → use separate Notification + Stop hooks
- [ ] You work over SSH/remote → use OSC escape sequences

> If any box is unchecked, see [Comparison to Alternatives](#73-comparison-to-alternatives).

### 8.2 Next Steps

- Explore the `Stop` hook for task-completion notifications (separate from input-needed notifications)
- Test the `permission_prompt` vs `idle_prompt` distinction in your actual workflow before committing to a matcher
- If running multiple sessions, investigate CCNotify or build a custom hook script that includes workspace/project context in the notification message

---

## 9. Open Questions & Unknowns

- Whether Anthropic will introduce a dedicated `waiting_for_user_action` notification type (requested in GitHub issue #12048 [5] but closed as duplicate)
- Exact behavior of `elicitation_dialog` matcher — limited documentation available
- Whether Ghostty's native bell-to-notification support covers all notification types or just the basic bell character
- Whether the `/config` slash command will remain the supported replacement for `claude config set` long-term

---

## References

1. [CCNotify — Desktop notifications for Claude Code](https://github.com/dazuiba/CCNotify)
2. [claude-code-notifications — Notification solutions from terminal bells to desktop alerts](https://github.com/dongzhenye/claude-code-notifications)
3. [Claude Code Docs — Terminal configuration](https://code.claude.com/docs/en/terminal-config)
4. [Claude Code Docs — Automate workflows with hooks](https://code.claude.com/docs/en/hooks-guide)
5. [GitHub Issue #12048 — Add notification matcher for when Claude is actually waiting for user input](https://github.com/anthropics/claude-code/issues/12048)
6. [Claude Code terminal notification hooks — martin.hjartmyr.se](https://martin.hjartmyr.se/articles/claude-code-terminal-notifications/)
7. [Boris Buliga — Claude Code Notifications That Don't Suck](https://www.d12frosted.io/posts/2026-01-05-claude-code-notifications)
8. [Desktop Notifications for Claude Code over SSH — kane.mx](https://kane.mx/posts/2025/claude-code-notification-hooks/)
9. [GitHub Issue #8441 — `claude config set --global` is broken](https://github.com/anthropics/claude-code/issues/8441)

---

## Appendix

### A. Raw Data / Configs

**Recommended hooks config (macOS with terminal-notifier):**

```json
{
  "preferredNotifChannel": "terminal_bell",
  "hooks": {
    "Notification": [
      {
        "matcher": "permission_prompt",
        "hooks": [{
          "type": "command",
          "command": "terminal-notifier -title 'Claude Code' -message 'Needs permission' -sound Glass"
        }]
      }
    ],
    "Stop": [
      {
        "hooks": [{
          "type": "command",
          "command": "terminal-notifier -title 'Claude Code' -message 'Task complete' -sound Tink"
        }]
      }
    ]
  }
}
```

**Recommended hooks config (Linux):**

```json
{
  "preferredNotifChannel": "terminal_bell",
  "hooks": {
    "Notification": [
      {
        "matcher": "permission_prompt",
        "hooks": [{
          "type": "command",
          "command": "notify-send 'Claude Code' 'Needs permission'"
        }]
      }
    ],
    "Stop": [
      {
        "hooks": [{
          "type": "command",
          "command": "notify-send 'Claude Code' 'Task complete'"
        }]
      }
    ]
  }
}
```

**Recommended hooks config (Windows/WSL):**

```json
{
  "preferredNotifChannel": "terminal_bell",
  "hooks": {
    "Notification": [
      {
        "matcher": "permission_prompt",
        "hooks": [{
          "type": "command",
          "command": "powershell.exe -Command \"[System.Reflection.Assembly]::LoadWithPartialName('System.Windows.Forms'); [System.Windows.Forms.MessageBox]::Show('Claude Code needs permission', 'Claude Code')\""
        }]
      }
    ]
  }
}
```

### B. Related Documents

N/A — no related documents from this conversation.

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial research article | claude |
| 2026-02-14 | Added Finding 4.5: `claude config set --global` broken in v2.0+, updated methodology and confusion points with manual settings.json workaround, added reference [9] | claude |
