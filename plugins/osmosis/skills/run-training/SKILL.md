---
name: run-training
description: Configures, submits, inspects, analyzes, or stops Osmosis training runs. Use for run settings, progress, reward trends, held-out regressions, or checkpoint comparisons.
---

# Prepare or manage training

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

**Existing run:** analyze supplied exports directly; CLI setup and platform access are needed only to retrieve missing evidence. Otherwise use `osmosis --json train info <run-name>` for progress, metrics, and available checkpoints; `train list` to locate it; `train logs <run-name>` for failures. For learning progress, held-out regressions, checkpoint selection, or what to change next, read [training analysis](references/analysis.md). Use `train stop <run-name> --yes` only for a requested stop of that run. From outside a connected repository, prefix platform-only commands with `osmosis --workspace <workspace-name>`. None of these reads require a rollout checkout or preflight.

**New run:** use the rollout's existing `configs/training/<name>.toml`. When creating it or choosing overrides, read [the full training config](assets/training.toml) for supported fields, defaults, and ranges; keep optional fields omitted unless needed. For workload and secret handling, see [training settings](references/training-settings.md).

1. Verify the configured rollout and its in-directory server entrypoint, supported base model (`osmosis --json model list --type base`), and uploaded dataset **name**. Use `dataset info` and `dataset preview` to check actual schema against the workflow or task verifier. Uploading or replacing data is a separate action.
2. Check evidence that this rollout produces trainable trajectories and meaningful rewards. Use a bounded local smoke to check workflow and grading mechanics; when hosted execution is unverified, a small managed smoke on the intended revision checks that path. Choose held-out or full evaluation coverage for the model-quality decision, not as a universal training prerequisite. Reuse adequate existing evidence, honor any stricter workspace rule, and run only within the user's authorization. Compare actual row coverage, ungraded samples, grader and model rather than relying on a status label. A held-out eval may intentionally use a different dataset. An uploaded local eval does not verify hosted Git sync or execution.
3. Verify source: the CLI reads config from disk but the platform fetches rollout code from the connected Git repository. Use one of `commit_sha` or `branch`, or the default branch if neither is set. Prefer the pushed SHA for reproducibility; surface unresolved dirty/ahead or source mismatches. Commit and push only when each is authorized.
4. State the target workspace, model, dataset, revision, and training settings that determine work. Preparation ends with a reviewable config. Only when the user authorizes this paid run, use `osmosis --json train submit configs/training/<name>.toml --yes`. It submits if preflight passes; it is not a validation command. From outside the clone use root `--workspace <name>` with the absolute canonical config path in the connected repository.

Return the run name, status, and platform link if available. If submission times out, check existing runs before trying again. A checkpoint becoming available does not authorize deployment.
