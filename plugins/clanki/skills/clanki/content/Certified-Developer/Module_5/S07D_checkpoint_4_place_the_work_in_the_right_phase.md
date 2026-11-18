# Checkpoint 4: place the work in the right phase

> **Screen:** S07D · **Section:** Requirements & Lifecycle
>
> **Category:** Checkpoint Requirements & Lifecycle · 2 min

Try it now. Place each activity in the lifecycle phase it belongs to: requirements, design, test, deploy, operate.

## Activities and lifecycle phases

Options: "requirements" · "design" · "test" · "deploy" · "operate"

1. **(a) pinning the full model ID and keeping the prior version**
   - Answer: deploy
   - Why: Pinning the version and retaining the prior one is a deploy decision.
2. **(b) gating promotion on the eval result before a version goes to production**
   - Answer: deploy
   - Why: Gating promotion on an eval result before production is a deploy decision.
3. **(c) deciding data must be processed in a specific region**
   - Answer: requirements
   - Why: The residency rule is a requirement.
4. **(d) instrumenting token cost and latency per call in production**
   - Answer: operate
   - Why: Instrumenting cost and latency in production is operate.
5. **(e) choosing Amazon Bedrock because the customer holds its compliance posture there**
   - Answer: design
   - Why: The platform that satisfies the requirement is a design choice.

The residency rule is a requirement, the platform that satisfies it is a design choice, gating promotion on an eval result is a deploy decision, and instrumenting cost and latency is operate. Writing the eval suite and rubric is test; gating promotion on its result is deploy.
