---
title: "Knowledge Dashboard — Personal AI Documentation Frontend"
status: in-progress
category: "projects"
slug: "knowledge-dashboard"
created: 2026-02-14
updated: 2026-02-14
author: "claude"
tags: [nextjs, vercel, tailwind, gsd, multi-agent-design, knowledge-base, frontend, markdown-rendering]
---

# Knowledge Dashboard — Personal AI Documentation Frontend

---

## Overview

A personal knowledge dashboard that gives the user's brother (and anyone with the URL) a clean, browsable frontend for the AI Documentation Library — a growing collection of 14+ research articles and build journals covering AI tools, models, skills, agent patterns, and projects. The site renders markdown articles with full frontmatter metadata, organized by category, with client-side search. Built using Claude Code in terminal, combining the GSD (Get Shit Done) spec-driven workflow for structured execution with the multi-agent design generation pattern for creative design exploration. Deployed on Vercel with automatic rebuilds when the documentation library updates.

---

## 1. Goals & Requirements

### 1.1 What I Set Out to Build

A read-only frontend where someone with zero context on AI tooling can browse, search, and read all the documentation I've gathered. The primary user is my brother, but the site is public. The core problem: the AI Documentation Library lives as markdown files in a GitHub repo — great for version control and AI agents, terrible for human browsing. This project turns it into something a person would actually want to read.

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

---

## 2. Stack & Tools

| Layer | Choice | Why |
|-------|--------|-----|
| Framework | Next.js (App Router) | Best Vercel integration, static generation for markdown, React ecosystem |
| Styling | Tailwind CSS | All collected design skills are Tailwind-native — zero translation needed |
| Content source | GitHub API at build time | Decoupled from the documentation repo, auto-rebuild via webhook |
| Markdown parsing | gray-matter + next-mdx-remote | gray-matter handles YAML frontmatter, next-mdx-remote renders MDX/markdown with component overrides |
| Search | Fuse.js | Client-side fuzzy search, zero cost, works with static content |
| Deployment | Vercel | Free tier, git-push deploys, edge CDN |
| Build orchestration | GSD (Get Shit Done) | Spec-driven development with fresh context per phase — prevents context rot across a multi-phase build |
| Design exploration | Multi-Agent Design Generation (Claude Code Agent Teams) | 5 parallel agents generate distinct visual directions, each driven by a different design dimension |
| Version control | GitHub | Public repo, Vercel auto-deploy on push to main |

The stack was chosen to minimize friction at every layer. Next.js + Vercel is the zero-config deployment path. Tailwind matches the design skills already collected. GitHub API as content source means the dashboard repo and documentation repo stay decoupled — no submodule sync issues, no build coupling.

Alternatives considered:
- **Astro** — excellent for content sites but less Vercel-native than Next.js, and the user is already in the Next.js ecosystem from prior GSD projects.
- **Git submodule** for content — simpler initial setup but couples the repos and creates merge headaches when both repos are actively developed.
- **Algolia** for search — superior search quality but adds a paid dependency and API key management for a personal project.

---

## 3. Build Phases

### Phase 0: Repo & Environment Setup

**Duration:** ~15 minutes
**Goal:** Create the project repo, initialize Next.js + Tailwind, install GSD, and run `/gsd:new-project` to generate the project roadmap.

```bash
mkdir knowledge-dashboard && cd knowledge-dashboard
git init
claude  # open Claude Code in terminal
```

Then inside Claude Code:
```
/gsd:new-project
# Describe: "A personal knowledge dashboard — article-based site where my brother
# can browse AI tools, findings, and workflows I've documented. Next.js, Tailwind,
# deployed on Vercel. Content sourced from my AI Documentation Library GitHub repo."
```

GSD asks clarifying questions. Key decisions at this stage:
- Content source: GitHub API at build time (recommended)
- Navigation: Category-based with search
- Interactivity: Read-only articles with filters and search

**Outcome:** Initialized repo with Next.js + Tailwind, GSD roadmap generated with phased task breakdown.

### Phase 1: Design Exploration via Multi-Agent

**Duration:** ~45 minutes
**Goal:** Generate 5 visually distinct dashboard designs using the multi-agent pattern, then select a direction.

This phase follows the documented workflow from [multi-agent-design-generation](../agents/multi-agent-design-generation.md):

1. Collect design skills into `.claude/skills/design-sources/`
2. Synthesize into a single mega-skill at `.claude/skills/synthesized-design/SKILL.md`
3. Enable Agent Teams: `export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`
4. Switch to plan mode (Shift+Tab) and deploy 5 agents:
   - Agent 1: Typography-driven (editorial/magazine feel)
   - Agent 2: Color/depth-driven (dark mode, layered cards)
   - Agent 3: Motion-driven (scroll animations, micro-interactions)
   - Agent 4: Spatial/layout-driven (bento grid, asymmetric cards)
   - Agent 5: Conversion-driven (clear hierarchy, search prominent)
5. Review variants, pick direction (or frankenmix elements from multiple)

**Outcome:** One chosen design direction with code/styles ready to feed into GSD.

### Phase 2: GSD Build Execution — Core

**Duration:** ~2-3 hours
**Goal:** Build the full application across GSD phases.

```
/gsd:discuss-phase 1   # Content pipeline + core layout
/gsd:plan-phase 1
/gsd:execute-phase 1
/gsd:verify-work 1

/gsd:discuss-phase 2   # Category pages + navigation + search
/gsd:plan-phase 2
/gsd:execute-phase 2
/gsd:verify-work 2

/gsd:discuss-phase 3   # Polish — responsive, animations, dark mode
/gsd:plan-phase 3
/gsd:execute-phase 3
/gsd:verify-work 3
```

Each GSD phase runs tasks via subagents in fresh 200k-token context windows, with automatic git commits per task.

**Outcome:** Fully functional dashboard with content pipeline, navigation, search, and polish.

### Phase 3: Content Integration

**Duration:** ~30 minutes
**Goal:** Wire up the AI Documentation Library as the content source.

Implementation approach (GitHub API at build time):
1. `getStaticProps` fetches markdown files from `fatherfilth/AI-Documentation-Library` via GitHub Contents API
2. `gray-matter` parses YAML frontmatter (title, category, slug, tags, status)
3. `next-mdx-remote` renders markdown body with custom component overrides for tables, code blocks, and callouts
4. Static paths generated per article: `/docs/[category]/[slug]`
5. Category index pages aggregate articles by frontmatter category

**Outcome:** All 14+ articles from the documentation library rendering correctly on the dashboard.

### Phase 4: Deploy

**Duration:** ~15 minutes
**Goal:** Push to GitHub, connect to Vercel, verify production build.

```bash
gh repo create knowledge-dashboard --public --source=. --push
npx vercel
```

Set up webhook: pushes to `AI-Documentation-Library` main branch trigger a Vercel rebuild of the dashboard.

**Outcome:** Live URL, auto-deploying on changes to either repo.

---

## 4. Key Decisions

| # | Decision | Options Considered | Rationale | Outcome |
|---|----------|-------------------|-----------|----------|
| 1 | Next.js over Astro | Next.js, Astro, Remix | Best Vercel integration, App Router for layouts, React ecosystem compatibility with design skills | Pending — build not started |
| 2 | GitHub API over submodule for content | GitHub API at build time, Git submodule, Copy files manually | Decoupled repos, no merge conflicts, webhook-triggered rebuilds | Pending |
| 3 | Multi-agent for design, GSD for build | Multi-agent only, GSD only, Combined | Multi-agent excels at creative divergence (design), GSD excels at structured execution (build) — different strengths for different phases | Pending |
| 4 | Fuse.js over Algolia for search | Fuse.js, Algolia, Pagefind, none | Zero cost, no API keys, sufficient for ~50 articles, client-side means no server dependency | Pending |
| 5 | Tailwind over CSS Modules | Tailwind, CSS Modules, styled-components | Design skills are all Tailwind-native, no translation needed | Pending |
| 6 | gray-matter + next-mdx-remote | contentlayer, remark+rehype, custom parser | gray-matter is the standard for YAML frontmatter, next-mdx-remote handles component overrides for tables and code blocks | Pending |

---

## 5. Challenges & Solutions

| # | Challenge | What I Tried | What Worked | Impact |
|---|-----------|-------------|-------------|--------|
| 1 | Combining two workflow approaches (multi-agent + GSD) that weren't designed to work together | Planning the handoff point: multi-agent produces design, GSD consumes it as spec input | Pending — identified the handoff point (design selection → GSD project spec) but not yet executed | High |
| 2 | Content sourcing from a separate repo without coupling the builds | Evaluated submodule vs. GitHub API vs. file copy | Pending — GitHub API at build time selected as approach, not yet implemented | Medium |
| 3 | Ensuring the dashboard auto-updates when new docs are added to the library | Need webhook or rebuild trigger between repos | Pending — Vercel deploy hooks identified as the mechanism | Medium |

---

## 6. Results

### 6.1 What Was Built

Build not yet started. This document captures the planning phase — architecture decisions, phase breakdown, and the combined GSD + multi-agent workflow design.

### 6.2 Against Success Criteria

| Criterion | Status |
|-----------|--------|
| Brother can browse topics by category | Not started |
| Articles render with proper formatting | Not started |
| Search works | Not started |
| Auto-updates on new docs | Not started |
| Looks bookmarkable | Not started |

---

## 7. Retrospective

### 7.1 What Worked Well

Pending — build not yet executed. Planning phase captured the combined workflow clearly.

### 7.2 What I'd Do Differently

Pending — will be updated after build execution.

### 7.3 Surprises

Pending — will be updated after build execution.

---

## 8. Open Questions & Next Steps

- Will the multi-agent → GSD handoff be smooth, or will the design variant code need significant refactoring before GSD can consume it as a spec?
- Is GitHub Contents API fast enough for build-time fetching of 14+ markdown files, or will we need to cache/batch the requests?
- How will the dashboard handle the documentation library's internal cross-references (relative links between docs)? These need URL rewriting.
- Should the dashboard expose the transcript archives alongside the doc pages, or keep those as repo-only artifacts?
- What happens when the library grows to 50+ or 100+ articles — does the static generation approach still scale on Vercel free tier?
- Will the chosen design variant's Tailwind classes conflict with next-mdx-remote's rendered markdown?

---

## References

1. [GSD — Get Shit Done Documentation](../tools/get-shit-done-gsd.md) — The spec-driven development tool used for build execution
2. [Multi-Agent Design Generation](../agents/multi-agent-design-generation.md) — The parallel design exploration pattern used for Phase 1
3. [Frontend Design Skills Catalog](../skills/frontend-design-in-build-workflow.md) — The 18-skill catalog and tier system for design skills
4. [AI Frontend Design Workflows](../skills/ai-frontend-design-workflows.md) — Five benchmarked workflows for AI-generated frontends
5. [Next.js Documentation](https://nextjs.org/docs) — Framework documentation
6. [Vercel Deployment Documentation](https://vercel.com/docs) — Deployment platform
7. [gray-matter — npm](https://www.npmjs.com/package/gray-matter) — YAML frontmatter parser
8. [next-mdx-remote — GitHub](https://github.com/hashicorp/next-mdx-remote) — MDX/markdown rendering for Next.js
9. [Fuse.js — Lightweight Fuzzy Search](https://www.fusejs.io/) — Client-side search library
10. [AI Documentation Library — GitHub](https://github.com/fatherfilth/AI-Documentation-Library) — The content source

---

## Appendix

### A. Key Configs & Commands

**Project initialization sequence:**

```bash
# Create and enter project directory
mkdir knowledge-dashboard && cd knowledge-dashboard
git init

# Open Claude Code
claude

# Inside Claude Code:
/gsd:new-project
# Describe the project when prompted

# For design phase, enable Agent Teams:
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

**Content fetching pattern (Next.js):**

```javascript
// lib/content.js — conceptual, to be implemented
import matter from 'gray-matter';

const REPO = 'fatherfilth/AI-Documentation-Library';
const CATEGORIES = ['models', 'tools', 'skills', 'repos', 'agents', 'projects'];

export async function getAllArticles() {
  const articles = [];
  for (const category of CATEGORIES) {
    const res = await fetch(
      `https://api.github.com/repos/${REPO}/contents/docs/${category}`,
      { headers: { 'Accept': 'application/vnd.github.v3+json' } }
    );
    const files = await res.json();
    for (const file of files.filter(f => f.name.endsWith('.md'))) {
      const content = await fetch(file.download_url).then(r => r.text());
      const { data, content: body } = matter(content);
      articles.push({ ...data, body, category });
    }
  }
  return articles;
}
```

**GSD phase execution pattern:**

```bash
/gsd:discuss-phase N   # Clarify implementation preferences
/gsd:plan-phase N      # Generate atomic task plans
/gsd:execute-phase N   # Run tasks via subagents
/gsd:verify-work N     # Confirm features work
```

### B. Related Documents

- [get-shit-done-gsd](../tools/get-shit-done-gsd.md) — Build execution tool
- [multi-agent-design-generation](../agents/multi-agent-design-generation.md) — Design exploration pattern
- [frontend-design-in-build-workflow](../skills/frontend-design-in-build-workflow.md) — Design skills catalog
- [ai-frontend-design-workflows](../skills/ai-frontend-design-workflows.md) — Design workflow benchmarks
- [ai-frontend-design-tools](../tools/ai-frontend-design-tools.md) — Design tool landscape

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial build journal — planning phase with architecture decisions and phase breakdown | claude |