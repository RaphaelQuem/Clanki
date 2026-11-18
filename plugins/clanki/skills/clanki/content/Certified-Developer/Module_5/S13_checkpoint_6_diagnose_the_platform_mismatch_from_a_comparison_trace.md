# Checkpoint 6: Diagnose the platform mismatch from a comparison trace

> **Screen:** S13 · **Section:** Comparing Platforms
>
> **Category:** Checkpoint Comparing platforms on latency, compliance, and cost · 3 min

Try it now. The comparison trace below shows a deployment platform selected on familiarity failing a customer requirement. Identify the mechanism, then pick the targeted fix from the three options.

**The trace**

```
platform_selected = "team_default"   # chosen on familiarity
latency_test: measured from dev laptop -> 180ms (looked fine)
customer_region: eu-west, payload 12 KB
compliance_check: data residency = EU-only required
result: REJECTED  reason="data processed outside EU on selected platform"
```

## Options

- Option 1: Optimize the parser to cut the 180ms latency measured on the laptop.
- Option 2: Remeasure latency from EU-west and select the platform whose region satisfies EU-only residency.
- Option 3: Add a caching layer to reduce per call cost on the selected platform.

## Answer key

- **Option 1 — Incorrect.** The rejection reason names data residency, not speed. Optimizing latency treats a dimension that was never the problem.
- **Option 2 — Correct.** The mechanism is a residency mismatch. The latency number hides it because it was measured from the wrong place. The fix is to measure latency from the customer's region and choose the platform that meets EU-only residency, which is the dimension that determines this placement.
- **Option 3 — Incorrect.** The rejection reason names data residency, not cost. Adding caching treats a dimension that was never the problem.
