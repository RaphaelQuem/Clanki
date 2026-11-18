# Checkpoint 2: Choose the contribution channel and the readiness fix

> **Screen:** S07 · **Section:** Contributing Back
>
> **Category:** Checkpoint Contributing back to the ecosystem · 3 min

Try it now. Read the three cases below. Match each case to the channel built for it and match each case to the one readiness item the snippet is missing.

**Case A:** A focused tool that wraps a single API into a clean function. The snippet is the function and nothing else.

**Case B:** A full customer-service application a developer wants to share whole, including its UI and deployment scripts.

**Case C:** A one-line fix to an existing Cookbook example. The snippet is the corrected line, carried in from a customer engagement.

## Part 1: which channel?

Options: "The tool's own repository" · "The Cookbook, but only after the reusable pattern is stripped out as a focused example" · "The Cookbook example's own repository"

1. **Case A: A focused tool that wraps a single API into a clean function. The snippet is the function and nothing else.**
   - Answer: The tool's own repository
   - Why: A focused tool lives and is reviewed in its own repository.
2. **Case B: A full customer-service application shared whole, including its UI and deployment scripts.**
   - Answer: The Cookbook, but only after the reusable pattern is stripped out as a focused example
   - Why: A whole application does not fit a review built for one pattern; only the extracted pattern goes to the Cookbook.
3. **Case C: A one-line fix to an existing Cookbook example. The snippet is the corrected line, carried in from a customer engagement.**
   - Answer: The Cookbook example's own repository
   - Why: A fix to an existing example belongs in that example’s own repository.

## Part 2: what is missing before a maintainer can review it?

Options: "A test that proves the wrapper behaves" · "Reduction to a single focused pattern, because a whole application does not fit a review built for one pattern" · "The rights check, because engagement code can carry a licensing constraint that blocks the merge before any technical review"

1. **Case A: A focused tool that wraps a single API into a clean function. The snippet is the function and nothing else.**
   - Answer: A test that proves the wrapper behaves
   - Why: A focused tool still needs a test that proves the wrapper behaves.
2. **Case B: A full customer-service application shared whole, including its UI and deployment scripts.**
   - Answer: Reduction to a single focused pattern, because a whole application does not fit a review built for one pattern
   - Why: The application must be reduced to the pattern before it fits the review.
3. **Case C: A one-line fix to an existing Cookbook example. The snippet is the corrected line, carried in from a customer engagement.**
   - Answer: The rights check, because engagement code can carry a licensing constraint that blocks the merge before any technical review
   - Why: Engagement code needs the licensing gate cleared before any technical review.

## Summary

Case A goes to the tool’s own repository and is missing a test that proves the wrapper behaves. Case B does not belong as a whole application, so the reusable pattern inside it goes to the Cookbook as a focused example. Case C goes to the Cookbook example’s repository and is missing the rights check, because engagement code can carry a licensing constraint that blocks the merge before any technical review.
