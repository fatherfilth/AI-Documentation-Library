# Leaderboard Schema Reference

This file documents the YAML schema for `data.yaml` entries. The documentation agent uses this to validate entries before writing them.

## Entry Schema

```yaml
- name: string          # Required. Display name of the item.
  type: enum            # Required. One of: tool, skill, model, application, workflow
  subcategory: enum     # Required. One of: coding, design, ui-ux, video-gen, prompt-engineering,
                        #   project-building, research, productivity, devops, data, audio, writing, other
  status: enum          # Required. One of: used, researched
  rank: integer         # Required. Position within type + subcategory. 1 = top pick.
  use_case: string      # Required. What this is for / what it's best at.
  where_used: string    # Required if status=used. Project or context. "N/A" if status=researched.
  verdict: string       # Required. One-line personal opinion.
  doc_link: string      # Optional. Relative path to doc in this repo (e.g., ../tools/cursor.md)
  date_added: date      # Required. YYYY-MM-DD when first added.
  date_updated: date    # Required. YYYY-MM-DD when last modified.
```

## Ranking Rules

1. **Rankings are scoped to type + subcategory.** Rank 1 Tool/coding is independent of Rank 1 Tool/design.
2. **No ties.** If two items seem equal, the user must pick one over the other.
3. **Inserting a new entry at rank N bumps all entries at rank ≥ N down by 1.**
4. **An item can appear in multiple subcategories** with different ranks (e.g., Cursor could be Rank 1 in Tool/coding and Rank 2 in Tool/ui-ux).
5. **Status transitions:** An entry can move from `researched` → `used` when the user starts using it. This should update `status`, `where_used`, and `date_updated`.

## Agent Behavior

When the agent detects a tool/skill/model/application/workflow mentioned in conversation:

1. **Check if it already exists in `data.yaml`**
2. **If it exists:** No action needed unless the user shares new ranking info
3. **If it doesn't exist:** Ask the user:
   - "Have you used [X] or are you just researching it?"
   - If used: "Where did you use it? How would you rank it for [inferred subcategory]?"
   - If researched: "Got it — I'll add it as researched. Any first impressions on where it ranks?"
4. **Add the entry** to `data.yaml` and note it at publish time
5. **If ranking changes:** Re-number affected entries to maintain contiguous ranks

## Rendering

The README.md leaderboard tables are rendered from `data.yaml` between the `<!-- LEADERBOARD_START -->` and `<!-- LEADERBOARD_END -->` markers. The format is:

```markdown
### 🔧 Tools

#### Coding

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | Cursor | 🟢 Used | AI-assisted coding | Best AI code editor |
| 2 | VS Code | 🟢 Used | Traditional coding | Solid without AI features |

#### Design

| Rank | Name | Status | Use Case | Verdict |
|------|------|--------|----------|---------|
| 1 | v0 | 🔵 Researched | Prompt-to-UI | Strong for React/Next.js |
```

Type headers use these emojis:
- 🔧 Tools
- 🧠 Skills
- 🤖 Models
- 📱 Applications
- 🔄 Workflows
