---
topic: learning-from-system-prompts
category: skills
date: 2026-02-15
participants:
  - user
  - claude
source: claude.ai
summary: "How to use 'awesome AI system prompt' GitHub repos to study production prompt patterns and improve prompt engineering skills."
related_topics: []
---

# Transcript: Learning From System Prompts

## User's Question

The user asked how to use "awesome AI system prompt" repositories on GitHub to improve their prompt engineering.

## Research Phase

Claude searched for the relevant GitHub repositories. Multiple repos surfaced:

- **dontriskit/awesome-ai-system-prompts** (5.1k stars) — the most popular, with a comprehensive README synthesizing patterns across all prompts
- **x1xhlol/system-prompts-and-models-of-ai-tools** — the most comprehensive raw collection (30+ tools)
- **EliFuzz/awesome-system-prompts** — focused on coding agents with mode breakdowns
- **0xeb/TheBigPromptLibrary** — includes custom GPTs and jailbreak research
- **ai-boost/awesome-prompts** — curated ChatGPT prompts with academic prompt engineering papers
- **tallesborges/agentic-system-prompts** — focused on agentic coding patterns with source attribution

Claude fetched the full README of the primary repo (dontriskit/awesome-ai-system-prompts) which contained a detailed guide titled "Crafting Effective Prompts for Agentic AI Systems: Patterns and Practices."

## Key Findings Discussed

### Repo Structure
The primary repo organizes prompts by product (ChatGPT, Claude, Claude Code, Cursor, v0, Manus, Cline, Bolt.new, Loveable, Windsurf, etc.) with each folder containing raw prompt files in `.md`, `.txt`, `.ts`, or `.json` format.

### Eight Core Patterns Identified Across Production Prompts
1. Clear role definition and scope
2. Structured instructions and organization (XML tags, Markdown headings)
3. Explicit tool integration and usage guidelines
4. Step-by-step reasoning and planning
5. Environment and context awareness
6. Domain-specific expertise and constraints
7. Safety, alignment, and refusal protocols
8. Consistent tone and interaction style

### Practical Usage Workflow
Claude recommended a specific approach:
1. Study patterns, not just individual prompts
2. Compare how different products solve the same problem (e.g., planning mechanisms)
3. Copy structural patterns as templates for your own system prompts
4. Focus on negative constraints and forbidden behaviors as "confusion point goldmines"
5. Use companion repos for deeper dives into specific areas

### Planning Mechanism Comparison
Different tools handle pre-action planning differently:
- Manus: explicit agent loop (Analyze → Select Tool → Wait → Iterate → Submit)
- v0: `<Thinking>` tags before code generation
- Bolt.new: mandated holistic thinking before artifact creation
- same.new: required user confirmation after each step
