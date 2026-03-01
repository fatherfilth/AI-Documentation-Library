---
title: "Knowledge Dashboard — Personal AI Documentation Frontend"
status: in-progress
category: "projects"
slug: "knowledge-dashboard"
created: 2026-02-14
updated: 2026-02-15
author: "claude"
tags: [nextjs, vercel, tailwind, gsd, multi-agent-design, knowledge-base, frontend, markdown-rendering, leaderboard]
---

# Knowledge Dashboard — Personal AI Documentation Frontend

---

## Overview

A personal knowledge dashboard that gives the user's brother (and anyone with the URL) a clean, browsable frontend for the AI Documentation Library — a growing collection of 19+ research articles and build journals covering AI tools, models, skills, agent patterns, and projects. The site renders markdown articles with full frontmatter metadata, organized by category, with client-side search and a personal stack leaderboard. Built using Claude Code in terminal, combining the GSD (Get Shit Done) spec-driven workflow for structured execution with the multi-agent design generation pattern for creative design exploration. Deployed on Vercel with automatic rebuilds when the documentation library updates.

---

## 1. Goals & Requirements

### 1.1 What I Set Out to Build

A read-only frontend where someone with zero context on AI tooling can browse, search, and read all the documentation I've gathered. The primary user is my brother, but the site is public. The core problem: the AI Documentation Library lives as markdown files in a GitHub repo — great for version control and AI agents, terrible for human browsing. This project turns it into something a person would actually want to read.

Additionally, the site includes a personal stack leaderboard page — a ranked registry of every tool, skill, model, application, and workflow encountered across documentation conversations, showing what's been used vs. only researched.

### 1.2 Constraints

- **Budget:** Zero ongoing cost — Vercel free tier for hosting, no paid APIs for search or content delivery.
- **Timeline:** Single session build — the combined GSD + multi-agent approach should get a deployable v1 in 4-5 hours.
- **Tech constraints:** Must consume markdown with YAML frontmatter as-is from the documentation library. No content restructuring — the existing `docs/<category>/<slug>.md` format is the source of truth.
- **Skill constraints:** Terminal-first workflow using Claude Code. No manual Figma design phase.

### 1.3 Success Criteria

1. Brother can open a URL and browse all documented topics by category
2. Articles render with proper formatting: tables, code blocks, headings, links
3. Search works — type a topic, find the article
4. Site auto-updates when new docs are added to the library
5. Looks good enough that someone would bookmark it
6. Leaderboard page shows ranked personal stack with used/researched status

---

## 2. Stack & Tools

| Layer | Choice | Why |
|-------|--------|-----|
| Framework | Next.js (App Router) | Best Vercel integration, static generation for markdown, React ecosystem |
| Styling | Tailwind CSS | All collected design skills are Tailwind-native — zero translation needed |
| Content source | GitHub API at build time | Decoupled from the documentation repo, auto-rebuild via webhook |
| Markdown parsing | gray-matter + react-markdown (planned) | gray-matter handles YAML frontmatter; react-markdown avoids MDX/JSX parsing issues |
| Search | Fuse.js | Client-side fuzzy search, zero cost, works with static content |
| Deployment | Vercel | Free tier, git-push deploys, edge CDN |
| Build orchestration | GSD (Get Shit Done) | Spec-driven development with fresh context per phase — prevents context rot across a multi-phase build |
| Design exploration | Multi-Agent Design Generation (Claude Code Agent Teams) | 5 parallel agents generate distinct visual directions, each driven by a different design dimension |
| Version control | GitHub | Public repo at fatherfilth/knowledge-dashboard, Vercel auto-deploy on push to main |

### Stack Change: MDX → react-markdown

Originally chose `next-mdx-remote` for markdown rendering, but hit a critical issue: MDX tries to parse standard markdown characters (`<`, `>`, `{}`) as JSX, breaking articles that contain comparison operators, code snippets, or HTML-like syntax. The fix is switching to `react-markdown` + `remark-gfm` which treats content as plain markdown.

---

## 3. Build Phases

### Phase 0: Repo & Environment Setup ✅

**Duration:** ~30 minutes
**Status:** Complete

Created the GitHub repository and configured the development environment:

```bash
mkdir knowledge-dashboard && cd knowledge-dashboard
git init
winget install GitHub.cli          # GitHub CLI not pre-installed
gh auth login                      # Authenticated via browser
gh repo create knowledge-dashboard --public --source=. --push
git branch -M main                 # Renamed default branch from master
git config --global user.email "tristen@totempowered.com"
```

**Challenges encountered:**
- GitHub CLI (`gh`) wasn't installed → installed via `winget`
- Remote not set up after first `gh repo create` attempt → manually added remote, re-ran
- Git config missing user.email → set globally
- Default branch was `master` → renamed to `main`

**Outcome:** Public repo at `github.com/fatherfilth/knowledge-dashboard`, connected to origin, pushing to main.

### Phase 1: GSD Initialization ✅

**Duration:** ~20 minutes
**Status:** Complete

Opened Claude Code and ran `/gsd:new-project`. GSD generated planning artifacts:

- `.planning/REQUIREMENTS.md` — 25 requirements across 8 phases
- `.planning/ROADMAP.md` — 8-phase breakdown (more granular than original 4-phase plan)
- `.planning/research/` — Research findings

**GSD 8-Phase Breakdown:**
1. Project Foundation (Next.js + Tailwind + content pipeline)
2. Core Features (category pages, article rendering)
3. Search Implementation (Fuse.js)
4. Navigation & UI Components
5. Content Integration (GitHub API fetching)
6. Article Page Polish (markdown rendering, metadata sidebar)
7. Tag Discovery (tag-based navigation)
8. Design Polish (responsive, dark mode, performance)

**Key decisions during GSD planning:**
- Aesthetic direction: "Dark & technical"
- Status badges (in-progress/complete/stable): "prominent" visibility
- Content structure follows `docs/<category>/<slug>.md` from the library

### Phase 2: GSD Build Execution — Phases 1-3 ✅

**Duration:** ~2 hours
**Status:** Complete (with known issue)

Executed GSD phases using the standard loop:
```
/gsd:discuss-phase N → /gsd:plan-phase N → /clear → /gsd:execute-phase N → /gsd:verify-work N
```

Used `claude --dangerously-skip-permissions` to avoid constant approval prompts for git commits and bash commands during GSD execution.

**Phase 1 verification note:** GSD asked about GITHUB_TOKEN for authenticated API access (5000 req/hr vs 60 unauthenticated). Skipped local token setup, configured in Vercel environment variables instead.

**Key learning:** `/clear` between phases prevents context rot — especially important before `/gsd:execute-phase`. GSD commits locally but doesn't auto-push; manual `git push` required.

**Known issue — MDX compilation error:**
Build failing on `/tools/cleo-memory-and-statusline` page. Error: `Unexpected character '7' (U+0037) before name` — MDX parsing markdown content as JSX. Attempted fix with `format: 'md'` option didn't resolve. Need to replace `next-mdx-remote` with `react-markdown` + `remark-gfm`.

### Phase 3: Vercel Deployment ✅

**Duration:** ~15 minutes
**Status:** Deployed (with build error on some pages)

Project deployed to Vercel with auto-deploy on push to main. Configured:
- `GITHUB_TOKEN` as Vercel environment variable (personal access token with `public_repo` read scope, marked sensitive)
- Auto-deploy triggered on every push to main branch

Site is live but some article pages fail due to the MDX parsing issue.

### Phase 4: Design Skills Collection ✅

**Duration:** ~30 minutes
**Status:** Complete

Collected 5 design skill sources for the multi-agent design phase:

| Source | Location | Status |
|--------|----------|--------|
| Anthropic official frontend-design | `.claude/skills/design-sources/anthropic-official-frontend-design.md` | ✅ Downloaded |
| ehmo platform-design-skills (300+ rules) | `.claude/skills/design-sources/platform-design-skills/` | ✅ Cloned |
| UI UX Pro Max | `.claude/skills/ui-ux-pro-max/SKILL.md` | ✅ Installed via `uipro init` |
| mosif16 product-frontend-design | `.agents/skills/product-frontend-design/SKILL.md` | ✅ Installed via playbooks |
| arvindand ui-ux-design | `.agents/skills/ui-ux-design/SKILL.md` + `REFERENCES.md` | ✅ Installed via playbooks |

**Failed sources (not blocking):**
- Google Labs skills (`google-labs-code/*`) — repos don't exist as individual packages
- eddiebe147/claude-settings — repo not found (may have been deleted)
- Anthropic cookbook aesthetics — URL 404

**Outcome:** 5 substantial skill sources covering typography, color, layout, accessibility, components, and anti-patterns.

### Phase 5: Design Skill Synthesis ✅

**Duration:** ~3 minutes (Claude Code processing)
**Status:** Complete

Ran synthesis prompt in Claude Code after `/clear`:

```
Read every design skill file in .claude/skills/ AND .agents/skills/
(including all subdirectories)...
Then create a single synthesized skill at .claude/skills/synthesized-design/SKILL.md
organized ONLY by design dimension...
```

**Output:** `.claude/skills/synthesized-design/SKILL.md`
- Sources consumed: 7 files across both skill directories
- Organized into 9 dimensions: Typography, Color & Theme, Layout, Motion, Backgrounds, Accessibility, Conversion, Components, Anti-Patterns (25+ rules)
- Contradictions resolved (e.g., "system fonts as safe choice" vs "never use system fonts" → kept more specific guidance)

### Phase 6: Multi-Agent Design Generation 🔄

**Duration:** In progress
**Status:** Setting up

Environment configured:
```powershell
set CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
claude --dangerously-skip-permissions
```

5 agents to deploy in plan mode (Shift+Tab twice):
- Agent 1: Typography-driven (editorial/magazine feel)
- Agent 2: Color/depth-driven (dark mode, layered cards)
- Agent 3: Motion-driven (scroll animations, micro-interactions)
- Agent 4: Spatial/layout-driven (bento grid, asymmetric cards)
- Agent 5: Conversion-driven (clear hierarchy, search prominent)

Each agent reads `.claude/skills/synthesized-design/SKILL.md` first, declares fonts/palette/layout before implementation, lead agent confirms no duplicates.

Output: `designs/variant-{1-5}/` (homepage.html + article.html each)

### Phase 7: Design Selection & GSD Resume ⏳

**Status:** Not started — waiting for Phase 6 completion

After picking a design variant:
1. `/clear`
2. `/gsd:discuss-phase N` — reference chosen design variant as visual spec
3. `/gsd:plan-phase N` → `/gsd:execute-phase N`
4. Continue through remaining GSD phases

### Phase 8: Leaderboard Page ⏳

**Status:** Not started — added as new requirement

Leaderboard page fetches `docs/_leaderboard/data.yaml` from GitHub API and renders:
- Grouped by type (Tool → Skill → Model → Application → Workflow) with icons
- Within each type, grouped by subcategory
- Ranked tables: Rank, Name, Status (🟢 Used / 🔵 Researched), Use Case, Verdict
- Links to doc pages where they exist
- Added to site navigation

Prompt provided to Claude Code for GSD integration:
```
Read the leaderboard system from the AI Documentation Library...
Add a Leaderboard page to the knowledge dashboard that:
1. Fetches data.yaml from the GitHub API
2. Parses the YAML entries
3. Groups entries by type with icons
...
```

---

## 4. Key Decisions

| # | Decision | Options Considered | Rationale | Outcome |
|---|----------|-------------------|-----------|----------|
| 1 | Next.js over Astro | Next.js, Astro, Remix | Best Vercel integration, App Router for layouts, React ecosystem compatibility with design skills | ✅ Working — deployed on Vercel |
| 2 | GitHub API over submodule for content | GitHub API at build time, Git submodule, Copy files manually | Decoupled repos, no merge conflicts, webhook-triggered rebuilds | ✅ Implemented in GSD Phase 1 |
| 3 | Multi-agent for design, GSD for build | Multi-agent only, GSD only, Combined | Multi-agent excels at creative divergence (design), GSD excels at structured execution (build) — different strengths for different phases | 🔄 In progress — GSD phases 1-3 done, multi-agent design launching |
| 4 | Fuse.js over Algolia for search | Fuse.js, Algolia, Pagefind, none | Zero cost, no API keys, sufficient for ~50 articles, client-side means no server dependency | ✅ Implemented in GSD Phase 3 |
| 5 | Tailwind over CSS Modules | Tailwind, CSS Modules, styled-components | Design skills are all Tailwind-native, no translation needed | ✅ Working |
| 6 | react-markdown over next-mdx-remote | next-mdx-remote, react-markdown + remark-gfm, contentlayer | next-mdx-remote breaks on standard markdown chars (`<`, `>`, `{}`); react-markdown treats content as plain markdown | ⏳ Switch pending — MDX error blocking some pages |
| 7 | `--dangerously-skip-permissions` for GSD | Default permissions, granular allow-list, skip all | GSD needs constant bash/git access; approval prompts break flow | ✅ Essential for smooth GSD execution |
| 8 | Skip local GITHUB_TOKEN, configure in Vercel | Local .env, Vercel env vars, both | Dev works with unauthenticated rate limit (60/hr), production needs authenticated (5000/hr) | ✅ Configured in Vercel |
| 9 | Add leaderboard page | Static leaderboard, dynamic from YAML, skip entirely | data.yaml already exists with full schema; dashboard should surface it | ⏳ Added as Phase 8 requirement |

---

## 5. Challenges & Solutions

| # | Challenge | What I Tried | What Worked | Impact |
|---|-----------|-------------|-------------|--------|
| 1 | GitHub CLI not installed on Windows | Searched for `gh` command | `winget install GitHub.cli` — one command install | Low |
| 2 | Git remote not configured after `gh repo create` | First attempt didn't set remote properly | Re-ran `gh repo create --source=. --push` which set up remote and pushed | Low |
| 3 | Git config missing user.email | Commit failed | `git config --global user.email "tristen@totempowered.com"` | Low |
| 4 | Default branch `master` instead of `main` | Branch mismatch with Vercel defaults | `git branch -M main` — renamed before first push | Low |
| 5 | MDX compilation error — JSX parsing markdown | Added `format: 'md'` to next-mdx-remote serialize options | **Not yet resolved** — need to switch to react-markdown + remark-gfm entirely | High |
| 6 | GSD constant permission prompts | Default Claude Code permissions | `claude --dangerously-skip-permissions` — essential for GSD workflow | Medium |
| 7 | GSD doesn't auto-push to GitHub | Expected auto-push after commits | Manual `git push` after GSD phases — expected behavior, not a bug | Low |
| 8 | Google Labs design skill repos don't exist | Tried `npx playbooks add skill google-labs-code/*` | Repos not found — not individual packages. Not blocking (5 other sources sufficient) | Low |
| 9 | PowerShell `&&` not valid separator | `npm install -g uipro-cli && uipro install` | Use `;` instead: `npm install -g uipro-cli; uipro init` (also `init` not `install`) | Low |
| 10 | Combining GSD + multi-agent in single build | Needed to identify handoff point | Multi-agent runs between GSD scaffolding (phases 1-3) and design phase. GSD Phase 8 is polish, not initial design | Medium |

---

## 6. Results

### 6.1 What Was Built (so far)

- ✅ Next.js project scaffolded with Tailwind
- ✅ Content pipeline fetching from GitHub API
- ✅ Category pages rendering article lists
- ✅ Article pages rendering markdown (with MDX issue on some)
- ✅ Fuse.js search implemented
- ✅ Deployed to Vercel with auto-deploy
- ✅ 5 design skill sources collected and synthesized
- ✅ Leaderboard data populated (35 entries across 7 subcategories)
- 🔄 5-agent design generation in progress
- ⏳ Design application to components
- ⏳ Leaderboard dashboard page
- ⏳ react-markdown migration
- ⏳ Responsive polish, dark mode, performance

### 6.2 Against Success Criteria

| Criterion | Status |
|-----------|--------|
| Brother can browse topics by category | ✅ Working (deployed on Vercel) |
| Articles render with proper formatting | ⚠️ Partial — most work, some fail on MDX parsing |
| Search works | ✅ Fuse.js implemented |
| Auto-updates on new docs | ✅ Vercel auto-deploy on push |
| Looks bookmarkable | 🔄 Design phase in progress |
| Leaderboard page | ⏳ Data populated, page not yet built |

---

## 7. Retrospective

### 7.1 What Worked Well

- **GSD is genuinely incredible** for structured builds. The fresh-context-per-task model produces consistent quality across phases. The 5-command loop (`discuss → plan → clear → execute → verify`) creates a natural rhythm.
- **Design skill collection** worked smoothly once the right sources were identified. The synthesis step compressed 7 files into one actionable skill document.
- **Vercel deployment** was frictionless — push to main and it's live. Environment variables for the GitHub token just worked.
- **`--dangerously-skip-permissions`** is essential for GSD. Without it, the constant approval prompts make multi-phase execution unbearable.

### 7.2 What I'd Do Differently

- **Skip `next-mdx-remote` entirely** — go straight to `react-markdown` + `remark-gfm`. MDX's JSX parsing is a trap for plain markdown content.
- **Set up GITHUB_TOKEN locally too** — the 60 req/hr unauthenticated limit can slow dev iteration.
- **Research design skill sources before collecting** — several repos didn't exist, wasting time on 404s.

### 7.3 Surprises

- GSD generated **8 phases** instead of the 3-4 originally planned — more granular than expected, which is actually better for context isolation.
- The design skill synthesis step only took ~3 minutes and consumed 7 files — faster and more effective than expected.
- PowerShell's `&&` limitation caught me off guard — need `;` as separator.
- Several "recommended" GitHub repos for design skills simply don't exist — community references can be stale.

---

## 8. Open Questions & Next Steps

### Open Questions
- Will the multi-agent design variants produce usable code, or will they need heavy refactoring for Next.js/Tailwind integration?
- How will the react-markdown switch affect existing pages that currently work with next-mdx-remote?
- Is the YAML-based leaderboard the right format, or should it move to JSON for easier dashboard consumption?
- How will cross-references between docs (relative markdown links) resolve in the dashboard URL structure?

### Next Steps
1. Deploy 5-agent design generation and select a variant
2. Apply chosen design to the dashboard via GSD design phase
3. Switch from next-mdx-remote to react-markdown + remark-gfm
4. Build the leaderboard page
5. Complete remaining GSD phases (navigation polish, tag discovery, responsive/dark mode)
6. Set up Vercel deploy hook for cross-repo rebuilds

---

## References

1. [GSD — Get Shit Done Documentation](../tools/get-shit-done-gsd.md) — The spec-driven development tool used for build execution
2. [Multi-Agent Design Generation](../agents/multi-agent-design-generation.md) — The parallel design exploration pattern used for design phase
3. [Frontend Design Skills Catalog](../skills/frontend-design-in-build-workflow.md) — The 18-skill catalog and tier system for design skills
4. [AI Frontend Design Workflows](../skills/ai-frontend-design-workflows.md) — Five benchmarked workflows for AI-generated frontends
5. [AI Frontend Design Tools](../tools/ai-frontend-design-tools.md) — Design tool landscape
6. [Next.js Documentation](https://nextjs.org/docs) — Framework documentation
7. [Vercel Deployment Documentation](https://vercel.com/docs) — Deployment platform
8. [gray-matter — npm](https://www.npmjs.com/package/gray-matter) — YAML frontmatter parser
9. [react-markdown — GitHub](https://github.com/remarkjs/react-markdown) — Markdown rendering (replacing next-mdx-remote)
10. [Fuse.js — Lightweight Fuzzy Search](https://www.fusejs.io/) — Client-side search library
11. [AI Documentation Library — GitHub](https://github.com/fatherfilth/AI-Documentation-Library) — The content source
12. [Knowledge Dashboard — GitHub](https://github.com/fatherfilth/knowledge-dashboard) — The dashboard repo

---

## Appendix

### A. Key Configs & Commands

**Repo setup sequence (Windows PowerShell):**

```powershell
mkdir knowledge-dashboard; cd knowledge-dashboard
git init
winget install GitHub.cli
gh auth login
gh repo create knowledge-dashboard --public --source=. --push
git branch -M main
git config --global user.email "tristen@totempowered.com"
git commit --allow-empty -m "init"
git push -u origin main
```

**GSD initialization:**

```bash
claude --dangerously-skip-permissions
/gsd:new-project
# Describe project when prompted
```

**GSD phase execution pattern:**

```bash
/gsd:discuss-phase N   # Clarify implementation preferences
/gsd:plan-phase N      # Generate atomic task plans
/clear                 # Reset context before execution
/gsd:execute-phase N   # Run tasks via subagents
/gsd:verify-work N     # Confirm features work
```

**Design skill collection (Tab 2 — regular terminal):**

```powershell
mkdir -p .claude/skills/design-sources
npx playbooks add skill mosif16/codex-skills --skill frontend-design
npx playbooks add skill arvindand/agent-skills --skill ui-ux-design
git clone https://github.com/ehmo/platform-design-skills .claude/skills/design-sources/platform-design-skills
npm install -g uipro-cli; uipro init
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/anthropics/claude-code/main/plugins/frontend-design/skills/frontend-design/SKILL.md" -OutFile ".claude/skills/design-sources/anthropic-official-frontend-design.md"
```

**Multi-agent design deployment (Tab 1 — Claude Code):**

```powershell
# After /exit from synthesis:
set CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
claude --dangerously-skip-permissions
# Shift+Tab twice → plan mode → paste 5-agent prompt
```

**Vercel environment variables:**

| Variable | Value | Scope |
|----------|-------|-------|
| `GITHUB_TOKEN` | Personal access token (public_repo read) | Production, Preview, Development |

### B. Related Documents

- [get-shit-done-gsd](../tools/get-shit-done-gsd.md) — Build execution tool
- [multi-agent-design-generation](../agents/multi-agent-design-generation.md) — Design exploration pattern
- [frontend-design-in-build-workflow](../skills/frontend-design-in-build-workflow.md) — Design skills catalog
- [ai-frontend-design-workflows](../skills/ai-frontend-design-workflows.md) — Design workflow benchmarks
- [ai-frontend-design-tools](../tools/ai-frontend-design-tools.md) — Design tool landscape
- [Leaderboard](../_leaderboard/README.md) — Personal stack rankings displayed on dashboard

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial build journal — planning phase with architecture decisions and phase breakdown | claude |
| 2026-02-15 | Major update — repo setup, GSD phases 1-3 complete, Vercel deployed, design skills collected and synthesized, MDX error documented, leaderboard requirement added, retrospective started | claude |
