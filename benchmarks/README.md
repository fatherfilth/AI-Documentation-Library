# Benchmarks

Structured data for the daily newsletter's benchmarking section.

## Structure

```
benchmarks/
├── models/          # Public leaderboard snapshots (LMSYS, HumanEval, MMLU, etc.)
│   └── YYYY-MM-DD.json
├── evals/           # Personal eval results
│   └── YYYY-MM-DD__<eval-name>.json
├── tools/           # Tool performance metrics
│   └── YYYY-MM-DD__<tool-name>.json
└── README.md
```

## Schemas

### models/YYYY-MM-DD.json

```json
{
  "date": "YYYY-MM-DD",
  "source": "lmsys-chatbot-arena | humaneval | mmlu | other",
  "leaderboard": [
    {
      "rank": 1,
      "model": "claude-opus-4",
      "score": 1350,
      "previous_rank": 1,
      "delta": 0
    }
  ],
  "url": "https://..."
}
```

### evals/YYYY-MM-DD__<eval-name>.json

```json
{
  "date": "YYYY-MM-DD",
  "eval_name": "code-generation-v2",
  "model": "claude-opus-4",
  "score": 0.87,
  "previous_score": 0.84,
  "delta": 0.03,
  "notes": "Improved on multi-file tasks after prompt change",
  "config": {
    "temperature": 0.3,
    "max_tokens": 4096
  }
}
```

### tools/YYYY-MM-DD__<tool-name>.json

```json
{
  "date": "YYYY-MM-DD",
  "tool": "mcp-server-github",
  "metrics": {
    "avg_response_ms": 230,
    "p99_response_ms": 890,
    "uptime_pct": 99.7,
    "tokens_used": 45200,
    "cost_usd": 1.23
  },
  "previous": {
    "avg_response_ms": 245,
    "p99_response_ms": 920
  },
  "notes": "Latency improved after connection pooling change"
}
```

## Rules

- One file per source per day.
- File names use ISO dates.
- All files are valid JSON (CI will validate).
- Missing fields are `null`, not omitted.
- Deltas are always `current - previous` (positive = improvement for scores, negative = improvement for latency).
