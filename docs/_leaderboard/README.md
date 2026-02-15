# Personal Stack Leaderboard

> My ranked picks across tools, skills, models, applications, and workflows — built organically through documentation conversations.

This leaderboard tracks what I've actually used vs. what I've only researched, ranked by use case. It's updated automatically by the documentation agent whenever a tool, model, or technique comes up in conversation.

---

## How It Works

1. During any conversation, when a tool/skill/model/application/workflow is mentioned, the agent asks:
   - **Have you used this, or just researching?**
   - **If used:** Where did you use it? How would you rank it for this use case?
2. The agent updates `data.yaml` with the entry and re-renders this page.
3. Rankings are per **type + subcategory** — so Rank #1 Tool for Coding is independent of Rank #1 Tool for Design.

---

## Types

| Type | Definition | Examples |
|------|-----------|----------|
| **Tool** | Software you install, run, or subscribe to | Cursor, Claude Code, Figma, Bolt.new |
| **Skill** | A technique or methodology you apply | Prompt engineering, RAG pipelines, few-shot patterns |
| **Model** | An AI model you use directly or via API | Claude Opus 4.6, GPT-4.5, Gemini 2.5 Pro |
| **Application** | A product or platform (not a dev tool) | ChatGPT, Perplexity, Midjourney, Runway |
| **Workflow** | A multi-step process combining tools/skills | Design-to-code pipeline, multi-agent orchestration |

## Subcategories

| Subcategory | Covers |
|------------|--------|
| `coding` | Writing, debugging, reviewing, refactoring code |
| `design` | Visual design, graphic design, brand work |
| `ui-ux` | Interface design, component libraries, design systems |
| `video-gen` | AI video generation, editing, motion graphics |
| `prompt-engineering` | Prompt design, optimization, testing |
| `project-building` | End-to-end product/project creation |
| `research` | Information gathering, analysis, synthesis |
| `productivity` | Task management, automation, workflow optimization |
| `devops` | Deployment, CI/CD, infrastructure, monitoring |
| `data` | Data analysis, visualization, pipelines |
| `audio` | Music generation, voice, sound design, podcasts |
| `writing` | Content creation, copywriting, documentation |
| `other` | Anything that doesn't fit above |

---

## Leaderboard

<!-- LEADERBOARD_START -->

### 🔧 Tools

#### Coding

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Claude Code | 🟢 Used | Terminal-native AI coding agent with subagents, skills, and MCP | Primary coding tool — unmatched for agentic workflows |
| 2 | Cursor | 🔵 Researched | AI-assisted code editing with rules system and Figma MCP | Essential infrastructure for frontend AI dev — tier 1 alongside v0 |
| 3 | Playwright MCP | 🔵 Researched | Browser automation for AI agents — testing, form filling, UI verification | Official Microsoft backing, CLI+SKILLS mode is the future — heavy on tokens though |

#### Project-Building

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | GSD (Get Shit Done) | 🟢 Used | Meta-prompting system for phased project execution in Claude Code | Best I've seen — incredible for structured multi-phase builds |
| 2 | cc-sdd | 🔵 Researched | Multi-agent spec-driven development with 8 agent support | Broader agent support than GSD but heavier setup |
| 3 | spec-kit | 🔵 Researched | Formal constitution-to-tasks specification pipeline | Rigorous documentation alongside code — more formal than GSD |

#### UI/UX

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | v0 | 🔵 Researched | Prompt-to-UI generation for React/Next.js with shadcn/ui | Most mature prompt-to-UI tool for React ecosystem |
| 2 | Kombai | 🔵 Researched | Repo-aware Figma-to-code with existing component library indexing | Standout for teams with existing design systems — genuinely repo-aware |
| 3 | Google Stitch | 🔵 Researched | Free prompt-to-UI with Figma export, powered by Gemini | Strong for design ideation — still a Labs experiment |
| 4 | Locofy | 🔵 Researched | Native Figma plugin for multi-framework code generation | Best native Figma integration — requires well-structured files |
| 5 | Bolt.new | 🔵 Researched | Browser-based vibe-coding for fast prototypes | Fast for experiments — limited depth for production |
| 6 | Lovable | 🔵 Researched | Prompt-to-app with built-in Supabase integration | Great for demos — components break when you try to extend them |
| 7 | screenshot-to-code | 🔵 Researched | Open-source screenshot/Figma to HTML/React/Vue conversion | Not pixel-perfect but strong starting point — self-hostable |
| 8 | WebCrumbs | 🔵 Researched | Framework-agnostic open-source component generation | Best option for non-React/Next.js stacks |

#### Video-Gen

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | SeedDance 2.0 | 🔵 Researched | AI-generated cinematic UI demos from screenshots in minutes | Cinematic product demos from a single screenshot — game-changing speed |
| 2 | Jitter | 🔵 Researched | Figma-to-video with per-element animation control | Best layer-level UI animation from Figma — intuitive and precise |
| 3 | After Effects | 🔵 Researched | High-production showcase videos with 3D mockups and compositing | Still required for the most demanding work — steep learning curve |

#### Design

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Rive | 🔵 Researched | Interactive production animations with state machine architecture | Only tool with true runtime interactivity — state machines are the differentiator |
| 2 | LottieFiles | 🔵 Researched | Lightweight linear animations with AI Motion Copilot | Widest platform support for simple animations — AI copilot is a nice touch |
| 3 | Antigravity IDE | 🔵 Researched | Prompt-to-scroll-animated frontend generation | Interesting for scroll animation prototyping — niche but unique |
| 4 | Glif MCP | 🔵 Researched | Visual AI workflow execution — image gen, memes, ComfyUI pipelines via MCP | Only MCP for generative media — creative but pre-1.0 and small community |

#### Productivity

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Claude Squad | 🔵 Researched | TUI for managing multiple Claude Code agents with git worktree isolation | Most popular and battle-tested multi-agent manager — tmux + worktrees |
| 2 | Claudia / Opcode | 🔵 Researched | Full desktop GUI replacement for Claude Code with session time-travel | Most ambitious wrapper — session checkpoints are killer but maturity is early |
| 3 | NTM | 🔵 Researched | Terminal orchestrator for mixed AI agent types (Claude + Codex + Gemini) | Most feature-rich — mixed agent support is unique |
| 4 | Nimbalyst | 🔵 Researched | Visual WYSIWYG editor companion to Claude Code with diff approval | Best for non-developers who need visual editing alongside agents |
| 5 | Caudex | 🔵 Researched | Enhanced terminal with multi-tab splits and cost monitoring | Lightweight and keyboard-first — very early but promising |
| 6 | CLEO | 🔵 Researched | Persistent AI agent memory across sessions with statusline integration | Solves session memory loss — pairs well with Claude Code statusline |

#### Research

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Perplexity MCP | 🔵 Researched | Real-time web search, deep research, and reasoning via Sonar models in any MCP client | Strongest MCP for live research — but overlaps with built-in search in Claude/ChatGPT |

#### Data

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Firecrawl MCP | 🔵 Researched | Web scraping, structured extraction, and site crawling for RAG and data pipelines | Most broadly useful MCP for dev workflows — map→scrape is the key pattern |

#### DevOps

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Vercel | 🟢 Used | Frontend deployment with GitHub auto-deploy and environment variables | Frictionless deploy — push to main and it's live |

---

### 🤖 Models

#### Coding

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Claude Opus 4.6 | 🟢 Used | Most advanced model for complex reasoning, research, and documentation | Go-to model for everything requiring depth |
| 2 | Claude Sonnet 4.5 | 🔵 Researched | Fast model used as GSD subagent backend | Good balance of speed and quality — GSD runs subagents on this |

---

### 🧠 Skills

#### Prompt-Engineering

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Context engineering | 🟢 Used | Managing AI context windows to prevent quality degradation | The meta-skill — everything else depends on keeping context clean |
| 2 | Learning from system prompts | 🟢 Used | Studying production AI system prompts to extract prompt patterns | Eight core patterns found — role framing, format locking, guardrails, etc. |

#### Coding

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Managing Claude skills | 🟢 Used | When to create vs. reuse skills, scoping per-project, avoiding bloat | Less is more — targeted skills beat large libraries |
| 2 | Terminal-first AI workflows | 🟢 Used | Understanding why AI tools use CLI and mapping to desktop mental models | Terminal unlocks parallel sessions, scripting, and composability |

#### Design

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Frontend design workflows | 🟢 Used | Five workflows for getting polished frontend from AI tools | Prompt-only is fastest but Figma-to-code gives best fidelity |

---

### 🔄 Workflows

#### Project-Building

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | GSD + multi-agent design | 🟢 Used | GSD for phased execution + 5-agent design exploration before UI build phase | Best of both worlds — structured execution with creative exploration |
| 2 | GSD + Ralph Loop | 🔵 Researched | GSD for planning + Ralph Loop for autonomous execution with backpressure | Theoretical power combo — GSD plans, Ralph executes autonomously |

#### Design

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Multi-agent design generation | 🟢 Used | Deploy 5 Claude Code agents in parallel for distinct design variants | Five unique designs in one shot — coordination protocol prevents duplicates |

<!-- LEADERBOARD_END -->

---

## Status Legend

| Badge | Meaning |
|-------|---------|
| 🟢 **Used** | I've used this in a real project or workflow |
| 🔵 **Researched** | I've investigated this but haven't used it yet |

---

## Data

The source of truth is [`data.yaml`](data.yaml). The agent reads and writes this file. The README tables above are re-rendered from the YAML whenever entries change.
