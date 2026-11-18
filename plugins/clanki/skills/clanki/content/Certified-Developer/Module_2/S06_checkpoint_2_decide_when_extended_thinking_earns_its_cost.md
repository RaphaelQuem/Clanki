# Checkpoint 2 · Decide when extended thinking earns its cost

> **Screen:** S06 · **Section:** Extended Thinking
>
> **Category:** Checkpoint Extended Thinking · 3 min

Three tasks are described below. Match each task on the left to the correct extended-thinking decision on the right. There is one correct call per task.

## Scenarios and answers

1. **Classify 50,000 support tickets into three labels overnight.**
   - Answer: Leave it off.
   - Why: A one-word label needs no reasoning pass. Reasoning tokens on every ticket multiply output cost across 50,000 calls with no accuracy gain.
2. **Plan a multi-step refactor where each step depends on the previous one.**
   - Answer: Enable it, budget for the planning step.
   - Why: Without the reasoning pass the model commits to a plan before working through the dependencies, and the first dependent step fails.
3. **Strip the thinking block out of conversation history to save context before the next tool call.**
   - Answer: Never do this.
   - Why: The signature no longer matches and the next request is rejected. Manage accumulated reasoning with context engineering, not by editing the blocks.
