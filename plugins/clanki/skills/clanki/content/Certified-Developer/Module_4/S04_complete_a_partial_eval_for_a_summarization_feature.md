# Complete a partial eval for a summarization feature

> **Screen:** S04 · **Section:** Evals & Judges
>
> **Category:** Checkpoint Evals & Judges · 9 min

This eval has two gaps. For the dataset, identify the specific output each input case should produce. For the judge prompt, match each score band to what it means.

**dataset.json**

```
[
  {
    "input": "Long support thread about a delayed refund, 14 messages.",
    "expected_behavior": "A 2-sentence summary naming the issue (delayed
                          refund) and the current status (escalated)."
  },
  {
    "input": "Meeting transcript where three action items are assigned.",
    "expected_behavior": ""
  },
  {
    "input": "Bug report with repro steps and one unrelated aside.",
    "expected_behavior": ""
  }
]
```

**judge\_prompt.txt**

```
You are grading a summary against its expected behavior.
Summary:           {output}
Expected behavior: {expected_behavior}

Return JSON with "strengths", "weaknesses", "reasoning", and "score".

Score scale: 1 to 3, 4 to 7, 8 to 10 (see below to complete the definitions).
```

## Answer bank

- A summary that lists all three action items with their owners
- A summary of the bug and its repro steps that omits the unrelated aside
- Misses required content
- Partial: some required content present, some missing
- Complete and faithful to the expected behavior

## Answer key

1. **Expected output for the meeting-transcript case**
   - Answer: A summary that lists all three action items with their owners
   - Why: The dataset case is a meeting transcript where three action items are assigned; the expected_behavior must name that specific output, not restate the input.
2. **Expected output for the bug-report case**
   - Answer: A summary of the bug and its repro steps that omits the unrelated aside
   - Why: The dataset case is a bug report with repro steps and one unrelated aside; the expected output names the bug and repro steps and excludes the aside.
3. **Judge score band 1 to 3**
   - Answer: Misses required content
   - Why: The low band anchors to missing the required content entirely.
4. **Judge score band 4 to 7**
   - Answer: Partial: some required content present, some missing
   - Why: The middle band anchors to partial coverage.
5. **Judge score band 8 to 10**
   - Answer: Complete and faithful to the expected behavior
   - Why: The top band anchors to complete, faithful coverage of the expected behavior.

You named a concrete output for each case, for example "a summary listing all three action items with their owners" and "a summary of the bug and its repro steps that omits the unrelated aside," and you gave the judge an anchored scale, such as 1 to 3 misses required content, 4 to 7 partial, 8 to 10 complete and faithful. The expected behavior and the anchored scale are what make the score comparable across runs and defensible in review.
