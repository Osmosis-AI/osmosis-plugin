---
name: plan-eval
description: Plans an Osmosis evaluation when the task, data, scoring, or comparison is undecided. Use for experiment design and budget; defined model comparisons belong to run-evals.
---

# Plan an evaluation

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

Plan from the request and supplied examples; CLI installation, login, and a connected workspace are needed only for operations that use them. Start with the decision the result should support. Specify the model or comparison, what counts as success on one row, and the aggregate metric and threshold. Use the same rows, grader, and attempt count for models being compared.

Inspect representative rows directly from a local file or with `osmosis --json dataset preview <name> --rows 5` on the platform. Judge whether they represent the intended task and expose the failures the metric should detect. With no data, agree on the task and what a representative test would look like before proposing sample rows.

Match the grader to the data. Prompt-mode rows supply `user_prompt` and an answer in `ground_truth` or its alias `label`; `system_prompt` is optional. A metadata-driven task verifier may work without a prompt or label. Record required field mappings or data-quality work in the plan; perform file preparation only when it is part of the request.

Deliver the measurement plan: dataset and split, model, grading rule, row/attempt budget, and what result would change the decision. Distinguish a smoke check from a formal measurement and say whether it needs managed infrastructure. A plan alone doesn't require creating a config, uploading data, or starting a run.
