---
name: run-benchmarks
description: Prepares, runs, or analyzes Osmosis catalog benchmarks. Use for catalog tasks, harness comparisons, trial budgets, results, or stopping a benchmark run.
---

# Run a benchmark

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

A benchmark runs tasks from its catalog manifest independently of the workspace's rollout code. Run commands in the connected repository or add root `--workspace <workspace-name>` when the target workspace is known.

## An existing benchmark or run

| Request | Operation |
| --- | --- |
| Find a benchmark or its leaderboard | `osmosis --json benchmark list` or `osmosis --json benchmark info <key>` |
| Find a run, read progress, or diagnose failure | `osmosis --json benchmark runs list`, `benchmark runs info <run-name>`, or `benchmark runs logs <run-name>`; use a returned cursor for older logs |
| Download results | `osmosis --json benchmark runs download <run-name> --yes` downloads summary and results to `.osmosis/benchmarks/<run-name>/` by default. `--type artifacts,logs` selects only those two types; use `--type all` for everything. |
| Stop a run | `osmosis --json benchmark runs stop <run-name> --yes` only for an authorized stop of that run |

Use a fresh `--output <directory>` to preserve an earlier snapshot. Without `--overwrite`, the downloader skips files of matching size and replaces files of different sizes. A running benchmark's files may change between downloads.

For a comparison, match catalog/task versions, selected task IDs, harness, attempts, judge, and scoring rules except for the component under test. Report graded successes and failures separately from timeouts, missing verifier outputs, and skipped trials, with explicit denominators and incomplete coverage. Inspect task-level results before explaining an aggregate change; mark a subset or changed harness as a different measurement from the published leaderboard. An analysis request does not authorize another run.

## A new run

1. Inspect `benchmark info <key>` for the manifest, task sets, judge requirements, and whether a harness is supported, required, or selected by default. Check `sync_status` in `benchmark list`; only a ready benchmark can run.
2. Use the user's benchmark, agent/model choices, task selection, and attempts. If scope is open, propose a small comparison first. Resolve selectors to distinct task IDs from the manifest, including for categories. Report **selected tasks × agents × attempts per task = trials**; judge calls may cost extra. State when a subset or non-default harness changes leaderboard comparability.
3. For TOML or credential wiring read [benchmark configuration](references/config.md). Omit `[tasks]` only for an intentional full run. For HLE, recommend the published parity set when comparability is the goal.
4. Submit with `osmosis --json benchmark submit <config.toml> --yes` only when the user has authorized that workspace, resolved trial scope, and agent choices. Report the generated run name and URL. If a submit response is lost, check `benchmark runs list` before submitting again.
