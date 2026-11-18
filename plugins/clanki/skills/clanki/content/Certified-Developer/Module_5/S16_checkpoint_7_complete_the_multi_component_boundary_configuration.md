# Checkpoint 7: Complete the multi-component boundary configuration

> **Screen:** S16 · **Section:** Trust Boundaries
>
> **Category:** Checkpoint Multi-component app and trust boundaries · 3 min

Try it now. The multi-component app below is wired, with two blanks left. Place the correct control on the seam that receives untrusted fetched content and the correct identity scope on the most privileged component.

**The partial app**

```
# components wired: API -> Claude Code task -> MCP server
fetched = code_task.run(fetch_url=customer_page)

# BLANK 1: control on the seam receiving untrusted fetched content
next_call(input=[BLANK 1](fetched))

# MCP server reaches the customer system (most privileged component)
mcp_server = MCPServer(
    system=customer_db,
    scope=[BLANK 2],   # BLANK 2: identity scope
)
```


## Token bank (two are distractors)

- `treat_as_data`
- `least_privilege_read_only`
- `run_as_instructions`
- `full_access`

## Answer key

- **Blank 1:** `treat_as_data`
- **Blank 2:** `least_privilege_read_only`

Blank 1 wraps the fetched content, so the next component treats it as data, rather than instructions. This closes the injection seam. Blank 2 scopes the most privileged component to least privilege, so a steered action cannot reach beyond its task. Together they hold both boundaries under review.
