# Checkpoint 8 · Select the right input encoding for each scenario

> **Screen:** S26 · **Section:** Multimodal and Batch Ingestion
>
> **Category:** Checkpoint Multimodal and Batch Ingestion · 3 min

Read the three input scenarios below. For each input scenario, select the correct encoding method. Each item's feedback names the cost of the wrong choice.

## Scenarios and answers

1. **A reference product diagram used in every request your pipeline makes**
   - Answer: Files API: upload once, reference file_id in each request
   - Why: Inline base64 would resend the full payload with every request, adding transfer overhead and latency on every call.
2. **A one-off screenshot of a UI bug, submitted by a support engineer in a single request**
   - Answer: Inline base64: encode and include directly in the message block
   - Why: Files uploaded to API add a round-trip step for an asset that will only ever be used once.
3. **A job classifying 5,000 customer feedback responses**
   - Answer: Message Batches API: submit all requests in one batch call, poll for completion
   - Why: Synchronous API would process requests sequentially or require managing thousands of concurrent connections against rate limits.
