---
name: plan-training
description: Designs an Osmosis training experiment around a target behavior, reward, data split, and baseline. Run configuration and existing results belong to run-training.
---

# Plan a training experiment

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

Plan from the request and supplied examples; CLI installation, login, and a connected workspace are needed only for operations that use them. Identify one behavior the policy should learn. Use examples from the user's data to describe a good trajectory, a plausible failure, and how a grader or task verifier can tell them apart. If the signal rewards a shortcut, revise it before proposing training.

- **Data:** identify the training distribution and a separate evaluation set or split, including the grouping or time boundary needed to avoid leakage. Inspect representative rows locally, or preview an existing platform dataset. Specify whether examples supply prompt/answer pairs or task metadata and record preparation work still needed. Creating or changing dataset files is a separate requested deliverable.
- **Baseline:** choose a model and a small rollout evaluation that can establish whether the reward and trajectory are usable. A correct verifier alone does not prove the policy can learn: an oracle that makes no model calls yields no training trajectory.
- **Decision:** define the held-out metric, target improvement, and stopping condition. Reuse a suitable rollout and prior measurement where possible. If there's no baseline, propose measuring it as the next step.

Return a concrete experiment and the missing inputs that affect it. Save a note when requested or expected by the workspace. Planning does not require publishing a dataset, scaffolding code, or starting a paid run.
