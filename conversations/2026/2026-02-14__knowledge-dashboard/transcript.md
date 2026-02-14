---
topic: knowledge-dashboard
category: projects
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "Planning session for a personal knowledge dashboard — a frontend site where the user's brother can browse all documented AI tools, findings, and workflows from the AI Documentation Library."
related_topics: []
---

# Transcript: Knowledge Dashboard Build Planning

## Context

The user has been building an AI Documentation Library on GitHub, documenting their learning journey across AI models, tools, skills, agent patterns, and projects. The library contains 14+ research articles and build journals written in markdown with YAML frontmatter, organized by category.

## The Request

**User:** I want to start a documented project to create a custom frontend article/personal tooling dashboard where my brother can view all of the information and knowledge I've gathered. All the tools I've used and for what and which worked.

I want to use Claude Code to build this. In terminal. With the get shit done project and the multi design agent combined skill approach we discussed. All stored on GitHub and deployed on Vercel. First give me the steps and then document the process.

## Key Context From Prior Conversations

The user has previously documented two approaches they want to combine for this build:

1. **GSD (Get Shit Done)** — A meta-prompting system for Claude Code that breaks projects into phases and executes each in a fresh context window via subagents. Documented at `docs/tools/get-shit-done-gsd.md`.

2. **Multi-Agent Design Generation** — A workflow using Claude Code Agent Teams to generate 5 visually distinct frontend designs in parallel, each driven by a different design dimension (typography, color, motion, spatial, conversion). Documented at `docs/agents/multi-agent-design-generation.md`.

The user wants to combine these: multi-agent for design exploration, then GSD for structured build execution.

## Build Plan Discussion

### Architecture Decisions

**Framework:** Next.js — best Vercel integration, works with the Tailwind-native design skills the user has already collected.

**Content Source:** Two options discussed:
- **Option A (recommended):** GitHub API at build time via `getStaticProps` — fetches markdown from `AI-Documentation-Library` repo, parses frontmatter with `gray-matter`, renders with `next-mdx-remote`. Decoupled repos, auto-rebuild via webhook.
- **Option B:** Git submodule — simpler but couples the repos.

**Styling:** Tailwind CSS — all the design skills from the multi-agent workflow are Tailwind-native.

**Search:** Fuse.js for client-side search — zero cost, works with static content.

### Phase Breakdown

| Phase | What | Duration Estimate |
|-------|------|-------------------|
| 0 | Repo setup, Next.js init, GSD new-project | 15 min |
| 1 | Design exploration — 5 agents via multi-agent pattern | 45 min |
| 2 | GSD build — content pipeline, layout, navigation, search | 2-3 hours |
| 3 | Content integration — wire up AI Documentation Library as source | 30 min |
| 4 | Deploy to Vercel | 15 min |
| 5 | Share the URL | 5 min |

### Combined Workflow: Multi-Agent + GSD

The key insight is that these two approaches serve different phases:
- **Multi-agent design generation** handles Phase 1 (creative exploration, design divergence)
- **GSD** handles Phase 2 onward (structured spec → plan → execute pipeline)

The handoff point is after design selection — the chosen variant's code/style becomes input to GSD's project specification.

### What the Brother Will See

A clean, browsable site organized by 6 categories (models, tools, skills, repos, agents, projects). Each article rendered from the library's markdown with confusion points tables, code snippets, and references. Search bar for quick topic lookup. Latest additions feed on the homepage.

## Decisions Made

1. **Next.js + Tailwind + Vercel** as the stack
2. **GitHub API at build time** for content sourcing (recommended over submodule)
3. **Combined multi-agent → GSD workflow** for the build process
4. **Fuse.js** for client-side search
5. **gray-matter + next-mdx-remote** for markdown processing

## References Discussed

- GSD documentation: `docs/tools/get-shit-done-gsd.md`
- Multi-agent design generation: `docs/agents/multi-agent-design-generation.md`
- Frontend design skills catalog: `docs/skills/frontend-design-in-build-workflow.md`
- AI frontend design workflows: `docs/skills/ai-frontend-design-workflows.md`
