# Contributing — AI Documentation Library

> **These rules are non-negotiable.** Every agent and human contributor follows them exactly.

---

## Folder Routing & Naming Conventions

### Category → Folder

| Category | Path | Definition |
|----------|------|------------|
| Models | `docs/models/` | AI model capabilities, behavior, architecture, benchmarks, prompting patterns |
| Tools | `docs/tools/` | Software tools, libraries, CLIs, APIs, integrations |
| Skills | `docs/skills/` | Techniques, workflows, methodologies, transferable practices |
| Repos | `docs/repos/` | Specific GitHub repositories — structure, usage, contribution patterns |
| Agents | `docs/agents/` | AI agent architectures, configurations, orchestration, multi-agent systems |

No other category folders exist. If a topic doesn't fit, discuss before creating a new one.

### Slug Rules

| Rule | Example |
|------|---------|
| Format: `tool-name-feature` | `oauth-device-flow`, `mcp-server-config` |
| Lowercase, hyphenated only | ✅ `prompt-caching` · ❌ `Prompt_Caching` |
| No dates in slug | ❌ `mcp-2026-02` |
| Max ~40 characters | ❌ `how-to-configure-advanced-prompt-caching-with-claude` |
| No special characters | ❌ `oauth/device.flow` |
| No category prefix | ❌ `tools-oauth-flow` · ✅ `oauth-flow` |

### Conversation Archive Path

```
conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md
```

- Double underscore `__` separates date from slug.
- Date is the conversation date, not the publish date.
- Slug must match the branch slug character-for-character.

---

## Workflow: How Every Chat Works

> The agent handles categorization and publishing automatically. No manual commands needed.

### Phase 1: Conversation

1. Start a new chat in the project.
2. Just talk about the topic — ask questions, debug, explore.
3. The agent silently infers the **category** and **slug** from context.
4. The agent silently tracks confusion points, decisions, working snippets, and references.

### Phase 2: Auto-Publish

5. When the conversation reaches a natural end, the agent proposes a publish with category/slug/summary for confirmation.
6. On confirmation, the agent creates branch → transcript → doc → index → PR.
7. You review and merge.

The user may also say "Publish" at any time to trigger Phase 2 early.
The user may say "Skip" or "Don't publish" to end without a PR.
The user may override the inferred category or slug during confirmation.

**Full agent instructions live in `.claude/PROJECT_INSTRUCTIONS.md`.**

---

## Required Files Per PR

ALL three — PR will be rejected if any are missing.

### A. Curated Doc Page

```
docs/<category>/<slug>.md
```

- Must follow `docs/_templates/topic.md` exactly.
- Every `{REQUIRED}` placeholder must be filled.
- No empty sections. If a section truly doesn't apply, write `N/A — {reason}`.

### B. Transcript Archive

```
conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md
```

- Must include YAML frontmatter:

```yaml
---
topic: <slug>
category: <category>
date: YYYY-MM-DD
participants:
  - user
  - claude
source: claude.ai | api | claude-code
summary: "One-line summary of the conversation."
---
```

### C. Index Update

```
docs/_index/README.md
```

- Add one row above the `<!-- NEW ROWS ABOVE THIS LINE -->` comment.
- Row format:

```
| <slug> | <category> | <one-line summary> | [doc](../category/slug.md) | [transcript](../../conversations/YYYY/YYYY-MM-DD__slug/transcript.md) | YYYY-MM-DD |
```

- Rows sorted alphabetically by slug.

---

## Commit & PR Requirements

### Commits (two small commits per PR)

1. Transcript only → `docs(<category>): add transcript for <slug>`
2. Doc page + index → `docs(<category>): add doc and index for <slug>`

### Branch

```
topic/<slug>
```

No direct commits to `main`. Ever.

### PR Format

- **Title:** `docs(<category>): <slug>`
- **Body must include:**

```markdown
## Summary
<One-line summary.>

## Confusion Points Captured
<Bulleted list from the conversation.>

## Checklist

- [x] Branch named `topic/<slug>`
- [x] Doc page at `docs/<category>/<slug>.md` using `topic.md` template
- [x] Transcript at `conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` with YAML frontmatter
- [x] Index row added to `docs/_index/README.md`
- [x] All `{REQUIRED}` placeholders filled
- [x] No empty sections
```

- **Do not squash-merge.** Standard merge to preserve commit history.

---

## Validation Rules (for agents)

Before creating a PR, verify:

1. `ls docs/<category>/<slug>.md` → file exists.
2. `ls conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` → file exists.
3. `grep '<slug>' docs/_index/README.md` → row exists in index.
4. `grep '{REQUIRED}' docs/<category>/<slug>.md` → returns **zero** matches (all filled).
5. Branch name starts with `topic/`.
6. Slug is ≤ 40 characters, lowercase, hyphenated, no dates.

If any check fails, **stop and fix before opening the PR.**

---

## Automation Hardening

### A. Filesystem Scope

Agents may only read/write within the repository working tree. No access to home directories, system paths, or other repos.

### B. GitHub Token Permissions (Minimum Scopes)

| Scope | Purpose |
|-------|---------|
| `contents: write` | Create branches, push commits |
| `pull-requests: write` | Open PRs |
| `metadata: read` | List repo info |

No `admin`, no `delete`, no `actions: write`. Token scoped to this single repo.

### C. CI Checks (enforced via GitHub Actions)

The `pr-validation` workflow runs on every PR and **must pass** before merge. See `.github/workflows/pr-validation.yml`.

---

## Sourcing & Research Guidelines

### Without a research/browsing tool

1. User pastes 3–10 quality reference links.
2. Agent builds a **research pack** first, then the doc page.
3. All sources must appear in the **References** section with labeled links.

### With a research-fetch tool

- Tool must be **read-only**.
- All fetched URLs logged in the transcript.
- References section still mandatory.

### Hard rule

> **No references section = PR rejected.** Every claim traces back to a source.

---

## Quick Reference

| Rule | Value |
|------|-------|
| Branch pattern | `topic/<slug>` |
| Slug format | lowercase, hyphenated, ≤40 chars, no dates, no category prefix |
| Doc path | `docs/<category>/<slug>.md` |
| Transcript path | `conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` |
| Index path | `docs/_index/README.md` |
| PR title format | `docs(<category>): <slug>` |
| Valid categories | `models`, `tools`, `skills`, `repos`, `agents` |
| Template | `docs/_templates/topic.md` |
| Agent instructions | `.claude/PROJECT_INSTRUCTIONS.md` |
| Direct commits to main | **Forbidden** |
| Empty references section | **Forbidden** |
| Manual categorization needed | **No — agent auto-infers** |
| Manual publish command needed | **No — agent auto-triggers** |
