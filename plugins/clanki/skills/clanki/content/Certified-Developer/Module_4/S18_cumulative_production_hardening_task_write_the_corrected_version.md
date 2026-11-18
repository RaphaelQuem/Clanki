# Cumulative production-hardening task: write the corrected version

> **Screen:** S18 · **Section:** Cumulative Task
>
> **Category:** Cumulative Module-Wide · 8 min

Write the corrected version of the application. For each defect you identified, show the fixed code and name what it changes.

**Application from the previous screen (for reference)**

```
def answer(question, page_url):
    page = fetch(page_url)                       # untrusted content

    notes = read_file("/workspace/input/notes")
    write_file(page.suggested_path, summarize(page))

    resp = None
    for i in range(5):
        try:
            resp = client.messages.create(model=MODEL, max_tokens=MAX_TOKENS, messages=msg(question))
            break
        except Exception:
            time.sleep(0)

    return resp.content[0].text
```

## Model answer

```
def answer(question, page_url):
    page = fetch(page_url)                   # still untrusted

    # security: fixed write path + PreToolUse hook enforces it
    notes = read_file("/workspace/input/notes")  # scoped read, unchanged
    write_file("/workspace/output/summary.txt", summarize(page))
    # (hook denies any write outside /workspace/output and audits it)

    # error handling: backoff, honor retry-after, fail fast on terminal
    resp = call_with_retry(
        lambda: client.messages.create(model=MODEL, max_tokens=MAX_TOKENS, messages=msg(question)))

    # eval: answer() is covered by a graded holdout set run on every change
    return resp.content[0].text
```

Each fix sits at the layer it defends: the fixed write path and hook close the security boundary before the tool runs; call\_with\_retry adds exponential backoff with terminal-status detection; the graded holdout set gives the eval a baseline score to fail against on every future change.

The hardened version measures success with an eval, handles failures through explicit retry and backoff logic, and enforces the security boundary before the action with a hook on a fixed path. Each fix sits at the layer it defends, and the three reinforce each other rather than overlapping.
