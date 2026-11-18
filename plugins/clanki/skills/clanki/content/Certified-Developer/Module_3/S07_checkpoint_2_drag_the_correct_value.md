# Checkpoint 2: drag the correct value

> **Screen:** S07 · **Section:** Durable Project Context
>
> **Category:** Checkpoint Durable Project Context · 3 min

Try it now. You are setting up a hook that enforces a path restriction, and the configuration below has two blanks.

Select the correct one: the lifecycle event that runs before a tool call executes, and the command the hook runs to block reads of `.env.production`.

```
{
  "hooks": {
    "________": [
      {
        "matcher": "Read",
        "hooks": [{ "type": "command", "command": "________" }]
      }
    ]
  }
}
```

## Blank 1: the lifecycle event

- **A.** PreToolUse
- **B.** PostToolUse
- **C.** UserPromptSubmit
- **D.** SessionStart

## Blank 2: the command

- **A.** A script that reads the tool call from stdin, checks the file path, and exits with code 2 when the path is .env.production (writing the reason to stderr).
- **B.** A script that logs the tool call to an audit file and exits 0.
- **C.** A script that prints a warning and exits 0 unconditionally.

## Answer key

- **Blank 1:** PreToolUse
- **Blank 2:** A script that reads the tool call from stdin, checks the file path, and exits with code 2 when the path is .env.production (writing the reason to stderr).

PreToolUse fires before the tool call executes, which is when blocking is possible. A PostToolUse hook cannot block because the tool has already run. The command reads the tool call from stdin, checks the file path, and exits with code 2 to block it, and anything written to stderr becomes the message Claude sees.

- **Why not PostToolUse:** PostToolUse fires after the tool has already executed, so a hook on that event cannot prevent the read from happening. You need the event that intercepts the tool call before it runs, which is PreToolUse.
