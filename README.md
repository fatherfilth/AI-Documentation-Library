# AI Documentation Library

A living knowledge base for AI models, tools, skills, repos, and agents — built automatically from conversations with Claude.

## How It Works

1. **Start a chat** in the Claude Project linked to this repo.
2. **Talk about any AI topic** — the agent auto-detects the category and generates a slug.
3. **When you're done**, the agent confirms and opens a PR with a curated doc page, a conversation transcript, and an index update.
4. **You merge** — the library grows.

No manual categorization. No "Publish" command. Just talk and review.

## Repository Structure

```
.
├── .claude/
│   └── PROJECT_INSTRUCTIONS.md   # Agent brain — paste into Claude Project custom instructions
│
├── .github/
│   └── workflows/
│       └── pr-validation.yml     # CI: validates every PR before merge
│
├── docs/
│   ├── models/                   # AI model capabilities, benchmarks, prompting patterns
│   ├── tools/                    # Software tools, libraries, CLIs, APIs, integrations
│   ├── skills/                   # Techniques, workflows, methodologies
│   ├── repos/                    # GitHub repository docs — structure, usage, patterns
│   ├── agents/                   # Agent architectures, orchestration, multi-agent systems
│   ├── _index/
│   │   └── README.md             # Master index — one row per topic, sorted by slug
│   └── _templates/
│       └── topic.md              # Strict template every doc page must follow
│
├── conversations/
│   └── 2026/                     # Transcripts archived by year → date__slug
│
├── CONTRIBUTING.md               # Rules of engagement — naming, paths, PR format, CI, sourcing
└── README.md                     # ← You are here
```

## Categories

| Category | Folder | What goes here |
|----------|--------|----------------|
| Models | `docs/models/` | AI model capabilities, behavior, architecture, benchmarks, prompting patterns |
| Tools | `docs/tools/` | Software tools, libraries, CLIs, APIs, integrations |
| Skills | `docs/skills/` | Techniques, workflows, methodologies, transferable practices |
| Repos | `docs/repos/` | Specific GitHub repositories — structure, usage, contribution patterns |
| Agents | `docs/agents/` | AI agent architectures, configurations, orchestration, multi-agent systems |

## Contributing

Every contribution follows the same workflow — enforced by CI and agent instructions:

- **Branch:** `topic/<slug>` (no direct commits to `main`)
- **Doc page:** `docs/<category>/<slug>.md` (must use `topic.md` template, no unfilled placeholders)
- **Transcript:** `conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` (YAML frontmatter required)
- **Index row:** added to `docs/_index/README.md`

Full rules in [`CONTRIBUTING.md`](CONTRIBUTING.md).

## Setup

To use this as a Claude Project:

1. Create a new **Claude Project** at [claude.ai](https://claude.ai).
2. Open the project settings.
3. Paste the contents of [`.claude/PROJECT_INSTRUCTIONS.md`](.claude/PROJECT_INSTRUCTIONS.md) into the **Custom Instructions** box.
4. Connect your GitHub integration (so the agent can create branches, push, and open PRs).
5. Start chatting.

## CI Validation

Every PR is validated by GitHub Actions before it can be merged:

- ✅ Branch name matches `topic/<slug>`
- ✅ Doc page exists in a valid category folder
- ✅ No `{REQUIRED}` placeholders remain
- ✅ Transcript exists with valid YAML frontmatter
- ✅ Index row present
- ✅ No secrets or credentials in any file
