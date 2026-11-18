# Checkpoint 5: match transport and scope to each deployment scenario

> **Screen:** S14 · **Section:** MCP Servers
>
> **Category:** Checkpoint MCP Servers · 4 min

Try it now. For each deployment scenario below, select the correct transport and scope.

Labelled configuration snippets are provided.

## Options

- stdio + Local
- HTTP + Project (.mcp.json)
- stdio or HTTP + Local
- HTTP + Enterprise (managed settings)

## Scenarios and answers

1. **A local SQLite query tool you use only on your development machine.**
   - Answer: stdio + Local
2. **A code search service hosted on your company’s infrastructure that the whole engineering team should access.**
   - Answer: HTTP + Project (.mcp.json)
3. **An experimental web-scraping server you are testing this week against one specific repository, not ready to share.**
   - Answer: stdio or HTTP + Local
4. **A security-scanning server your organization’s IT team needs deployed to every developer’s Claude Code installation.**
   - Answer: HTTP + Enterprise (managed settings)
