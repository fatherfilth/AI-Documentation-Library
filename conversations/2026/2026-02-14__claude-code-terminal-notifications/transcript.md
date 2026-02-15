---
topic: claude-code-terminal-notifications
category: tools
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "Step-by-step guide to configuring terminal bell notifications and hooks in Claude Code so you get alerted when the agent needs input."
related_topics: []
---

# Transcript: Claude Code Terminal Notifications

## Initial Question — Getting Alerted When Claude Needs Input

**User:** When working with a terminal how do I get alerted if Claude needs an input?

**Claude:** There are several approaches depending on your setup. The simplest is the terminal bell — Claude Code triggers a bell character (`\a`) when it needs input. Most terminal emulators can be configured to show a system notification or bounce the dock icon when a bell fires.

Key approaches covered:

1. **Terminal bell** — one command: `claude config set --global preferredNotifChannel terminal_bell`
2. **iTerm2 built-in** — no hooks required, native notification support
3. **Hooks** — the most flexible approach, works everywhere. Configure via `/hooks` in the CLI or by editing `~/.claude/settings.json`
4. **CCNotify** — a community tool that uses Claude Code hooks to send macOS desktop notifications with project context
5. **terminal-notifier** — a macOS CLI tool that sends rich Notification Center alerts

### Alert Fatigue Gotcha

The `idle_prompt` notification hook fires after EVERY response — not just when Claude is genuinely blocked waiting for input. This causes alert fatigue. The workaround is to use `permission_prompt` matcher specifically, or combine separate Notification and Stop hooks with different sounds.

### Platform-Specific Notification Commands

- **macOS native:** `osascript -e 'display notification "..." with title "Claude Code" sound name "Ping"'`
- **macOS terminal-notifier:** `terminal-notifier -title 'Claude Code' -message 'Ready for input'`
- **Linux:** `notify-send 'Claude Code' 'Awaiting your input'`
- **Windows:** PowerShell MessageBox or SystemSounds

---

## Deep Dive — Step-by-Step Terminal Bell Configuration

**User:** How do I configure the terminal bell step by step?

**Claude:** Covered the full end-to-end setup:

### Step 1: Enable in Claude Code
```bash
claude config set --global preferredNotifChannel terminal_bell
```
No restart required — takes effect immediately.

### Step 2: Test Bell Support
```bash
echo -e "\a"
```
If you hear a beep or see a flash, proceed. If nothing happens, your terminal needs configuration.

### Step 3: Configure Terminal Emulator

Detailed per-terminal instructions provided:

- **iTerm2:** Settings → Profiles → Terminal → uncheck "Silence bell" → enable "Send escape sequence-generated alerts" → set notification delay
- **macOS Terminal.app:** Settings → Profiles → Advanced → check "Audible bell" (note: no OS notification banners, only beep/flash)
- **Ghostty:** Native support, works out of the box
- **Kitty:** `enable_audio_bell yes` in kitty.conf, plus optional `command_on_bell` for OS notifications
- **WezTerm:** `config.audible_bell = "SystemBeep"` in wezterm.lua
- **Windows Terminal:** Settings → Profile → Advanced → Bell notification style → Audible/Visual/Both/Taskbar flash
- **VS Code terminal:** Settings → `terminal.integrated.enableBell` → true
- **tmux:** `set-option -g visual-bell on` and `set-option -g bell-action any`

### Step 4: Verify End-to-End
Start a Claude Code session, give it a multi-second task, switch windows, and confirm the notification fires.

### WSL Caveat
Terminal bell often doesn't work in WSL. Recommendation: skip bell entirely and go straight to hooks with PowerShell notification commands.

### Graduation Path
Terminal bell = zero config option. Hooks = full control option (custom sounds, project-specific messages, clickable banners).

---

## Bug Report — `claude config set --global` Broken on Newer Versions

**User:** (Screenshot) Running `claude config set --global preferredNotifChannel terminal_bell` in PowerShell on Windows returns `error: unknown option '--global'`.

**Claude:** This is a known bug (GitHub issue #8441). The `claude config set --global` command is broken in Claude Code v2.0+ — the `claude config` command was deprecated. The fix is to edit the settings file directly:

### Workaround: Edit settings.json manually

Open or create `C:\Users\<username>\.claude\settings.json` and add:

```json
{
  "preferredNotifChannel": "terminal_bell"
}
```

From PowerShell:
```powershell
notepad "$env:USERPROFILE\.claude\settings.json"
```

Takes effect immediately, no restart needed.

### Alternative: Use `/config` inside Claude Code

The `/config` slash command inside a running Claude Code session opens an interactive settings menu that works correctly — bypassing the broken CLI flag.

### Key Confusion Point

Most tutorials and blog posts (including Anthropic's own community posts) still reference the `--global` flag syntax. This is outdated. The correct approach for Claude Code v2.0+ is direct settings.json editing or the `/config` slash command.
