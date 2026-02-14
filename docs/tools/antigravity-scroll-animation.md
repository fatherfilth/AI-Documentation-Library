---
title: "Google Antigravity — Prompt to Scroll-Animated Frontend"
status: stable
category: "tools"
slug: "antigravity-scroll-animation"
created: 2026-02-14
updated: 2026-02-14
author: "claude"
tags: ["google-antigravity", "scroll-animation", "framer-motion", "prompt-to-code", "vibe-coding"]
---

# Google Antigravity — Prompt to Scroll-Animated Frontend

---

## Abstract

Google Antigravity is an agent-first IDE released in November 2025 alongside Gemini 3 that generates full frontend applications from text prompts. A popular use case — widely shared on social media — is producing scroll-animated portfolio websites where product images float, explode, or transform as the user scrolls. This document traces the full pipeline from prompt to deployed frontend, identifies the underlying CSS/JS patterns the AI generates, and documents the iterative debug loop that the one-take demos typically edit out. Key finding: the "antigravity" visual effect maps to a well-known stack of `position: sticky` canvases, Framer Motion `useScroll`, and scroll-linked opacity overlays — the tool's value is in orchestrating these patterns from a single structured prompt, not in inventing new frontend techniques.

---

## 1. Introduction

### 1.1 Problem Statement

Scroll-animated websites — the kind that win Awwwards — traditionally require senior frontend developers who understand canvas rendering, scroll math, Framer Motion or GSAP choreography, and responsive image optimization. Google Antigravity promises to collapse this into a single prompt. The question is: what actually happens between typing a prompt and getting a working frontend, and how reliable is that pipeline?

### 1.2 Scope

This document covers:
- The Antigravity IDE prompt-to-code workflow for scroll-animated sites
- The specific frontend patterns the AI generates (canvas pinning, frame sequences, text overlays)
- The iterative debugging process required after initial generation
- Asset requirements and gotchas

Not covered: Antigravity's general-purpose coding features, Remotion video workflows, or comparison with every AI IDE.

### 1.3 Key Terms

| Term | Definition |
|------|------------|
| Google Antigravity | Google's AI-powered IDE (released Nov 2025) built around Gemini 3, designed as an "agent-first" development platform |
| Scroll-pinned canvas | An HTML5 `<canvas>` with `position: sticky` that stays in the viewport while the user scrolls through a tall container |
| Frame sequence | A series of images (typically 80–200 frames) that are drawn to a canvas in order, mapped to scroll position, creating a video-like effect |
| useScroll | A Framer Motion hook that returns `scrollYProgress` (0 to 1) relative to a container, used to drive animations |
| Vibe coding | The practice of generating code by describing desired outcomes in natural language to AI tools |
| Wipe coding | The iterative debug phase where you show the AI screenshots of broken elements and describe what to fix |

---

## 2. Background & Prior Work

Scroll-driven animations have been a premium web pattern for years. Apple pioneered the technique with product pages (AirPods Pro, MacBook) that play image sequences as the user scrolls, creating a cinematic reveal effect. Traditionally, building these requires:

- **GSAP ScrollTrigger** — the industry-standard library for scroll-linked animations, used by most Awwwards-winning sites [1]
- **Framer Motion** — React-native motion library with `useScroll` and `useTransform` hooks for declarative scroll animations [2]
- **Canvas API** — for drawing pre-rendered frame sequences at 60fps, avoiding DOM-heavy approaches
- **Lottie / Rive** — for vector-based scroll-driven animations

Before Antigravity, tools like Cursor, Bolt.new, and v0 could generate animated frontends from prompts, but scroll-driven canvas sequences typically required manual wiring. Antigravity's differentiator is that its system prompt explicitly demands premium aesthetics and its agent can iterate on visual bugs using screenshots [3].

---

## 3. Methodology / Approach

### 3.1 Setup

```
1. Install Google Antigravity IDE (free, available for Mac/Windows/Linux)
2. Create or clone a GitHub repo into Antigravity
3. Prepare image assets:
   - 80+ frames of the product/subject (from video split or AI-generated)
   - Naming convention: frame_001.webp, frame_002.webp, ...
   - Place in public/images/ directory
4. Write a structured prompt (see Section 4.1)
5. Paste prompt into Antigravity Agent Manager
6. Accept edit access when prompted
7. Run npm install && npm run dev to preview locally
8. Begin iterative debug loop
```

### 3.2 Process

**Phase 1: Asset Generation**
The scroll effect lives or dies on image quality. Users either split a video into frames (FFmpeg: `ffmpeg -i input.mp4 -vf fps=20 frame_%03d.webp`) or generate product images with AI tools like ChatGPT/DALL-E and then create variations for the sequence.

**Phase 2: Prompt Engineering**
A detailed prompt is pasted into Antigravity that specifies the exact tech stack, component structure, scroll behavior, and text overlay timing. The most effective prompts follow the pattern documented in WebSensePro's breakdown [4]: ACT AS a creative developer → specify tech stack → define visual direction → list implementation details → describe sections and timing.

**Phase 3: Generation + Debug**
Antigravity generates all files. The user runs the dev server, identifies visual issues, takes screenshots, and feeds them back to Antigravity with descriptions like "the text overlay appears too early" or "the canvas isn't pinned on mobile." This loop runs 5–15 times before the result is production-ready.

---

## 4. Findings

### 4.1 The Prompt Structure That Works

Effective scroll-animation prompts follow a specific structure. Based on the WebSensePro and Lilys AI breakdowns [4][5], a working prompt includes:

1. **Role declaration**: "ACT AS a world-class Creative Developer (Awwwards-level)"
2. **Tech stack**: Next.js 14 (App Router), Tailwind CSS, Framer Motion, HTML5 Canvas
3. **Visual direction**: Background color (must match image background exactly), color palette, typography
4. **The sticky canvas component**: A container with `h-[400vh]`, a `<canvas>` element that is `sticky`, `top-0`, `h-screen`, `w-full`
5. **Scroll logic**: Load N images from a directory, use `useScroll` to map scroll progress to frame index, draw current frame on each tick
6. **Text overlay timing**: Specific percentages for headline appearance (e.g., 0% = title, 30% = feature 1, 60% = feature 2, 90% = CTA)
7. **Post-sequence content**: What happens after the animation ends

The specificity of this prompt is what makes Antigravity succeed — it's not "make me a cool website" but a near-specification.

### 4.2 The Frontend Patterns Are Standard — The Value Is Orchestration

The generated code uses four well-established frontend patterns:

1. **Scroll-pinned canvas**: `position: sticky` inside a tall scroll container. The scroll distance is the "playback" control.
2. **Framer Motion `useScroll` → `useTransform`**: Maps `scrollYProgress` (0 to 1) to a frame index (0 to N). Each scroll tick triggers a canvas redraw.
3. **Text overlays with scroll-linked opacity**: `<div>` elements positioned over the canvas with opacity driven by scroll ranges.
4. **Preloading strategy**: A subset of frames loads eagerly; the full sequence loads progressively to avoid stuttering.

None of these are novel. What Antigravity provides is the orchestration — wiring them together correctly, handling edge cases (mobile memory, image paths, responsive sizing), and producing a complete working app.

### 4.3 The Debug Loop Is the Real Work

The one-prompt demos seen on social media edit out the iteration. In practice, after initial generation, the user enters "wipe coding" — taking screenshots of broken elements and describing fixes. Common issues include mismatched image paths, scroll speed too fast or slow, text overlays at wrong positions, canvas not resizing on mobile, and images failing to preload. This typically takes 5–15 rounds of feedback.

### 4.4 Antigravity's System Prompt Demands Premium Aesthetics

Antigravity's internal system prompt explicitly instructs the AI to prioritize visual excellence, stating (per leaked prompts) that if the web app looks "simple and basic" then the AI has "FAILED" [3]. This design-first bias means scroll-animation prompts tend to produce richer output than equivalent prompts in other AI IDEs that don't have this instruction.

---

## 5. Confusion Points

| # | What I assumed | What's actually true | Impact |
|---|----------------|----------------------|--------|
| 1 | "Antigravity" refers to a CSS effect or animation library | It's the name of Google's AI IDE — the visual effect is just standard scroll-driven animation | High |
| 2 | One prompt produces a finished, deployable site | One prompt produces a starting point; 5–15 rounds of screenshot-based debugging are typical | High |
| 3 | The floating/scroll effect uses novel frontend techniques | It maps to standard patterns: sticky canvas, Framer Motion useScroll, scroll-linked opacity | Medium |
| 4 | Any prompt will produce the scroll effect | Prompts must be highly structured — specifying tech stack, canvas dimensions, scroll percentages, and text timing | Medium |
| 5 | The image assets are optional or easily replaceable | The scroll animation depends entirely on high-quality frame sequences; bad assets break the illusion | High |

---

## 6. Failure Modes & Gotchas

| # | Trigger | Symptom | Fix | Severity |
|---|---------|---------|-----|----------|
| 1 | Image path mismatch (e.g., `public/images/` vs `public/frames/`) | Canvas renders blank; no animation plays | Check code for expected path pattern; rename folder/files to match | Critical |
| 2 | Too few scroll container height | Animation plays too fast, frames skip | Increase container height (e.g., `h-[400vh]` → `h-[600vh]`) | High |
| 3 | Large frame sequences on mobile | Page crashes or stutters due to memory | Reduce frame count, compress images to WEBP, limit canvas resolution | High |
| 4 | Image background color doesn't match page background | Visible frame edges destroy the floating illusion | Eyedrop the exact background color from the frame sequence; use that as `bg-[#hex]` | Medium |
| 5 | Text overlays not timed to scroll | Headlines appear/disappear at wrong moments | Adjust scroll percentage ranges in the Framer Motion useTransform mappings | Medium |
| 6 | Frame naming convention mismatch | Only some frames load; animation jumps | Ensure consistent naming: `frame_001.webp` through `frame_080.webp` with zero-padded indices | High |

---

## 7. Analysis

### 7.1 Strengths

Antigravity's core strength for this use case is collapsing a multi-day frontend build into a prompt + debug loop. A developer who understands the scroll-animation pattern but would take 2–3 days to wire it manually can get a working prototype in 30–60 minutes. The built-in browser agent that accepts screenshots for debugging makes the iteration cycle particularly efficient — you don't need to describe DOM structure, you just show what's wrong.

The system prompt's aesthetic bias is also a genuine advantage. Other AI IDEs produce functional but visually generic output; Antigravity defaults to premium design decisions.

### 7.2 Limitations

The asset dependency is the biggest bottleneck. Antigravity generates code, not images — you still need to produce or source 80+ high-quality frames. If your assets are weak, no amount of prompt engineering fixes the result.

Mobile optimization remains manual. The initial generation rarely handles memory constraints, responsive canvas sizing, or aggressive image compression correctly. These require explicit debug prompts.

Reproducibility is low. The same prompt can produce structurally different code on different runs, making it hard to build a team workflow around Antigravity outputs.

### 7.3 Comparison to Alternatives

| Alternative | Key difference | When to prefer it |
|-------------|----------------|--------------------||
| Cursor + manual prompting | More control, less opinionated defaults | When you need specific architecture choices or have an existing codebase |
| Framer (no-code) | Visual editor, no code generation | When the team has designers but no developers |
| Hand-coded GSAP/Framer Motion | Full control, no AI dependency | When building a production site that needs to be maintainable long-term |
| Dora.run | AI-powered no-code 3D site builder | When you want no-code and don't need a codebase output |

---

## 8. Recommendations

### 8.1 Decision Checklist

- [ ] You need a scroll-animated site quickly (prototype or portfolio)
- [ ] You have or can produce high-quality image frame sequences
- [ ] You're comfortable with iterative prompt-based debugging
- [ ] Long-term maintainability of the generated code is not a primary concern
- [ ] You're targeting a single-page or landing-page format

> If any box is unchecked, see [Comparison to Alternatives](#73-comparison-to-alternatives).

### 8.2 Next Steps

- Test the workflow with a real project and document the exact prompts used and rounds of iteration required
- Compare output quality: Antigravity vs. Claude Code vs. Cursor for the same scroll-animation prompt
- Explore whether Antigravity Skills (modular prompt packages) can standardize the scroll-animation pattern into a reusable workflow
- Investigate the Remotion integration for generating the frame sequences programmatically instead of sourcing them manually

---

## 9. Open Questions & Unknowns

- The Notion guide linked by the user was behind authentication and could not be fully accessed — its specific step-by-step process may contain additional patterns not captured here
- The Instagram reel content could not be fetched — the exact demo technique shown may differ from the general workflow documented
- How does Antigravity handle scroll-animation on very long pages (10,000+ px) with multiple independent animation sequences?
- What is the maximum practical frame count before mobile browsers crash consistently?
- Does Antigravity's aesthetic bias actually produce better Lighthouse scores, or just better-looking code?

---

## References

1. [GSAP ScrollTrigger Documentation](https://gsap.com/docs/v3/Plugins/ScrollTrigger/)
2. [Framer Motion useScroll API](https://www.framer.com/motion/use-scroll/)
3. [Antigravity System Prompt (leaked) — GitHub](https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools/blob/main/Google/Antigravity/Fast%20Prompt.txt)
4. [WebSensePro: Google Antigravity — Build 3D Animated Award-Winning Website](https://websensepro.com/blog/google-antigravity-build-3d-animated-award-winning-website/)
5. [Lilys AI: Build Animated Websites with Google AntiGravity](https://lilys.ai/en/notes/google-antigravity-20260204/google-antigravity-animated-websites)
6. [Google Codelabs: Getting Started with Google Antigravity](https://codelabs.developers.google.com/getting-started-google-antigravity)
7. [Heikki Hellgren: Gemini3 + Antigravity — From Prompt to Portfolio (Medium)](https://drodil.medium.com/gemini3-antigravity-from-prompt-to-portfolio-2bca9b5ecb34)
8. [Samuel Adekunle: Google Antigravity AI Coding (Medium)](https://techwithsam.medium.com/google-antigravity-ai-coding-building-my-portfolio-site-from-scratch-63cc7e93a721)
9. [Bram.us: The Google Antigravity website, rebuilt with Modern CSS](https://www.bram.us/2025/12/02/google-antigravity-modern-css/)
10. [Google Antigravity IDE — Official Site](https://antigravity.google)

---

## Appendix

### A. Raw Data / Configs

**Minimal scroll-animation prompt structure (pseudocode):**

```
ACT AS: Awwwards-level creative developer
TECH STACK: Next.js 14 (App Router), Tailwind CSS, Framer Motion, HTML5 Canvas
VISUAL DIRECTION:
  - Background: #050505 (must match image background exactly)
  - Typography: Inter, tracking-tight
  - Color: text-white/90 headings, text-white/60 body
IMPLEMENTATION:
  1. Sticky canvas: container h-[400vh], canvas sticky top-0 h-screen w-full
  2. Scroll logic: load N frames from public/images/, useScroll → frame index
  3. Text overlays:
     - 0%: Title (centered)
     - 30%: Feature 1 (left)
     - 60%: Feature 2 (right)
     - 90%: CTA (centered)
  4. Post-sequence: remaining sections, navbar stays on top
PRELOAD: first 10 frames eagerly, rest progressively
MOBILE: compress frames, limit canvas resolution
```

### B. Related Documents

- [ai-frontend-design-workflows](../skills/ai-frontend-design-workflows.md) — related coverage of AI-to-frontend workflows
- [ai-frontend-design-tools](../tools/ai-frontend-design-tools.md) — survey of AI design-to-code tools including Antigravity's competitors

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-14 | Initial research article | claude |
