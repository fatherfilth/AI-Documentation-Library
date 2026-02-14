# AI Documentation Library

A living knowledge base for AI models, tools, skills, repos, and agents — built automatically from conversations with Claude. Includes a daily newsletter with learnings, AI news, and benchmarks.

## How It Works

### Documentation

1. **Start a chat** in the Claude Project linked to this repo.
2. **Talk about any AI topic** — the agent auto-detects the category and generates a slug.
3. **When you're done**, the agent confirms and opens a PR with a curated doc page, a conversation transcript, and an index update.
4. **You merge** — the library grows.

No manual categorization. No "Publish" command. Just talk and review.

### Daily Newsletter

Every day at 06:00 UTC, a GitHub Actions workflow:

1. Collects merged PRs from the past 24 hours (your learnings).
2. Reads any links you dropped in `newsletters/inbox/`.
3. Reads benchmark data from `benchmarks/`.
4. Opens a GitHub issue for the agent to search for AI news and generate the newsletter.
5. Archives the result to `newsletters/YYYY/MM/YYYY-MM-DD.md`.
6. Sends the newsletter via SMTP.

You can also trigger it manually via `workflow_dispatch`.

## Repository Structure

```
.
├── .claude/
│   └── PROJECT_INSTRUCTIONS.md       # Agent brain — paste into Claude Project custom instructions
│
├── .github/
│   └── workflows/
│       ├── pr-validation.yml         # CI: validates every topic PR before merge
│       ├── daily-newsletter.yml      # Cron: collects data, creates issue, sends email via SMTP
│       └── validate-benchmarks.yml   # CI: validates benchmark JSON files
│
├── docs/
│   ├── models/                       # AI model capabilities, benchmarks, prompting patterns
│   ├── tools/                        # Software tools, libraries, CLIs, APIs, integrations
│   ├── skills/                       # Techniques, workflows, methodologies
│   ├── repos/                        # GitHub repository docs — structure, usage, patterns
│   ├── agents/                       # Agent architectures, orchestration, multi-agent systems
│   ├── _index/
│   │   └── README.md                 # Master index — one row per topic, sorted by slug
│   └── _templates/
│       └── topic.md                  # Strict template every doc page must follow
│
├── newsletters/
│   ├── _templates/
│   │   ├── daily.md                  # Markdown template for newsletter archive
│   │   └── email.html                # HTML template for SMTP email delivery
│   ├── inbox/                        # Drop links + notes here throughout the day
│   │   └── README.md                 # Instructions for the inbox
│   ├── config.json                   # Newsletter sources, schedule, delivery config
│   └── 2026/                         # Archived newsletters by year/month
│       └── MM/
│           └── YYYY-MM-DD.md
│
├── benchmarks/
│   ├── models/                       # Public leaderboard snapshots (LMSYS, HumanEval, MMLU)
│   │   └── YYYY-MM-DD.json
│   ├── evals/                        # Personal eval results
│   │   └── YYYY-MM-DD__eval-name.json
│   ├── tools/                        # Tool performance metrics
│   │   └── YYYY-MM-DD__tool-name.json
│   └── README.md                     # Schemas and rules
│
├── conversations/
│   └── 2026/                         # Transcripts archived by year → date__slug
│
├── CONTRIBUTING.md                   # Rules — naming, paths, PR format, CI, sourcing
└── README.md                         # ← You are here
```

## Categories

| Category | Folder | What goes here |
|----------|--------|----------------|
| Models | `docs/models/` | AI model capabilities, behavior, architecture, benchmarks, prompting patterns |
| Tools | `docs/tools/` | Software tools, libraries, CLIs, APIs, integrations |
| Skills | `docs/skills/` | Techniques, workflows, methodologies, transferable practices |
| Repos | `docs/repos/` | Specific GitHub repositories — structure, usage, contribution patterns |
| Agents | `docs/agents/` | AI agent architectures, configurations, orchestration, multi-agent systems |

## Newsletter Content Streams

| Stream | Source | How it works |
|--------|--------|--------------|
| **Your Learnings** | Merged PRs in this repo | Auto-collected by the daily workflow |
| **AI News** | Web search + manual inbox | Agent searches 5 categories; you drop links in `newsletters/inbox/YYYY-MM-DD.md` |
| **Leaderboard Watch** | LMSYS, HumanEval, MMLU | Agent searches for changes; snapshots saved to `benchmarks/models/` |
| **Personal Evals** | `benchmarks/evals/*.json` | You commit eval results; workflow picks them up |
| **Tool Metrics** | `benchmarks/tools/*.json` | You commit metrics; workflow picks them up |

## Contributing

Every documentation contribution follows the same workflow — enforced by CI and agent instructions:

- **Branch:** `topic/<slug>` (no direct commits to `main`)
- **Doc page:** `docs/<category>/<slug>.md` (must use `topic.md` template, no unfilled placeholders)
- **Transcript:** `conversations/YYYY/YYYY-MM-DD__<slug>/transcript.md` (YAML frontmatter required)
- **Index row:** added to `docs/_index/README.md`

Full rules in [`CONTRIBUTING.md`](CONTRIBUTING.md).

## Setup

### Claude Project

1. Create a new **Claude Project** at [claude.ai](https://claude.ai).
2. Paste the contents of [`.claude/PROJECT_INSTRUCTIONS.md`](.claude/PROJECT_INSTRUCTIONS.md) into **Custom Instructions**.
3. Connect your GitHub integration.
4. Start chatting.

### Newsletter (SMTP)

Add these secrets to the repository (`Settings → Secrets and variables → Actions`):

| Secret | Description |
|--------|-------------|
| `SMTP_SERVER` | SMTP server hostname (e.g. `smtp.gmail.com`) |
| `SMTP_PORT` | SMTP port (e.g. `587`) |
| `SMTP_USERNAME` | SMTP login username |
| `SMTP_PASSWORD` | SMTP login password or app password |
| `NEWSLETTER_TO` | Recipient email address(es) |
| `NEWSLETTER_FROM` | Sender email address |

## CI Workflows

| Workflow | Trigger | What it does |
|----------|---------|--------------|
| `pr-validation.yml` | Every PR to `main` | Validates branch name, doc page, transcript, index, placeholders, secrets |
| `daily-newsletter.yml` | Daily at 06:00 UTC + manual | Collects data, creates generation issue, sends email via SMTP |
| `validate-benchmarks.yml` | Push/PR with benchmark changes | Validates JSON format, file naming, required fields |
