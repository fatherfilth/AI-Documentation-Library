---
title: "Setting Up Projects in Claude Code with CLAUDE.md"
status: stable
category: "tools"
slug: "claude-code-project-setup"
created: 2026-02-14
updated: 2026-02-14
author: "claude"
tags: [claude-code, claude-md, project-setup, init, project-instructions, configuration]
---

# Setting Up Projects in Claude Code with CLAUDE.md

---

## Abstract

Claude Code does not have a "projects" feature analogous to the desktop app's project containers. Instead, your project is simply your filesystem directory, and project instructions live in a `CLAUDE.md` file that Claude reads automatically on every session. This document explains the mental model shift from desktop-style project management to directory-based context, covers three methods for creating CLAUDE.md (auto-generation with `/init`, manual creation, and on-the-fly with `#`), and documents the hierarchical CLAUDE.md system that allows global, project, and directory-level instructions.

---

## 1. Introduction

### 1.1 Problem Statement

Users coming from Claude's desktop app expect a "Create Project" workflow where they upload files and set instructions inside Claude's interface. Claude Code has no such workflow — the equivalent concepts exist but are mapped differently. This mismatch causes confusion during onboarding and leads users to underutilize Claude Code's project configuration capabilities.

### 1.2 Scope

This document covers: the desktop-to-terminal project concept mapping, CLAUDE.md creation and configuration, the `/init` command, the hierarchical CLAUDE.md system, and adding external directories. It does NOT cover MCP server integration, hooks, custom slash commands, or subagent configuration.

### 1.3 Key Terms

| Term | Definition |
|------|------------|
| CLAUDE.md | A markdown file in your project root that provides persistent instructions to Claude Code, loaded automatically every session |
| `/init` | A Claude Code slash command that analyzes your codebase and auto-generates a starter CLAUDE.md file |
| Working directory | The folder from which you launch Claude Code; Claude uses it as its primary context location and can access all files within it |
| `#` (hash key) | A Claude Code shortcut that lets you add an instruction to CLAUDE.md on the fly during a session |
| Project Knowledge (desktop) | The desktop app's feature where you upload files into a project container for Claude to reference |

---

## 2. Background & Prior Work

Claude's desktop app introduced "Projects" as organizational containers with three features: uploaded files (Project Knowledge), custom instructions, and scoped conversations. This model requires the user to bring content into Claude's environment [1].

Claude Code inverts this relationship — Claude enters the user's environment. When launched from a directory, Claude Code can read all files in that directory and its subdirectories without any upload step. Project instructions moved from an in-app text box to a committed file (`CLAUDE.md`) that lives alongside the code and can be version-controlled with Git [2].

The `/init` command was introduced to reduce the cold-start problem of writing a CLAUDE.md from scratch. It scans the codebase — package files, existing documentation, configuration, and code structure — and generates a starter file [3].

---

## 3. Methodology / Approach

### 3.1 Setup

```
Prerequisites:
- Claude Code installed
- A project directory with some files in it

No additional setup required — CLAUDE.md is just a text file.
```

### 3.2 Process

1. User asked how to create a project, add files, and set instructions in the terminal
2. Discovered that Claude Code has no "project" abstraction — the directory IS the project
3. Explored three methods for creating CLAUDE.md: `/init`, manual creation, and `#` shortcut
4. Documented the hierarchical CLAUDE.md system (global → project → subdirectory)
5. Covered adding external directories with `--add-dir` and `/add-dir`

---

## 4. Findings

### 4.1 The Desktop "Project" Concept Maps to Three Separate Things in Claude Code

What the desktop app bundles into a single "project" is split across three independent mechanisms in Claude Code:

| Desktop concept | Claude Code equivalent | How it works |
|----------------|----------------------|-------------|
| Create project | `mkdir my-app && cd my-app` | Your directory is your project |
| Upload files / Project Knowledge | Files are already there | Claude sees the entire directory tree automatically |
| Project instructions | `CLAUDE.md` file in project root | Loaded into Claude's system prompt every session |
| Start a chat in the project | `claude` (from inside the folder) | Each launch from a directory creates a session scoped to that directory |

This separation is actually more powerful — CLAUDE.md can be committed to Git (so the whole team shares instructions), files don't need uploading (Claude sees changes in real time), and sessions are automatically scoped to the working directory.

### 4.2 Three Methods for Creating CLAUDE.md, Each for a Different Situation

**Method 1: `/init` (auto-generation for existing projects)**

Run `/init` inside a Claude Code session. Claude examines the codebase and generates a CLAUDE.md with detected build commands, test instructions, key directories, and coding conventions. Best for onboarding to an unfamiliar or inherited codebase. Treat the output as a starting point — review and refine based on your team's actual practices.

**Method 2: Manual creation (for new projects or specific requirements)**

Create a `CLAUDE.md` file in your project root and write instructions directly. This gives full control over what Claude knows. Include coding standards, architecture notes, important commands, and project-specific warnings.

**Method 3: `#` shortcut (for iterative refinement)**

During a Claude Code session, press `#` and type an instruction. Claude adds it to your CLAUDE.md automatically. This is ideal for capturing instructions you find yourself repeating — they accumulate over time into a CLAUDE.md that reflects how you actually work.

### 4.3 CLAUDE.md Files Are Hierarchical

Claude Code reads CLAUDE.md files at multiple levels, applying them cumulatively:

```
~/.claude/CLAUDE.md              → Global (applies to ALL projects)
~/my-app/CLAUDE.md                → Project-level (applies to this project)
~/my-app/src/CLAUDE.md            → Directory-level (applies only when working in /src)
~/my-app/src/api/CLAUDE.md        → Subdirectory-level (applies only in /src/api)
```

This means you can put universal preferences ("always use TypeScript", "prefer functional components") in your global file and project-specific rules ("use Prisma for database queries", "never modify /legacy") in the project file.

### 4.4 External Directories Can Be Added Without Moving Files

If your project depends on files outside the working directory, you can add them:

```bash
# At startup
claude --add-dir ../backend-api --add-dir ~/shared/configs

# Mid-session
/add-dir /path/to/other/project
```

The working directory remains primary — additional directories provide supplementary context and access.

---

## 5. Confusion Points

| # | What I assumed | What's actually true | Impact |
|---|----------------|----------------------|--------|
| 1 | Claude Code has a "Create Project" command like the desktop app | There is no project abstraction; your directory is your project, and you just `cd` into it and run `claude` | High |
| 2 | You need to upload or explicitly add files for Claude to see them | Claude Code automatically sees all files in the working directory and subdirectories — no upload needed | High |
| 3 | Project instructions are set somewhere inside Claude's interface | Instructions live in a `CLAUDE.md` text file in your repo that you edit directly | Medium |
| 4 | CLAUDE.md is a single file per project | CLAUDE.md files are hierarchical — global, project, and directory levels all apply cumulatively | Medium |

---

## 6. Failure Modes & Gotchas

| # | Trigger | Symptom | Fix | Severity |
|---|---------|---------|-----|----------|
| 1 | CLAUDE.md exceeds ~50 lines with vague instructions | Claude stops paying attention to individual rules | Keep every line specific and actionable; remove anything that wouldn't change Claude's behavior | High |
| 2 | Running `/init` and using the output as-is | Generated file captures obvious patterns but misses team-specific nuances | Always review and refine /init output; add workflow instructions Claude couldn't infer | Medium |
| 3 | Launching `claude` from the wrong directory | Claude has no project context or sees the wrong project's files | Always verify with `pwd` before launching; exit and re-launch from the correct directory if needed | Medium |
| 4 | Including information Claude can infer from code | Wasted CLAUDE.md space (e.g., "this is a React project" when package.json says so) | Only include information Claude cannot determine by reading your code | Low |

---

## 7. Analysis

### 7.1 Strengths

The directory-as-project model eliminates upload friction entirely — Claude sees file changes in real time without re-uploading. CLAUDE.md being a regular text file means it can be committed to Git, code-reviewed, and shared across a team. The hierarchical system allows both personal global preferences and project-specific rules without conflict. The `/init` command significantly reduces onboarding time for new or inherited codebases.

### 7.2 Limitations

There is no visual interface for managing CLAUDE.md — it's a text file you edit. There's no equivalent of the desktop app's RAG mode for large file collections (Claude Code loads files into context on demand rather than using retrieval). The `#` shortcut for adding instructions is not well-documented and may not be discoverable for new users.

### 7.3 Comparison to Alternatives

| Alternative | Key difference | When to prefer it |
|-------------|----------------|--------------------||
| Desktop app Projects | GUI-based, file upload, in-app instructions | When you don't need filesystem access and prefer a visual interface |
| Cursor / VS Code with AI | IDE-managed context, settings in GUI | When you want AI integrated into an editor rather than the terminal |
| `.cursorrules` file (Cursor) | Similar concept to CLAUDE.md but for Cursor's AI | When using Cursor as your primary AI coding tool |

---

## 8. Recommendations

### 8.1 Decision Checklist

- [ ] You have a project directory with code in it
- [ ] You want Claude to understand your project's conventions across sessions
- [ ] You want instructions that are version-controlled and shareable with your team
- [ ] You're comfortable editing a markdown file

> If any box is unchecked, see [Comparison to Alternatives](#73-comparison-to-alternatives).

### 8.2 Next Steps

1. Run `/init` on your main project to generate a starter CLAUDE.md
2. Review and refine the generated file — add team conventions, remove generic content
3. Commit CLAUDE.md to your repo so teammates benefit
4. Use `#` during sessions to iteratively add instructions you find yourself repeating
5. Consider a global `~/.claude/CLAUDE.md` for preferences that apply everywhere

---

## 9. Open Questions & Unknowns

- How large can CLAUDE.md get before performance degrades? Anecdotal reports suggest ~50 lines is a practical ceiling for attention, but no official guidance exists.
- Does CLAUDE.md interact with the desktop app's embedded Claude Code sessions? The behavior when both desktop project instructions and CLAUDE.md exist is undocumented.
- Will Anthropic add a visual CLAUDE.md editor to the desktop app? No announcements.

---

## References

1. [Anthropic Help Center — How Can I Create and Manage Projects?](https://support.claude.com/en/articles/9519177-how-can-i-create-and-manage-projects)
2. [Anthropic Blog — Using CLAUDE.md Files: Customizing Claude Code for Your Codebase](https://claude.com/blog/using-claude-md-files)
3. [Claude Code Documentation — How Claude Code Works](https://code.claude.com/docs/en/how-claude-code-works)
4. [Shipyard — Claude Code CLI Cheatsheet](https://shipyard.build/blog/claude-code-cheat-sheet/)
5. [Developer Toolkit — Starting New Projects from the Terminal](https://developertoolkit.ai/en/claude-code/lessons/project-init/)

---

## Appendix

### A. Raw Data / Configs

**Example CLAUDE.md for a Next.js project:**

```markdown
# Project Context

This is a Next.js e-commerce app with TypeScript.

## Coding Standards
- Use TypeScript for all new code
- Write tests for all new functions using Jest
- Use functional components with React hooks

## Architecture
- Frontend: Next.js with TypeScript
- Backend: Node.js with Express
- Database: PostgreSQL with Prisma

## Commands
- Dev server: `npm run dev`
- Run tests: `npm test`
- Lint: `npm run lint`

## Important
- Never modify files in /legacy — that code is frozen
- All API routes need authentication middleware
```

**CLAUDE.md hierarchy example:**

```
~/.claude/CLAUDE.md              → "Always use TypeScript. Prefer functional style."
~/my-app/CLAUDE.md                → "Use Prisma for DB. Run tests with Jest."
~/my-app/src/api/CLAUDE.md        → "All endpoints need auth middleware."
```

### B. Related Documents

- [terminal-vs-desktop-for-ai](../skills/terminal-vs-desktop-for-ai.md) — created from the same conversation
- [claude-code-parallel-sessions](../tools/claude-code-parallel-sessions.md) — created from the same conversation

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial research article | claude |
