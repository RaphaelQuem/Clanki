# Claude API Foundations & Architecture Reference

## 1. Core Mechanics: Tokens, Context Window & Sampling

### Tokens: The Unit of Input, Output, and Cost
Claude does not read characters or words directly. It reads tokens, and the characters-per-token average depends on the tokenizer of the model at hand and differs between model generations. Treat any chars-per-token rule of thumb as model-dependent and confirm current tokenizer behavior at build time.

Everything the model processes is counted in tokens:
* Your prompt
* The conversation history
* Tool definitions
* Tool results
* The response the model generates

Tokens are the unit of both pricing and budget, so when you estimate what a feature costs or whether an input fits, you are counting tokens, not words. A useful habit is to think in tokens, since that is the unit the API bills in and the context window measures.

### The Context Window: A Fixed Budget
The context window is the total number of tokens the model can take in for a single request. It holds everything at once: the system prompt, the full conversation so far, any documents you inject, every tool result, and the model output. It is a fixed budget with two distinct edge behaviors:
* **Input exceeds window:** A request whose input is already larger than the window is rejected with a validation error before generation begins.
* **Ceiling reached during generation:** A request that fits on input can still reach the ceiling during generation. Current models then stop and return the output generated so far with a `model_context_window_exceeded` stop reason rather than raising an error.

Either way, keeping a long session running requires the application to trim or summarize history before each call. In development, the window rarely fills because test inputs are short. In production, on the other hand, longer inputs and more turns fill the window faster. This is the failure Module 2 explores in detail.

### Sampling: Why the Same Prompt Can Give Different Answers
A language model does not pick one fixed next token. At each step it produces a probability distribution over possible next tokens and then samples from it. Settings, such as `temperature`, shape that distribution:
* A **lower temperature** concentrates probability on the most likely tokens and makes output more repeatable.
* A **higher temperature** spreads it out and makes output more varied.

Because the choice is sampled rather than fixed, the same prompt run twice can return different wording even when both answers are correct. This is a property of how the model generates.

> **Important API Note:** Sampling controls are model-dependent: the newest Claude models do not accept non-default sampling parameters. Setting `temperature`, `top_p`, or `top_k` returns a `400` error, and behavior on those models is steered through prompting instead. Even where `temperature` is accepted, `temperature` `0` makes outputs more repeatable but does not guarantee identical outputs across calls. Confirm current parameter support in the API reference at build time.

### Non-Determinism: What It Means for Testing and Evals
Non-determinism is the primary consequence of sampling: identical inputs do not guarantee identical outputs. That changes how you test a Claude feature. A test that asserts the exact text of a response will be inconsistent, because the model can express the same correct answer many ways.

Instead:
* Assert on the **property that must hold**: a required field is present, a value is in range, the structure parses.
* When you need to judge **meaning rather than structure**, use an **eval with a model-graded judge**.

This is why the course treats evals as the standard for knowing a feature is correct, and why Module 3 builds that capability.

---

## 2. The Claude Model Family & Reasoning Modes

### The Claude Model Family
Claude is a family of models that currently spans four tiers: **Fable**, **Opus**, **Sonnet**, and **Haiku**. Each model represents a different tradeoff across cost, latency, and capability:
* **Sonnet:** The balanced default for most production workloads.
* **Haiku:** Built for speed and cost efficiency on tasks that fit its capability envelope.
* **Opus:** Handles demanding work above the Sonnet envelope.
* **Fable:** The most capable tier, built for the most demanding reasoning, coding, and agentic work where maximum intelligence is the priority.

**Practical Default Workflow:** Start with **Sonnet**, move up a tier only when an eval shows the current tier missing your quality bar, and move down to **Haiku** only when an eval shows the quality drop is acceptable for the task. Confirm the current model lineup and identifiers against `platform.claude.com/docs` at build time, since the Claude family is evolving.

### Reasoning Modes Are a Separate Setting from Model Choice
Choosing which model to run is one decision. Whether the model reasons before answering is a separate decision you make per call.
* On current models the reasoning mode is **adaptive thinking**: the model decides when and how much to think, and you tune depth with an `effort` setting rather than a fixed token budget (the older `budget_tokens` control is deprecated and, on the newest model generations, returns a `400` error).
* Thinking content is omitted from responses by default on the newest models. Request summarized display when you need to show it.
* Reasoning earns its cost on hard, multi-step problems and is wasted on lookups and classification.

The key point for this module is that the two levers compose: model choice picks the family member, while the reasoning mode is configured per request. Per-model defaults differ (some of the newest models think adaptively by default or always), so confirm the current thinking defaults for your model at build time.

### How the Two Work Together
Because model choice and reasoning mode are independent, each can be set separately:
* A capable model with reasoning off is fast and direct.
* A smaller model with reasoning on spends more tokens to think.
* The most demanding tasks pair a capable model with a higher `effort` setting.

Module 2 teaches the mechanics of enabling reasoning and handling the thinking blocks it returns. The decision of which model to run, weighed against cost, latency, and quality, is taken up in Module 4.

---

## 3. Prompting Modes: Zero-Shot, One-Shot, Multi-Shot

### The Three Modes
Separate from how you word a prompt is how many worked examples you give the model inside it:
* **Zero-shot:** Gives the instruction and no examples: you describe the task and ask for the result.
* **One-shot:** Adds one example of the input paired with the desired output.
* **Multi-shot (few-shot):** Includes several such examples.

The examples are not training data; they sit in the prompt and show the model the exact shape of the answer you want, which a description alone often fails to pin down.

### The Cost and Quality Trade-Off
Each example you add costs tokens on every call and consumes context budget, so the choice trades quality against cost:
* Reach for **zero-shot** when the task is simple and the output shape is obvious.
* Move to **one-shot** or **multi-shot** when the output has a specific structure, casing, or edge case that a description keeps missing.

Often one or two correct examples usually fix the issue faster than another paragraph of instructions. The general discipline, which Module 2 reinforces, is to add the smallest amount of prompt that produces a reliable result.

### Mode Choice Interacts with Model Choice
Prompting mode and model choice are related levers. A more capable model often succeeds zero-shot on a task where a smaller model needs a few examples to match the structure, so adding examples can let a cheaper model do the job. The two decisions are worth making together: try the simplest model and the fewest examples that meet your eval, and add capability or examples only where the eval says you need them.

---

## 4. The Technical Substrate: SDKs, REST, Streaming, Async

### How a Developer Reaches Claude: SDK versus Raw REST
At its core, Claude is reached over an **HTTP REST API**: your code sends a request to an endpoint with your API key and a JSON body, and reads a JSON response back. You can call that endpoint directly with any HTTP client.

More commonly you use an **official SDK**, available for Python and TypeScript among others, which is a thin convenience layer over the same REST API. It handles authentication, request construction, retries, and response parsing so you write less boilerplate. The SDK and raw REST reach the same API and the same model. The SDK saves you from assembling requests by hand. Module 2 builds against the SDK and the Messages API, which sits on this same foundation.

### Synchronous, Streaming, and Real-Time Responses
* **Synchronous request:** The simplest pattern: you send the request and wait for the complete response to come back in one piece, then act on it. That is fine for short responses and backend jobs where no one is waiting.
* **Streaming:** When a response is long or a user is watching, streaming sends the response in pieces as the model generates it. Output appears immediately rather than after a blank-screen wait, and your code reassembles the pieces into the final message. Claude exposes streaming over the same HTTP connection using server-sent events. Module 2 teaches how to consume a stream safely and recover when it is interrupted.

### Asynchronous Patterns for High-Volume Work
Two patterns address high-volume work, and they solve different problems:

1. **Non-Blocking Async Clients (Real-Time Concurrency):**
   * The Python SDK exposes an async client (`AsyncAnthropic`) that uses non-blocking `async`/`await` to make API calls without tying up your application thread.
   * In the TypeScript SDK the standard Anthropic client is `Promise`-based, so you `await` calls directly. There is no separate async client class.
   * Either way the request still returns in real time, but your application can handle other work while it waits. This is the right pattern when you need concurrency without blocking.

2. **Message Batches API (Bulk Offline Workloads):**
   * You submit a large set of requests in one call, receive an identifier, and poll for completion.
   * Batch jobs can take up to 24 hours to complete and run at a lower per-token cost in exchange for that latency.
   * This suits offline pipelines, evaluation runs, and bulk jobs where no user is waiting on each result and cost matters more than turnaround time.

---

## 5. Module Quiz

### Question 1
**A teammate says two identical prompts must return identical text. What is the most accurate response?**
* **A.** That is true, the model is deterministic.
* **B.** Not necessarily, the model samples each next token from a probability distribution, so wording can vary even when both answers are correct.
* **C.** That is only true if streaming is off.
* **D.** That is only true on the largest model.

> **Correct Answer: B**
> **Explanation:** Sampling makes generation non-deterministic, which is why tests assert on properties and meaning rather than exact text.

### Question 2
**Which statement best separates model choice from reasoning mode?**
* **A.** They are the same setting.
* **B.** Extended thinking is a different model.
* **C.** Model choice picks which member of the family runs; extended thinking is a per-call setting that any supporting model can run with on or off.
* **D.** Reasoning mode is fixed per account.

> **Correct Answer: C**
> **Explanation:** The two levers are independent but can work together.

### Question 3
**A short, well-specified classification task returns the right answer zero-shot. What does adding three examples most likely do?**
* **A.** Improves accuracy substantially.
* **B.** Adds token cost on every call for little or no gain.
* **C.** Changes the model being used.
* **D.** Disables sampling.

> **Correct Answer: B**
> **Explanation:** Add examples when the output shape is wrong, not when zero-shot already works.

### Question 4
**You must process thousands of inputs offline at the lowest cost. Which shape fits?**
* **A.** Synchronous calls in a loop.
* **B.** Streaming.
* **C.** Batch submission with polling.
* **D.** A larger context window.

> **Correct Answer: C**
> **Explanation:** Batch trades latency for lower per-token cost and avoids the rate limits a synchronous loop hits.

---

## 6. Exercise: Predict the Behavior

*Each scenario below presents a configuration drawn from one of this module’s four foundations: sampling, prompting mode, request shape, and the context budget. For each one, select the answer that predicts the correct behavior and identifies the reason why. Partial credit is available when you answer three of four correctly.*

### Scenario 1
**Consider a classification task run at temperature 0 versus the same task run at a high temperature. Predict how the outputs differ across repeated runs.**
* **A.** At a low temperature, the model concentrates probability on the most likely tokens, so repeated runs return the same label far more consistently, though never with guaranteed determinism, even at temperature 0. At a high temperature, the distribution spreads out, so wording and even the chosen label can vary. For a classifier you want the low-temperature, repeatable behavior.
* **B.** Both configurations return identical output every run, because temperature only affects response length, not which tokens are chosen.
* **C.** The high-temperature run is more accurate, because spreading the distribution lets the model consider more of the correct answers.
* **D.** Temperature has no effect on a classification task, because classification always returns a fixed label regardless of sampling.

> **Correct Answer: A**
> **Explanation:** Temperature shapes the probability distribution the model samples from, it does not fix or disable the output. Lower temperature concentrates probability on the most likely tokens, making repeated runs far more consistent (identical outputs are never guaranteed, even at temperature `0`), which is what a classifier needs. On the newest models, sampling parameters are omitted entirely. Non-default values return an error, and repeatability is managed through prompt design. Higher temperature spreads the distribution and lets the label and wording vary.

### Scenario 2
**Consider a task that keeps returning output in the wrong structure under a zero-shot prompt. Predict what changes if you switch to multi-shot.**
* **A.** Switching to multi-shot retrains the model on the new structure, so the change is permanent across every future call once the examples are sent.
* **B.** Adding two or three correct input-output examples shows the model the exact structure to match, which usually fixes a structure problem that more instruction text did not. The cost is extra tokens on every call, so add the fewest examples that make the output reliable.
* **C.** Multi-shot will not help a structure problem; only raising the temperature changes the shape of the output.
* **D.** Multi-shot lowers the token cost per call, because examples let the model produce shorter responses.

> **Correct Answer: B**
> **Explanation:** Examples in a prompt are not training and do not lower cost. They sit in the prompt, demonstrate the exact output shape, and add tokens on every call. Reach for multi-shot when the structure is wrong, and add the fewest examples that make the output reliable.

### Scenario 3
**Consider a pipeline that must process 50,000 documents overnight with no user waiting. Predict which request shape fits and why.**
* **A.** A synchronous loop fits best, because calling the API once per document is the simplest pattern and avoids the overhead of submitting a batch.
* **B.** Streaming fits best, because sending the response in pieces lets the pipeline start processing each document sooner.
* **C.** The batch pattern fits: submit the requests in a batch and poll for completion, accepting longer latency for a lower per-token cost. A synchronous loop would hit rate limits and tie up the application, and streaming buys nothing because no user is watching.
* **D.** A larger context window fits best, because fitting all 50,000 documents into one request avoids making repeated calls.

> **Correct Answer: C**
> **Explanation:** Match the request shape to whether a user is waiting. With no user watching and tens of thousands of inputs, the Message Batches API is the right fit: submit the requests in one call, poll for completion, and accept longer latency in exchange for a lower per-token cost. A synchronous loop hits rate limits at this volume. Streaming only helps when someone is watching the output arrive.

### Scenario 4
**Consider a long multi-turn agent session whose context window keeps filling. Predict the symptoms and name the budget at fault.**
* **A.** The model silently drops the oldest turns to make room, so the session continues but quietly loses early context without any error.
* **B.** The context window is a fixed token budget; as history and tool results accumulate it fills. An input that is already oversized is rejected with an error before generation, while a request that fits on input but reaches the ceiling mid-generation comes back with truncated output and a `model_context_window_exceeded` stop reason. The symptom is a session that ran fine in testing failing once inputs grow, which is why the application must trim or summarize history.
* **C.** The symptom is slower sampling, and the budget at fault is the temperature setting, which must be lowered as the session grows.
* **D.** There is no fixed budget; the window expands automatically to hold whatever history accumulates, so a long session never fails for this reason.

> **Correct Answer: B**
> **Explanation:** The context window is a fixed budget. An oversized input is rejected with an error before generation; a request that reaches the ceiling mid-generation instead comes back truncated with a `model_context_window_exceeded` stop reason. The oldest content is never silently dropped. The symptom is a session that passed in testing failing once inputs and turns grow, which is why trimming or summarizing history is the application's job.

---

## 7. Recap: Five Takeaways

1. **Tokens are the unit of input, output, and cost.**
   Think and budget in tokens rather than words, since that is what the API meters and the context window measures.
2. **The context window is a fixed token budget that holds the whole request at once.**
   An oversized input errors before generation, while hitting the ceiling mid-generation returns truncated output with a `model_context_window_exceeded` stop reason, so managing history is the application's job.
3. **Sampling makes generation non-deterministic.**
   The same prompt can return different wording on each run, so testing on exact text is unreliable. This is what evals are built for.
4. **Model choice and reasoning mode are separate, composable levers.**
   Pick the smallest model and the simplest reasoning and prompting that meet your eval and add capability only where the eval says you need it.
5. **A developer reaches Claude over a REST API, usually through an SDK.**
   Choose between synchronous, streaming, async/await, or batch based on whether a user is waiting and whether the workload is real-time or bulk offline.

> **What comes next:** Module 2 puts these foundations to work across prompting craft, tool schemas, streaming, context engineering, and agent construction.