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

*No entries yet — rankings will appear here as conversations populate the leaderboard.*

<!-- LEADERBOARD_START -->
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
