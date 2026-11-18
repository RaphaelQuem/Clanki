# Checkpoint 5: Match the deployment platform and version pin to each scenario

> **Screen:** S10 · **Section:** Deployment & Versioning
>
> **Category:** Checkpoint Deployment platform and versioning · 4 min

Try it now. A customer runs AWS with a data-residency requirement and needs to be able to roll back a model update. Select the one correct piece in each group below to assemble the minimal deployment configuration that satisfies both. Leave out what does not belong.

## Platform Group

- First-party API
- Amazon Bedrock
- Google Vertex AI

**Answer:** Amazon Bedrock

## Identity Group

- AWS identity reference
- Anthropic API key

**Answer:** AWS identity reference

## Model reference group

- A pinned full model ID
- A moving alias

**Answer:** A pinned full model ID

## Rollback Group

- Retain the prior pinned version
- No retention

**Answer:** Retain the prior pinned version

## Why

Bedrock keeps identity and data inside the AWS boundary the customer already cleared, the pinned full Bedrock model ID (with the anthropic. prefix) makes an upstream change something you adopt deliberately, and retaining the prior version gives you a rollback target. That is the minimal set that satisfies both residency and rollback.
