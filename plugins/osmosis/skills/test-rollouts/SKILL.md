---
name: test-rollouts
description: Verifies Osmosis rollout startup, trajectories, and grading with a bounded local smoke or regression test. Model-quality comparisons belong to run-evals, even on a small sample.
---

# Smoke-test a rollout

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

Establish the behavior being checked: startup, trajectory capture, grading, or a specific workflow regression. A small row count alone does not make a model comparison a smoke test. Use the workspace's installed CLI, rollout environment, and `configs/eval/<name>.toml`. If a config is missing, prefer the workspace's eval default. When creating a config or choosing overrides, read [the full eval config](assets/eval.toml) for supported fields, defaults, ranges, and local/managed differences. Keep optional fields omitted unless needed. The configured dataset is a name, even when a local file overrides it.

For an authorized smoke run, select the data source below. `--rows` overrides the configured row limit without editing the shared config, but leaves `[evaluation].n` attempts unchanged; for a one-attempt check, set `n = 1` in a separate config under `configs/eval/` if the original must be preserved. In noninteractive JSON mode, use `--yes` only when the row, attempt, model, and backend scope is authorized.

| Input | First-row smoke check |
| --- | --- |
| Local file | `osmosis --json eval run configs/eval/<name>.toml --dataset-file data/<file>.jsonl --rows 0 --yes` |
| Platform dataset named in config | `osmosis --json eval run configs/eval/<name>.toml --rows 0 --yes` |

Row indices are zero-based. A local file without `--upload` needs no platform login, lookup, or upload. The selected model and backend still need their own credentials and may incur charges. Local execution uses the backend in the entrypoint; a remote Harbor sandbox (or model-calling Docker on Linux) may need `cloudflared` for the model bridge.

Read the run name from the result and inspect `.osmosis/evals/<run-name>/` (or the requested output directory): `logs.txt`, the sample index/summary, the relevant trajectory and `rollout_trials/<id>/diagnostics.json`, plus Harbor verifier logs when present. `--verbose` streams logs; it is not required for saving them. Verify that the selected rows ran, a trajectory was produced where expected, and graded rows got meaningful rewards. Separate a zero reward from an ungraded or intentionally skipped sample. For a regression check between rollout versions, change one component and keep the rows, model, grader, and attempts fixed.

Resume interrupted work with the same inputs and `--name <run-name>`. Changed inputs need a new run; never edit a saved manifest to force a resume. An intentional retry with `--retry-failed` also reruns skipped items, including locally removed samples. Report the row selection and result as a smoke check. Publish outputs only if requested.
