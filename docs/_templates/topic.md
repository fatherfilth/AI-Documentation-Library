---
title: "{TITLE}"
status: draft | review | stable | deprecated
category: "{CATEGORY}"
slug: "{SLUG}"
created: YYYY-MM-DD
updated: YYYY-MM-DD
author: "{NAME_OR_AGENT_ID}"
tags: []
---

# {TITLE}

---

## Abstract

<!-- 3–5 sentence summary of the entire document. What is this, why does it matter, and what did we find? A reader should be able to skip everything else and still get value from this. -->

{REQUIRED}

---

## 1. Introduction

### 1.1 Problem Statement

<!-- What problem or question does this address? Why now? -->

{REQUIRED}

### 1.2 Scope

<!-- What this document covers and what it explicitly does NOT cover. -->

{REQUIRED}

### 1.3 Key Terms

<!-- Define any terms a reader might not know. Keep to 3–6 entries. -->

| Term | Definition |
|------|------------|
| {TERM} | {DEFINITION} |

---

## 2. Background & Prior Work

<!-- What existed before this? What other approaches have been tried? Cite specific tools, papers, or docs. This grounds the reader and prevents re-research. -->

{REQUIRED}

---

## 3. Methodology / Approach

<!-- How did we arrive at the findings below? This could be: setup steps, configuration, eval design, architecture decisions, or the specific workflow used. -->

### 3.1 Setup

<!-- Environment, prerequisites, configuration. Code blocks and commands only — no prose. -->

```
{REQUIRED}
```

### 3.2 Process

<!-- Step-by-step what was done. Numbered if sequential, grouped by phase if parallel. -->

{REQUIRED}

---

## 4. Findings

<!-- What did we learn? Organized by sub-topic. Each finding should be a specific, falsifiable claim. -->

### 4.1 {FINDING_TITLE}

{REQUIRED}

### 4.2 {FINDING_TITLE}

{REQUIRED}

---

## 5. Confusion Points

<!-- Specific misunderstandings surfaced during the conversation. These are the most valuable part of the doc — they prevent others from hitting the same walls. -->

| # | What I assumed | What's actually true | Impact |
|---|----------------|----------------------|--------|
| 1 | {MISCONCEPTION} | {CORRECTION} | {HIGH_MEDIUM_LOW} |

---

## 6. Failure Modes & Gotchas

<!-- Things that will break. Be specific: trigger → symptom → fix. -->

| # | Trigger | Symptom | Fix | Severity |
|---|---------|---------|-----|----------|
| 1 | {TRIGGER} | {SYMPTOM} | {FIX} | {CRITICAL_HIGH_MEDIUM_LOW} |

---

## 7. Analysis

### 7.1 Strengths

<!-- What works well and why. -->

{REQUIRED}

### 7.2 Limitations

<!-- What doesn't work, what's missing, where it breaks down. -->

{REQUIRED}

### 7.3 Comparison to Alternatives

<!-- How does this compare to other approaches? -->

| Alternative | Key difference | When to prefer it |
|-------------|----------------|--------------------|
| {NAME} | {DIFFERENCE} | {WHEN} |

---

## 8. Recommendations

### 8.1 Decision Checklist

<!-- Binary yes/no gates. If all yes → use this. -->

- [ ] {CONDITION_1}
- [ ] {CONDITION_2}
- [ ] {CONDITION_3}

> If any box is unchecked, see [Comparison to Alternatives](#73-comparison-to-alternatives).

### 8.2 Next Steps

<!-- What should be explored next? Unresolved questions, follow-up experiments, adjacent topics. -->

{REQUIRED}

---

## 9. Open Questions & Unknowns

<!-- Things we don't know yet. Honest gaps. If a reference couldn't be verified, note it here. -->

- {QUESTION_OR_UNKNOWN}

---

## References

<!-- No bare URLs. Every link gets a label. Numbered for in-text citation. -->

1. [{LABEL}]({URL})

---

## Appendix

### A. Raw Data / Configs

<!-- Any supporting material: full config files, raw output, extended tables. -->

N/A — {REASON_OR_CONTENT}

### B. Related Documents

<!-- Links to other docs in this library that connect to this topic. -->

- [{RELATED_SLUG}](../{CATEGORY}/{RELATED_SLUG}.md)

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| YYYY-MM-DD | Initial research article | {AUTHOR} |
