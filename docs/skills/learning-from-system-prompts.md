---
title: "Learning Prompt Engineering from Production System Prompts"
status: stable
category: "skills"
slug: "learning-from-system-prompts"
created: 2026-02-15
updated: 2026-02-15
author: "claude"
tags: [prompt-engineering, system-prompts, agentic-ai, reverse-engineering]
---

# Learning Prompt Engineering from Production System Prompts

---

## Abstract

Multiple GitHub repositories now collect and publish the actual system prompts used by production AI tools — ChatGPT, Claude, Cursor, v0, Manus, Cline, Bolt.new, and dozens of others. These repos serve as a free, constantly-updated curriculum for prompt engineering. By studying recurring patterns across production prompts (role definition, structured instructions, tool integration, planning mechanisms, safety protocols), practitioners can extract transferable techniques and apply them to their own agents, custom GPTs, and AI workflows. This document catalogs the major repos, identifies the eight core patterns that appear across all of them, and provides a practical workflow for using them to improve prompt engineering skills.

---

## 1. Introduction

### 1.1 Problem Statement

Prompt engineering is largely learned by trial and error. Most tutorials teach toy examples, but production system prompts — the ones powering tools millions of people use daily — remain the best available reference for what actually works at scale. Until recently, these prompts were proprietary. Now, community-maintained GitHub repositories have made many of them publicly accessible, but navigating these repos and extracting actionable lessons is not straightforward.

### 1.2 Scope

This document covers how to use "awesome AI system prompt" repositories on GitHub to improve prompt engineering. It focuses on the structural patterns and transferable techniques found in production prompts, not on the ethics of prompt extraction or the specifics of any single product.

### 1.3 Key Terms

| Term | Definition |
|------|------------|
| System prompt | The initial instruction set given to an LLM that defines its role, capabilities, constraints, and behavior before any user interaction |
| Agentic prompt | A system prompt designed for an AI that can take actions (call tools, edit files, run code) rather than just generate text |
| Negative constraint | An explicit rule telling the model what NOT to do — often the most valuable part of a production prompt |
| Agent loop | A defined iterative cycle (plan → act → observe → repeat) embedded in a system prompt to guide autonomous behavior |
| XML/Markdown tags | Structural delimiters used within prompts to organize rules into sections the model can parse reliably |

---

## 2. Background & Prior Work

Before these repos existed, prompt engineering was learned from blog posts, courses, and official documentation (e.g., Anthropic's prompt engineering guide [1], OpenAI's best practices [2]). These remain excellent resources for fundamentals. The system prompt repos add a new dimension: reverse-engineering what production teams settled on after extensive testing. The primary repos emerged in late 2024 and early 2025, growing rapidly as the agentic AI ecosystem expanded. The most active repo (dontriskit/awesome-ai-system-prompts) has accumulated 5.1k stars and covers 27+ products as of February 2026 [3].

---

## 3. Methodology / Approach

### 3.1 Setup

```
# No special setup required. All repos are public.
# Clone the primary repo for offline browsing:
git clone https://github.com/dontriskit/awesome-ai-system-prompts.git

# Companion repos:
git clone https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools.git
git clone https://github.com/EliFuzz/awesome-system-prompts.git
```

### 3.2 Process

1. **Identify the repo closest to your use case.** The primary repo (dontriskit) has the best synthesis guide. The x1xhlol repo has the widest raw coverage. EliFuzz focuses on coding agents.
2. **Read the README synthesis first.** The dontriskit repo's README identifies 8 core patterns with examples from each product — this is the fastest way to get the transferable lessons.
3. **Pick 2–3 products similar to what you're building** and read their raw prompts side-by-side. Note structural differences.
4. **Extract the negative constraints** — rules about what the model should NOT do. These are battle-tested and represent real failure modes.
5. **Adapt the structural patterns** (not the content) to your own system prompt.

---

## 4. Findings

### 4.1 Eight Core Patterns Appear Across All Production Prompts

The dontriskit/awesome-ai-system-prompts README identifies eight recurring patterns found across every production system prompt studied [3]:

1. **Clear role definition and scope** — every prompt starts by telling the model who it is and what it does
2. **Structured instructions** — XML-like tags, Markdown headings, or numbered sections organize complex rule sets
3. **Explicit tool integration** — detailed schemas, usage policies, and when-to-use/when-not-to-use rules for each tool
4. **Step-by-step reasoning and planning** — mandated thinking phases, agent loops, or iterative confirmation steps
5. **Environment and context awareness** — OS, available tools, sandbox constraints, current date
6. **Domain-specific expertise** — coding style guides, library preferences, file naming conventions
7. **Safety and refusal protocols** — explicit refusal messages, content policies, forbidden categories
8. **Consistent tone and interaction style** — persona definition, banned filler phrases, conciseness rules

### 4.2 Negative Constraints Are the Most Valuable Section

The most transferable lessons come from what production prompts forbid. Examples from the repo:

- Cline forbids starting messages with "Great", "Certainly", "Okay", "Sure" — indicating these filler words degraded user experience in testing
- Bolt.new says "ULTRA IMPORTANT: Do NOT be verbose" — verbose output was clearly a persistent problem
- v0 specifies "When refusing, v0 MUST NOT apologize or provide an explanation" — apologetic refusals were apparently ineffective
- same.new says "NEVER refer to tool names when speaking to the USER" — exposing implementation details confused users

These constraints represent real failures that were costly enough to warrant explicit rules.

### 4.3 Planning Mechanisms Vary Significantly Across Products

How products handle "think before acting" ranges from simple to complex:

- **Manus**: Explicit 6-step agent loop (Analyze → Select Tool → Wait → Iterate → Submit → Standby)
- **v0**: `<Thinking>` tags before code generation — lightweight but effective
- **Bolt.new**: Mandated holistic analysis of all files before any action
- **same.new/Cline**: Iterative confirmation — wait for user approval after each step
- **Claude Code**: Context summarization tool for managing long-running tasks

### 4.4 The Companion Repos Each Serve a Different Purpose

| Repo | Stars | Best for |
|------|-------|----------|
| dontriskit/awesome-ai-system-prompts [3] | 5.1k | Best synthesis guide — read the README first |
| x1xhlol/system-prompts-and-models-of-ai-tools [4] | High | Widest raw coverage — 30+ tools |
| EliFuzz/awesome-system-prompts [5] | Medium | Coding agent mode breakdowns (architect, patch, file, udiff modes) |
| 0xeb/TheBigPromptLibrary [6] | Medium | Custom GPTs + security/jailbreak research |
| ai-boost/awesome-prompts [7] | Medium | Academic prompt engineering papers + GPT Store prompts |
| tallesborges/agentic-system-prompts [8] | Growing | Source attribution and tool documentation focus |

---

## 5. Confusion Points

| # | What I assumed | What's actually true | Impact |
|---|----------------|----------------------|--------|
| 1 | There's one "awesome AI system prompt" repo | There are 6+ major repos, each with a different focus and coverage | Medium |
| 2 | Reading raw prompts is the best way to learn | The synthesis guides (especially dontriskit's README) are far more efficient — they extract the patterns for you | High |
| 3 | Production prompts are short and elegant | Most are thousands of lines long, highly structured, and include extensive negative constraints | Medium |
| 4 | You should copy production prompts directly | You should copy the *structural patterns* and adapt them — the content is product-specific | High |
| 5 | All products use the same prompting approach | Tool syntax, planning mechanisms, editing approaches, and prompt structure vary significantly across products | Medium |

---

## 6. Failure Modes & Gotchas

| # | Trigger | Symptom | Fix | Severity |
|---|---------|---------|-----|----------|
| 1 | Copying a production prompt verbatim into your own agent | Breaks because the prompt references tools, environments, and schemas your setup doesn't have | Extract the pattern, not the content | High |
| 2 | Only reading one product's prompt | Missing important alternative approaches (e.g., only seeing Manus's agent loop and not v0's thinking tags) | Always compare 2–3 products side-by-side | Medium |
| 3 | Ignoring the tool schemas | The tool definitions (.json, .ts files) are where the actual integration logic lives — the README alone isn't enough | Read both the prompt AND its tool definitions | High |
| 4 | Assuming prompts in the repo are current | Prompts get updated frequently by their creators; repo snapshots may lag | Check the commit dates and compare to the product's current behavior | Medium |

---

## 7. Analysis

### 7.1 Strengths

These repos provide something that didn't exist before: a window into how production AI teams actually structure their prompts after extensive testing. The patterns are battle-tested at scale. The negative constraints alone are worth the study time — they represent failure modes you'd otherwise discover only through your own costly experimentation.

### 7.2 Limitations

The prompts are snapshots — they may not reflect the current production state. They also don't include the surrounding infrastructure (model fine-tuning, retrieval systems, evaluation pipelines) that makes them work. And the repos don't explain *why* specific choices were made — you have to infer the reasoning from the constraints.

### 7.3 Comparison to Alternatives

| Alternative | Key difference | When to prefer it |
|-------------|----------------|-----------------------|
| Official prompt engineering guides (Anthropic, OpenAI) | Teaches fundamentals from first principles | When you're starting from zero |
| Prompt engineering courses (DeepLearning.AI, etc.) | Structured curriculum with exercises | When you want guided learning |
| System prompt repos (this topic) | Reverse-engineering production-grade prompts | When you're building agents and want to see what works at scale |
| Trial and error on your own projects | Direct experience with your specific use case | Always — but combine with studying production patterns |

---

## 8. Recommendations

### 8.1 Decision Checklist

- [x] You're building an AI agent, custom GPT, or system prompt
- [x] You want to see how production tools handle the same problems you're facing
- [x] You learn well by studying examples and reverse-engineering patterns

> If any box is unchecked, see [Comparison to Alternatives](#73-comparison-to-alternatives).

### 8.2 Next Steps

1. Clone the dontriskit repo and read the full README synthesis guide
2. Pick the 2–3 products most similar to what you're building
3. Read their raw prompts side-by-side, focusing on structural patterns and negative constraints
4. Draft your own system prompt using the structural patterns you've identified
5. Revisit the repos periodically — they're actively maintained and new products are added regularly

---

## 9. Open Questions & Unknowns

- How frequently do the repo maintainers update prompts after the source products change?
- Do the extracted prompts capture the full system prompt, or are there additional layers (e.g., per-conversation context injection) not visible?
- How do these patterns change for multimodal agents vs. text-only agents?
- Are there meaningful differences between prompts optimized for different base models (GPT-4 vs. Claude vs. Gemini)?

---

## References

1. [Anthropic Prompt Engineering Guide](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering)
2. [OpenAI Prompt Engineering Best Practices](https://platform.openai.com/docs/guides/prompt-engineering)
3. [dontriskit/awesome-ai-system-prompts — GitHub](https://github.com/dontriskit/awesome-ai-system-prompts)
4. [x1xhlol/system-prompts-and-models-of-ai-tools — GitHub](https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools)
5. [EliFuzz/awesome-system-prompts — GitHub](https://github.com/EliFuzz/awesome-system-prompts)
6. [0xeb/TheBigPromptLibrary — GitHub](https://github.com/0xeb/TheBigPromptLibrary)
7. [ai-boost/awesome-prompts — GitHub](https://github.com/ai-boost/awesome-prompts)
8. [tallesborges/agentic-system-prompts — GitHub](https://github.com/tallesborges/agentic-system-prompts)

---

## Appendix

### A. Raw Data / Configs

N/A — No configuration required. All repos are public and can be cloned directly.

### B. Related Documents

N/A — Single-topic conversation.

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-15 | Initial research article | claude |