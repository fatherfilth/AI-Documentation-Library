---
topic: antigravity-scroll-animation
category: tools
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "How Google Antigravity IDE turns a single prompt into a scroll-animated frontend website — the full pipeline from prompt to deployed FE."
related_topics: []
---

# Transcript: Google Antigravity — Prompt to Scroll-Animated Frontend

## Context

The user encountered an Instagram reel and a Notion step-by-step guide demonstrating how to build scroll-animated portfolio websites using Google's Antigravity IDE. Their core question: how does writing a prompt in Antigravity produce a working frontend with floating/antigravity scroll effects?

## Key Exchange

**User:**
> How is antigravity in prompt → FE?
>
> saw this https://www.instagram.com/reel/DUjUWtoCBet/
>
> and led me to this [Notion: Scroll Animated Portfolio Website for free — Step by Step Guide]

**Claude:**

Researched the topic across multiple sources — Lilys AI notes on Antigravity scroll-animation workflows, Google's official Antigravity codelabs, WebSensePro's detailed prompt breakdown, Medium articles from developers who tested the workflow, and the leaked Antigravity system prompt.

### The Pipeline: Prompt → Frontend

**Step 1: Asset Preparation**
The scroll effect requires an image sequence (typically 80+ frames). These can be AI-generated or sourced directly. The quality of the base images is critical — flawed assets make the entire animation look fake.

**Step 2: The Prompt**
A structured prompt is pasted into Antigravity specifying:
- Tech stack: Next.js 14, Tailwind CSS, Framer Motion
- A sticky `<canvas>` element inside a tall scroll container (`h-[400vh]`)
- `useScroll` from Framer Motion mapping scroll progress (0→1) to frame index
- Text overlays that fade in/out at specific scroll percentages (30%, 60%, 90%)

**Step 3: Code Generation**
Antigravity generates the entire site — components, routing, styling — from the single prompt. The system prompt instructs the AI to prioritize rich aesthetics and premium design.

**Step 4: Iterative Debug Loop**
The real work begins. The user takes screenshots of broken elements, describes the problem, and iterates. Common issues include image path mismatches, scroll speed tuning, and text placement. This typically takes 5–15 rounds.

### The Frontend Patterns Behind "Antigravity"

The floating/scroll effect is composed of well-known FE patterns:
1. **Scroll-pinned canvas** — `position: sticky` canvas stays in view while user scrolls a tall container. Scroll distance controls which frame renders.
2. **Framer Motion `useScroll`** — Maps `scrollYProgress` to a frame index, redraws canvas on each tick.
3. **Text overlays with scroll-linked opacity** — Headings animate `opacity: 0 → 1 → 0` based on scroll position.
4. **GSAP / Framer Motion for floating elements** — `transform: translateY()` animations triggered by scroll or intersection observers.

### Key Insight

The "magic" isn't any single CSS trick — it's that Antigravity orchestrates the full complexity (canvas loop, scroll math, text timing, responsive handling) from a well-structured prompt. The one-prompt demos are edited; real usage requires iterative debugging.

## Sources Consulted

- Lilys AI: "Build Animated Websites with Google AntiGravity" (Feb 2026)
- WebSensePro: "Google Antigravity: Build 3D Animated Award-Winning Website" (Jan 2026)
- Google Codelabs: "Getting Started with Google Antigravity"
- Medium — Heikki Hellgren: "Gemini3 + Antigravity: From Prompt to Portfolio" (Nov 2025)
- Medium — Samuel Adekunle: "Google Antigravity AI Coding" (Dec 2025)
- Bram.us: "The Google Antigravity website, rebuilt with Modern CSS" (Dec 2025)
- Leaked Antigravity system prompt via GitHub (x1xhlol/system-prompts-and-models-of-ai-tools)
