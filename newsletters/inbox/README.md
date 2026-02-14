# Newsletter Inbox

Drop links and notes here throughout the day. The newsletter generator picks them up.

## How to use

Create a file named `YYYY-MM-DD.md` (today's date) with links and notes:

```markdown
## Links

- https://example.com/article — interesting take on prompt caching
- https://arxiv.org/abs/XXXX.XXXXX — new benchmark paper

## Notes

- Ran evals on Claude Opus 4 vs Sonnet 4.5 for code generation
- MCP server latency improved after config change
```

The newsletter agent will:
1. Read today's inbox file
2. Incorporate links into the appropriate news category
3. Include notes in the learnings or benchmarks section
4. Delete the inbox file after newsletter is generated (or archive it)

You can also drop links directly in a Claude chat — say "add to newsletter" and the agent will note it.
