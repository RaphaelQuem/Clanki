# Match each task to its agent type and cost lever

> **Screen:** S13 · **Section:** Cost & Orchestration
>
> **Category:** Checkpoint Cost & Orchestration · 8 min

Try it now. For each of the four scenarios below, select the configuration snippet that best matches it. Each snippet is labeled with its agent type and the primary cost lever it uses.

**Labeled configuration snippets**

```
A  orchestrator_worker(lead=LARGE, workers=SMALL, n=5)    # lever: parallel split
B  single_agent(model=SMALL, batch=True, cache=True)      # lever: Message Batches API (~50% cost reduction) + prompt caching
C  single_agent(model=SMALL, retrieval="fetch_once")      # lever: model choice
D  single_agent(model=SMALL, stream=True)                 # lever: streaming
```

## Scenarios and answers

1. **A single-fact lookup against a stable reference corpus**
   - Answer: C
   - Why: Fetch-once retrieval with the smallest model that works; no need to pay for iteration or fan-out.
2. **A broad research question that splits into independent parts explored at once**
   - Answer: A
   - Why: The one task that genuinely parallelizes, so the fan-out multiplier buys parallel exploration.
3. **A user-facing request where the reply should feel instant**
   - Answer: D
   - Why: Streaming so the response is perceived as fast even before it finishes.
4. **A cost-sensitive, non-urgent batch job**
   - Answer: B
   - Why: Batch and cache because latency does not matter and the same context recurs across requests. The Message Batches API processes requests asynchronously and reduces cost by approximately 50%; prompt caching compounds the saving when context is reused across calls.

You matched 1 to C (fetch-once, smallest model that works), 2 to A (the one task that splits into independent parts, so the fan-out multiplier buys parallel exploration), 3 to D (streaming so the reply feels instant), and 4 to B (batch and cache for a cost-sensitive non-urgent job). Your explanations for the incorrect choice correctly identify the real cost, such as paying the fan-out token multiplier on the lookup by choosing A, or forfeiting the Message Batches API's approximately 50% cost reduction by choosing a synchronous path for a non-urgent job.
