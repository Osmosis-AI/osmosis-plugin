---
name: run-evals
description: Prepares, runs, or analyzes Osmosis model-quality evaluations, including small local comparisons and trained checkpoints. Use also for eval results, retries, publishing, or stopping runs.
---

# Run or inspect an evaluation

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

For an **existing result**, analyze supplied exports directly; CLI setup and platform access are needed only to retrieve missing evidence. Otherwise inspect local metrics in its run directory or use `osmosis --json eval info <run-name>` on the platform. Use `eval list` to find a name or `eval logs <run-name>` for startup or infrastructure errors. No submission prerequisites apply. Outside a connected clone, prefix platform-only commands with `osmosis --workspace <workspace-name>`. For score interpretation, model comparisons, or failure analysis, read [evaluation analysis](references/analysis.md). For publishing, retrying, or stopping, read [result operations](references/results.md).

For a **new measurement**, establish the model, dataset, row coverage, attempts, and grading criterion. This includes small local model comparisons; the goal is measuring quality rather than checking rollout mechanics. Read the existing config in `configs/eval/`. When creating it or choosing overrides, use [the full eval config](assets/eval.toml) for supported fields, defaults, and ranges. Prompt-mode rows use `user_prompt` and either `ground_truth` or `label`. Uploaded datasets with a `metadata` column need a non-empty object on every row and may use a task-native verifier. Check which fields the grader consumes. Preparing a measurement ends with a reviewable config and coverage/budget; submission is a separate action.

For a **trained checkpoint**, read [checkpoint evaluation](references/checkpoints.md) before preparing a new measurement: it resolves the checkpoint, distinguishes managed and local model routing, and handles deployment prerequisites within the requested scope. Use held-out rows with recorded identities for a comparison against the base model or another checkpoint.

| Requested execution | Command and coverage |
| --- | --- |
| Local, local file | From the local rollout workspace, use `osmosis --json eval run configs/eval/<name>.toml --dataset-file <path>`. A one-row file is valid. Without `--upload`, no platform login or dataset lookup. For every row, omit both `--rows` and `[evaluation].limit`. |
| Local, platform dataset | `osmosis --json eval run configs/eval/<name>.toml`. Verify the config's dataset **name** in the connected workspace. Same local coverage defaults; rollout code comes from disk. |
| Managed | `osmosis --json eval submit configs/eval/<name>.toml --yes`. Omitting `[evaluation].limit` selects the platform's default sample. For all rows, get the uploaded dataset's row count with `dataset info` and set the limit to that count before submission. |

The local commands above require confirmation before dispatch: in JSON mode append `--yes` only for an authorized model, backend, row count and number of attempts. Local execution can still incur provider or sandbox charges.

For managed execution, resolve the named platform dataset and verify the model, requested attempts, secret names, and cost-bearing scope. The config is read from disk but the rollout comes from Git: verify the intended pushed revision is available. `commit_sha` pins a commit; `branch` is mutable; omit both for the default branch. Commit and push each require their own authorization. From outside the clone, use root `--workspace <name>` with an absolute config path in the connected repository's `configs/eval/`. Submit only with authorization for this scope; after an ambiguous response, look for the run before considering another submission.

Report the run name, local or managed origin, source revision if known, model, dataset, actual rows covered versus requested, attempts, score/pass rate, and any ungraded samples. Call a run full-dataset only when the observed coverage supports it.
