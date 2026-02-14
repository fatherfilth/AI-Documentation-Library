# Contributing — AI Documentation Library

> **These rules are non-negotiable.** Every agent and human contributor follows them exactly.

---

## Workflow: Adding a New Topic

### 1. Branch

```
topic/<slug>
```

- `<slug>` is lowercase, hyphenated, no special characters.
- Examples: `topic/mcp-servers`, `topic/claude-code`, `topic/prompt-caching`.
- **No commits directly to `main`.** All work goes through a PR.

### 2. Required Files (ALL three — PR will be rejected if any are missing)

#### A. Curated Doc Page

```
docs/<category>/<slug>.md
```

- `<category>` must be one of: `models`, `tools`, `skills`, `repos`, `agents`.
- Must follow the template at `docs/_templates/topic.md` exactly.
- Every `{REQUIRED}` placeholder must be filled.
- No empty sections. If a section truly doesn't apply, write `N/A — {reason}`.

#### B. Transcript Archive

```
conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md
```

- `YYYY` = four-digit year.
- `YYYY-MM-DD` = date the conversation occurred.
- `<slug>` must match the branch slug exactly.
- File must include a YAML frontmatter block:

```yaml
---
topic: <slug>
date: YYYY-MM-DD
participants:
  - user
  - claude
source: claude.ai | api | claude-code
summary: "One-line summary of the conversation."
---
```

#### C. Index Update

```
docs/_index/README.md
```

- Add one row to the master index table.
- Row format:

```
| <slug> | <category> | <one-line summary> | [doc](../category/slug.md) | [transcript](../../conversations/YYYY/YYYY-MM-DD__slug/transcript.md) | YYYY-MM-DD |
```

- Rows are sorted alphabetically by slug.
- If a category-level index exists (e.g. `docs/models/README.md`), update that too.

### 3. PR Requirements

- **Title:** `docs(<category>): <slug>`
- **Body must include:**
  - One-line summary of what the topic covers.
  - Checklist (copy-paste this exactly):

```markdown
## Checklist

- [ ] Branch named `topic/<slug>`
- [ ] Doc page at `docs/<category>/<slug>.md` using `topic.md` template
- [ ] Transcript at `conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` with YAML frontmatter
- [ ] Index row added to `docs/_index/README.md`
- [ ] All `{REQUIRED}` placeholders filled
- [ ] No empty sections
```

- **Do not squash-merge.** Use standard merge to preserve commit history.

---

## Validation Rules (for agents)

Before creating a PR, verify:

1. `ls docs/<category>/<slug>.md` → file exists.
2. `ls conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` → file exists.
3. `grep '<slug>' docs/_index/README.md` → row exists in index.
4. `grep '{REQUIRED}' docs/<category>/<slug>.md` → returns **zero** matches (all filled).
5. Branch name starts with `topic/`.

If any check fails, **stop and fix before opening the PR.**

---

## Quick Reference

| Rule | Value |
|------|-------|
| Branch pattern | `topic/<slug>` |
| Doc path | `docs/<category>/<slug>.md` |
| Transcript path | `conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` |
| Index path | `docs/_index/README.md` |
| PR title format | `docs(<category>): <slug>` |
| Valid categories | `models`, `tools`, `skills`, `repos`, `agents` |
| Template | `docs/_templates/topic.md` |
| Direct commits to main | **Forbidden** |
