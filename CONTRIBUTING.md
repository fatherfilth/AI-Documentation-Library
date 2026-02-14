# Contributing — AI Documentation Library

> **These rules are non-negotiable.** Every agent and human contributor follows them exactly.

---

## Folder Routing & Naming Conventions

### Category → Folder

| Category | Path |
|----------|------|
| Models | `docs/models/` |
| Tools | `docs/tools/` |
| Skills | `docs/skills/` |
| Repos | `docs/repos/` |
| Agents | `docs/agents/` |

No other category folders exist. If a topic doesn't fit, discuss before creating a new one.

### Slug Rules

| Rule | Example |
|------|---------|
| Format: `tool-name-feature` | `oauth-device-flow`, `mcp-server-config` |
| Lowercase, hyphenated only | ✅ `prompt-caching` · ❌ `Prompt_Caching` |
| No dates in slug | ❌ `mcp-2026-02` |
| Max ~40 characters | ❌ `how-to-configure-advanced-prompt-caching-with-claude` |
| No special characters | ❌ `oauth/device.flow` |

### Conversation Archive Path

```
conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md
```

- Double underscore `__` separates date from slug.
- Date is the conversation date, not the publish date.
- Slug must match the branch slug character-for-character.

---

## Workflow: Adding a New Topic

### Day-to-Day Usage

1. Start a new chat.
2. Declare: `Category: Tools` / `Slug: oauth-device-flow`
3. Work through the topic.
4. Say: **`Publish`**
5. Agent executes: writes transcript + doc + index → opens PR.
6. You merge → repo stays living.

### 1. Branch

```
topic/<slug>
```

- `<slug>` follows the slug rules above.
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
6. Slug is ≤ 40 characters, lowercase, hyphenated, no dates.

If any check fails, **stop and fix before opening the PR.**

---

## Automation Hardening

### A. Filesystem Scope

Agents may only read/write within the repository working tree. No access to home directories, system paths, or other repos. If an agent attempts out-of-scope access, halt the workflow.

### B. GitHub Token Permissions (Minimum Scopes)

| Scope | Purpose |
|-------|---------|
| `contents: write` | Create branches, push commits |
| `pull-requests: write` | Open PRs |
| `metadata: read` | List repo info |

No `admin`, no `delete`, no `actions: write`. Token should be scoped to this single repo.

### C. CI Checks (enforced via GitHub Actions)

The `pr-validation` workflow runs on every PR and **must pass** before merge:

1. **Frontmatter validation** — YAML block in transcript is parseable and has all required keys.
2. **Link format check** — No bare URLs in doc pages; all links must be `[label](url)`.
3. **Secret scan** — No API keys, tokens, or credentials in any file.
4. **Path/category match** — Doc file lives in a valid category folder.
5. **Slug consistency** — Branch slug matches doc filename, transcript folder name, and index row.
6. **Template compliance** — No `{REQUIRED}` placeholders remain in the doc page.

---

## Sourcing & Research Guidelines

Agent-generated research is only as good as the sourcing pipeline.

### Without a research/browsing tool

1. User pastes 3–10 quality reference links.
2. Agent builds a **research pack** (summarized facts, quotes, key data) first.
3. Agent then writes the doc page from the research pack.
4. All sources must appear in the **References** section with labeled links.

### With a research-fetch tool (e.g. MCP server)

- Tool must be **read-only** — no writes to external services.
- All fetched URLs must be logged in the transcript.
- References section is still mandatory — no "trust me, I looked it up."

### Hard rule

> **No references section = PR rejected.** Every claim traces back to a source.

---

## Quick Reference

| Rule | Value |
|------|-------|
| Branch pattern | `topic/<slug>` |
| Slug format | lowercase, hyphenated, ≤40 chars, no dates |
| Doc path | `docs/<category>/<slug>.md` |
| Transcript path | `conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` |
| Index path | `docs/_index/README.md` |
| PR title format | `docs(<category>): <slug>` |
| Valid categories | `models`, `tools`, `skills`, `repos`, `agents` |
| Template | `docs/_templates/topic.md` |
| Direct commits to main | **Forbidden** |
| Empty references section | **Forbidden** |
