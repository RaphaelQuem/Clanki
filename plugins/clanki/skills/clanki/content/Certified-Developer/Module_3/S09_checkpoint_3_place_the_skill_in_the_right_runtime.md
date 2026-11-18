# Checkpoint 3: place the skill in the right runtime

> **Screen:** S09 · **Section:** Packaging Workflows
>
> **Category:** Checkpoint Packaging Workflows · 3 min

Try it now. Three teams want to reuse the same review-checklist skill in different places.

For each, match what must be configured for the skill to load and run. Note: the source presents four runtime situations. All four are included here so the match stays complete.

## Options

- Place SKILL.md in .claude/skills with a description that matches review requests.
- Send the code-execution and skills beta headers and write the skill so its steps do not depend on local files or local tools.
- Enable filesystem sources by setting settingSources explicitly so the agent loads skills from the project. Do not rely on a default, and confirm current default behavior against the Agent SDK reference at build time.
- Define the agent as an API resource that lists the skill and set the managed-agents-2026-04-01 beta header on the calls. Write the skill so its steps do not depend on local files, because it will run in Anthropic’s sandbox.

## Scenarios and answers

1. **A developer wants the skill to load when they ask for a review in the Claude Code terminal.**
   - Answer: Place SKILL.md in .claude/skills with a description that matches review requests.
   - Why: This is the filesystem-discovery runtime: Claude Code.
2. **A service calls the Messages API and wants the skill to run as part of the request.**
   - Answer: Send the code-execution and skills beta headers and write the skill so its steps do not depend on local files or local tools.
   - Why: The Messages API runs the skill inside Anthropic’s code execution container, not your machine.
3. **A scheduled headless job uses Agent SDK and expects the skill from the repo to load.**
   - Answer: Enable filesystem sources by setting settingSources explicitly so the agent loads skills from the project. Do not rely on a default, and confirm current default behavior against the Agent SDK reference at build time.
   - Why: Whether filesystem settings load in the Agent SDK is controlled by the settingSources configuration; set it explicitly rather than relying on a default.
4. **A product team wants the same review-checklist skill to run inside a long-running agent that Anthropic hosts, reachable by an agent ID across sessions.**
   - Answer: Define the agent as an API resource that lists the skill and set the managed-agents-2026-04-01 beta header on the calls. Write the skill so its steps do not depend on local files, because it will run in Anthropic’s sandbox.
   - Why: Claude Managed Agents load the skill server-side as part of the agent resource definition.
