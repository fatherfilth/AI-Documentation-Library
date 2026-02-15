---
topic: popular-mcp-servers
category: tools
date: 2026-02-15
participants:
  - user
  - claude
source: claude.ai
summary: "Comparative analysis of four trending MCP servers: Playwright, Perplexity, Firecrawl, and Glif — capabilities, trade-offs, and stack fit."
related_topics: []
---

# Transcript: Popular MCP Servers — Playwright, Perplexity, Firecrawl, Glif

## User Request

User asked for an analysis of four MCP servers they'd been seeing frequently on social media:

- **Playwright MCP** — browser automation
- **Perplexity MCP** — web search and research
- **Firecrawl MCP** — web scraping and data extraction
- **Glif MCP** — visual AI workflow execution

## Research Phase

Agent conducted web research across official repos, documentation, and community analysis for each MCP:

### Playwright MCP
- Official Microsoft repo: `@playwright/mcp` (microsoft/playwright-mcp)
- Community alternative: `@executeautomation/playwright-mcp-server` (executeautomation/mcp-playwright)
- Key discovery: Microsoft recently added CLI+SKILLS mode as a more token-efficient alternative to the MCP tool-calling approach
- Integrated into GitHub Copilot Coding Agent natively
- Supports Chromium, Firefox, WebKit; persistent profiles; headed and headless modes

### Perplexity MCP
- Official repo: `@perplexity-ai/mcp-server` (perplexityai/modelcontextprotocol)
- Four tools: search (quick web search), chat (conversational with citations), deep research (multi-source synthesis), reasoning (analytical tasks)
- Recency filtering, domain filtering, academic mode
- Requires paid API key
- Ranked #84 globally on PulseMCP with ~332k estimated downloads

### Firecrawl MCP
- Official repo: `firecrawl-mcp` (firecrawl/firecrawl-mcp-server)
- Five core tools: scrape, crawl, search, extract (structured with JSON schema), map (URL discovery)
- FIRE-1 model handles interactive elements — logins, modals, buttons
- Self-hostable for enterprise use
- Credit-based pricing; crawl responses can be massive and exceed token limits

### Glif MCP
- Official repo: `@glifxyz/glif-mcp-server` (glifxyz/glif-mcp-server)
- Runs visual AI workflows from glif.app — image generation, memes, ComfyUI pipelines
- Meta-tools: save glifs as standalone tools, discover/manage workflows dynamically
- Pre-1.0 (v0.9.9 as of late 2025); ~1.4k downloads
- Only MCP of the four focused on generative media

## Comparative Analysis

Agent produced a side-by-side comparison covering:
- Core job and use case fit
- Backing organization and maturity level
- Free tier availability
- Token cost implications
- Overlap risk with existing tools
- Recommended combinations for agent stacks

## Key Findings

1. **Two Playwright MCPs exist** — the official Microsoft one (`@playwright/mcp`) and a community one (`@executeautomation/playwright-mcp-server`). Easy to confuse; the official one is more actively maintained and has the CLI+SKILLS mode.
2. **Perplexity MCP overlaps with built-in search** in clients like Claude that already have web search. The value add is Perplexity's Sonar models and deep research capability.
3. **Firecrawl's optimal workflow is map→scrape, not crawl** — crawling blindly burns credits and produces massive responses that exceed token limits.
4. **Glif is in a fundamentally different category** — it's generative media, not information retrieval. Comparing it to the other three is apples-to-oranges.
5. **Token footprint varies dramatically** — Playwright's accessibility trees are heavy, Firecrawl crawl responses can be huge, Perplexity is relatively light, Glif is minimal.

## Confusion Points Surfaced

- Two Playwright MCPs (official vs community) with similar names
- Perplexity API key required despite Perplexity having a free consumer tier
- Firecrawl crawl vs scrape distinction (crawl = entire site, scrape = single page)
- Glif requires Claude Desktop restart for new saved tools but Cursor auto-reloads

## Sources Consulted

- https://github.com/microsoft/playwright-mcp
- https://github.com/executeautomation/mcp-playwright
- https://github.com/perplexityai/modelcontextprotocol
- https://docs.perplexity.ai/guides/mcp-server
- https://github.com/firecrawl/firecrawl-mcp-server
- https://docs.firecrawl.dev/mcp-server
- https://github.com/glifxyz/glif-mcp-server
- https://www.pulsemcp.com/servers/perplexity
- https://www.pulsemcp.com/servers/firecrawl
- https://www.pulsemcp.com/servers/glif
- https://til.simonwillison.net/claude-code/playwright-mcp-claude-code
- https://www.firecrawl.dev/blog/launch-week-iii-day-6-firecrawl-mcp
- https://developer.microsoft.com/blog/the-complete-playwright-end-to-end-story-tools-ai-and-real-world-workflows
