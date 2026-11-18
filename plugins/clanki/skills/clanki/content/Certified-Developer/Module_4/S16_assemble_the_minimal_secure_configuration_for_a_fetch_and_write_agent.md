# Assemble the minimal secure configuration for a fetch-and-write agent

> **Screen:** S16 · **Section:** Security
>
> **Category:** Checkpoint Security · 10 min

The scenario is an agent that fetches untrusted web content and writes to a single protected path while acting under a scoped identity. Assemble the minimal configuration for this agent. Write the four controls it must include and explain in one sentence what each one enforces. Leave out anything that does not belong.

**Piece 1 · hook on a lifecycle event**

```
on: PreToolUse                      # runs before the tool executes
if tool == "write_file" and not path.startswith("/workspace/output"):
    deny("write outside permitted path")   # returns permissionDecision: "deny"
```

**Piece 2 · deny rule**

```
deny_paths: ["/etc", "/secrets", "~/.aws"]   # explicit filesystem denies
```

**Piece 3 · secret reference**

```
api_key: os.environ["SERVICE_API_KEY"]   # not committed config
```

**Piece 4 · audit-log line**

```
log_audit(action, path, result)     # on every privileged action
```

## Model answer

**Piece 1: PreToolUse hook.** Runs before write\_file executes. Blocks any write outside /workspace/output and logs the attempt. Enforcement happens before the tool runs, this is a guardrail, not a convention.

**Piece 2: Deny rule.** Explicitly denies access to /etc, /secrets, and ~/.aws. Limits the blast radius if the agent is steered by a prompt injection.

**Piece 3: Environment variable reference.** Pulls the API key from os.environ rather than committed configuration. A credential committed inline enters repository history and cannot be cleanly rotated.

**Piece 4: Audit log.** Records every privileged action with its result. Provides the evidence trail a regulated review requires and makes enforcement visible.

All four pieces belong. The hook blocks before the tool runs, the deny rule restricts the accessible filesystem, the env reference keeps the credential out of source control, and the audit log creates the record. The injected write hits the hook block and the audit log, not the disk.

All four controls belong: the hook, the deny rule, the environment reference, and the audit log.
