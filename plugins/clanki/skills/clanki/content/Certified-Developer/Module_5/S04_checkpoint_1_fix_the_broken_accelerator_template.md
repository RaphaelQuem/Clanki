# Checkpoint 1: Fix the broken accelerator template

> **Screen:** S04 · **Section:** Packaging for Reuse
>
> **Category:** Checkpoint Packaging a reusable accelerator · 4 min

Try it now. Below is an agent template another team is supposed to reuse. It has one defect: a customer-specific value is hardcoded where a parameter belongs.

**The template as shipped**

```
# agent_template.py  : "reusable" code-review agent
def build_review_agent():
    return Agent(
        model="claude-opus-4-8",
        system_prompt=SYSTEM_PROMPT,
        tools=[read_file, run_linter],
        repo_path="/home/acme/checkout-service",   # customer repo
    )
```

(Confirm current model ID at platform.claude.com/docs/en/about-claude/models at build time.)

Identify the hardcoded value, then write the corrected function signature and the parameterized line that replaces it.

## Model answer

```
def build_review_agent(repo_path):
	return Agent(
	    model="claude-opus-4-8",
	    system_prompt=SYSTEM_PROMPT,
	    tools=[read_file, run_linter],
	    repo_path=repo_path,   # set per engagement
	)
```

The hardcoded repo\_path is the defect. A reusable template takes the customer-specific value as a parameter, so the next team configures the asset instead of editing the code. That is the difference between a template that runs and a template that reuses.

You identified the hardcoded repo\_path and moved it to a parameter.
