---
name: create-rollouts
description: Creates or adapts an Osmosis rollout from an agent or task, including its grader or Harbor verifier and authorized local validation. Use for new implementation or integration work.
---

# Create a rollout

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

## Implement the task

Identify the task, representative input, expected output, and grading rule from the user's request and existing files. Reuse an existing agent or task implementation where it fits. Resolve only missing inputs that affect correctness or execution scope. When wiring a workflow, grader, or backend, check the Python signatures in the rollout's own SDK environment. Work in `rollouts/<name>/`; eval and training configs name the server entrypoint relative to that directory. Preserve an existing entrypoint unless the task changes it.

Scaffold a new rollout with the CLI: `osmosis --json template list` and `osmosis --json template apply <template>` for a matching integration, or `osmosis --json rollout init <name>` for a blank Python workflow. Use the generated files, including configs. Neither repair nor scaffolding should overwrite user work without authorization.

Choose the execution and reward paths independently:

- **Python workflow:** route policy calls through the active rollout context. Return one message history (optionally with finite numeric metrics), or register exactly one SDK sample source and return `None`. An async Python grader reads `ctx.sample` and calls `ctx.set_reward(...)`; use `ctx.label` for prompt-mode ground truth or `ctx.metadata` for metadata-mode inputs. See [Python integrations](references/python-integrations.md) when wiring an agent or a sample source.
- **Harbor:** the agent can be a registered native harness or a Python workflow. A task's `tests/test.sh` owns grading when present; it does not need a Python grader. Read [Harbor task and server wiring](references/harbor.md) when creating or changing a Harbor rollout.

Keep the generated server startup: bind to `0.0.0.0` on `_OSMOSIS_ROLLOUT_PORT` when supplied. Complete the requested behavior, dependency declarations, grader/verifier, and matching eval config; scaffolding alone does not finish the implementation. Replace required placeholders in the config used for validation and identify any remaining training-only inputs separately.

## Verify with a local eval

Carry an authorized create-and-test request through validation in the same task; the user need not invoke another skill. Use `eval run` to verify dependency sync, entrypoint import, server startup, and task execution together when the model, backend, credentials, and execution scope are available. Do not require a separate manual import or environment setup before the first eval. For an explicit code-only request, perform feasible static or import checks without running an eval. If execution is blocked, finish the implementation and report the specific missing input and prepared command instead of claiming runtime success. Local model calls and cloud sandboxes may incur charges; reuse authorization already given.

Start with a representative row and one attempt unless the request needs more. Set `[evaluation].n = 1` in the new config, or a separate diagnostic copy under `configs/eval/` when preserving an existing config. `--rows` controls rows, not attempts. For an authorized run with a local file:

```sh
osmosis --json eval run configs/eval/<name>.toml --dataset-file data/<file>.jsonl --rows 0 --yes
```

Use the intended backend and model; omit `--dataset-file` only when using the named platform dataset. Local-file execution without `--upload` needs no platform login or upload. Let the CLI manage dependency sync, server startup, and model routing. Check the returned run directory's `logs.txt`, sample index/summary, and trajectory plus grader/verifier evidence; an exit code or nonzero reward alone does not prove the task was implemented correctly. Expect policy calls and a captured trajectory for a model workflow, and a reward consistent with the observed output. An oracle can verify a Harbor task but cannot establish a trainable model trajectory.

If verification fails and repair is within scope, diagnose the first failed phase, fix the cause, and rerun the same sample within the authorized budget. After changing code, data, or grading settings, use a new run name so the previous evidence remains available. Stop retries when the same failure repeats without new evidence.

Report the files and task contract, whether the entrypoint loads, the local run name and selected rows/attempts, trajectory and grading evidence, and remaining blockers. Distinguish generated, loadable, and runtime-verified states. Local success does not establish hosted Git sync or execution; publishing, managed submission, and deployment remain separate actions.
