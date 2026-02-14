---
topic: frontend-design-in-build-workflow
category: skills
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "Where design fits in the build workflow, complete frontend design skills catalog, and multi-agent parallel design generation using Claude Code Teams."
related_topics:
  - multi-agent-design-generation
  - ai-frontend-design-workflows
  - ai-frontend-design-tools
---

# Transcript: Frontend Design in Build Workflow & Multi-Agent Design Generation

> This transcript covers the continuation of a conversation about AI frontend design. The first part (AI frontend design workflows and tools) is archived separately. This segment focuses on two new topics: (1) integrating design into the build workflow with a complete skills catalog, and (2) a multi-agent parallel design generation pattern sourced from an Instagram reel.

## Part 1: Where Does Design Fit in the Build Workflow?

**User:** How/where do I include this design process in my build workflow? What are all the best frontend design skills?

**Claude:** Most developers treat design as a phase that happens *before* development. That's wrong. Design is a layer that runs *through* the entire workflow.

### The Integrated Build Workflow

The workflow was presented as five phases:

- **Phase 0: Design Foundation** (do once, maintain forever) — Define design tokens, build component inventory, set up AI rules files, create Figma variables. This is Workflow 5 from the prior conversation. Time: 4-8 hours initially.
- **Phase 1: Direction** (per feature/page) — Explore visual direction using v0/Stitch for variants, reference site token extraction, or Figma handoff. Pick direction, screenshot key patterns.
- **Phase 2: Component Build** (the actual coding) — Generate components one-by-one in Cursor, AI reads rules files, paste screenshots for reference, "example first → variation" pattern. Visual QA each component. Compose into pages.
- **Phase 3: Polish** (where most people stop too early) — Responsive audit, interaction states, motion/micro-interactions, accessibility pass, typography fine-tuning, dark mode.
- **Phase 4: Feedback Loop** (feeds back to Phase 0) — Review what AI got wrong → update rules files. New components → add to inventory. New patterns → document.

### Key Insight

Design decisions happen at Phase 0 (tokens, rules) and Phase 1 (direction). Design *execution* happens at every phase. Phase 3 is where 80% of polish lives. Phase 4 is what separates improving teams from stagnant ones.

## Part 2: Complete Frontend Design Skills Catalog

Skills were organized into four tiers by learnability:

### Tier 1: Systematic Skills (learnable via rules, automatable with AI)
1. Spacing System — 4px base scale, every margin/padding from the scale
2. Typography Scale — Mathematical ratio, 3-5 levels of hierarchy
3. Color System — 1-2 base, 1 accent, 2-3 neutrals, CSS variables
4. Responsive Design — Mobile-first, Grid + Flexbox, content-based breakpoints
5. Accessibility — WCAG AA, focus-visible, semantic HTML, keyboard nav

### Tier 2: Compositional Skills (requires practice, AI needs guidance)
6. Visual Hierarchy — Size, weight, color, position to guide the eye
7. Whitespace / Negative Space — More space = more premium
8. Layout & Grid — 12-column, asymmetry must be intentional
9. Font Pairing — Distinctive display + refined body, two fonts max
10. Consistency — Same patterns across the entire project

### Tier 3: Perceptual Skills (requires taste, hardest for AI)
11. Color Harmony & Emotion — Palettes carry emotional weight
12. Motion & Micro-Interactions — Communicate, don't decorate
13. Visual Weight & Balance — Every element has visual weight
14. Depth & Layering — Shadows, overlap, z-axis thinking
15. Contextual Appropriateness — Match design language to domain

### Tier 4: Strategic Skills (product-level thinking)
16. Conversion-Oriented Design — Five-second test, one CTA per view
17. Information Architecture — Content organization and navigation
18. Design System Thinking — Atomic design: token → atom → molecule → organism

### Mapping to AI Rules Files

```
.ai/
├── design-system-foundation.md    ← Skills 1-5
├── component-inventory.md         ← Skills 10, 18
├── visual-direction.md            ← Skills 6-9, 11
├── motion-guidelines.md           ← Skill 12
└── do-not.md                      ← Negative constraints
```

## Part 3: Instagram Reel — Multi-Agent Design Generation

**User:** Shared an Instagram reel (https://www.instagram.com/reel/DUrgkd1DDbk/) describing a workflow:

1. Use Claude Code Teams
2. Find all reputable FE UI/UX design skills
3. Put them in a project folder
4. Create one synthesized design skill from all of them
5. Switch to plan mode
6. Plan deployment of 5 agents, each creating a unique design for a page
7. Direct all agents at the synthesized skill
8. Agents communicate to ensure they pull unique pieces of the skill
9. Each pulls unique assets/images from the internet
10. Each creates unique animated SVGs

### Available Skills Ecosystem (Researched)

**Tier 1 — Official/High-Quality:**
- `frontend-design` (Anthropic claude-code plugin) — Typography, color, motion, spatial composition, backgrounds
- `web-artifacts-builder` (Anthropic skills repo) — React + Tailwind + shadcn/ui
- `ui-ux-pro-max` (nextlevelbuilder.io) — 57 UI styles, 95 color palettes, 56 font pairings, 24 chart types, 98 UX guidelines
- `product-frontend-design` (mosif16/codex-skills) — SaaS/product marketing, five-second test, CTA hierarchy
- `ui-ux-design` (arvindand/agent-skills) — Full workflow with REFERENCES.md database

**Tier 2 — Framework-Specific:**
- `mui` (davila7) — MUI v7 patterns
- `shadcn-ui` (google-labs-code) — shadcn/ui components
- `ios-design` (pokotieru-mashumaro) — SwiftUI
- `react-components`, `design-md`, `enhance-prompt`, `stitch-loop` (google-labs-code)

**Tier 3 — Community:**
- `frontend-design` (eddiebe147) — Anti-convergence variant
- `frontend-design` (YYH211/Claude-meta-skill) — Implementation complexity matching
- Various from obra/superpowers-lab

### Synthesis Approach

Organize by design dimension, not by source: Typography, Color & Theme, Layout & Spatial Composition, Motion & Interaction, Backgrounds & Depth, Accessibility, Conversion & UX, Anti-Patterns.

### Agent Team Plan Structure

The plan prompt should:
1. Assign each agent a PRIMARY design dimension (typography-driven, color-driven, motion-driven, spatial-driven, conversion-driven)
2. Require agents to DECLARE font, palette, layout choices BEFORE coding
3. Lead agent reviews declarations for uniqueness conflicts
4. Only after confirmation does implementation begin
5. Each agent outputs to `designs/variant-{1-5}/`

### Why This Works
- Synthesis step is the force multiplier (2000 tokens of concentrated intelligence)
- Dimension assignment prevents convergence (agents would otherwise converge on skill's heaviest emphasis)
- "Declare before coding" protocol prevents aesthetic collision
- Teams (not subagents) because agents need to negotiate uniqueness

### Where This Breaks
- Token cost: 5-10x a single session
- Animated SVG generation: expect 2-3/5 broken on first pass
- "Pull from internet" is underspecified: need to constrain sources
- Context window limits per agent
- Feature is still experimental: session drops, zombie agents, missed messages
