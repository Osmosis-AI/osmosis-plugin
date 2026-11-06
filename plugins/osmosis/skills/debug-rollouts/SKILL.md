---
name: debug-rollouts
description: Diagnoses or fixes Osmosis rollout startup, trajectory, grading, and timeout failures. Use for broken rollouts or unexpected rewards; model-quality comparisons belong to run-evals.
---

# Diagnose a rollout

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

Start with the reported error and one affected row or run. Briefly inspect existing evidence to locate the failed phase. For reproducible rollout-code or runtime problems, prefer a bounded `osmosis eval run` on that input as the main diagnostic loop; it exercises the actual entrypoint and gathers server, trajectory, and grading evidence together. Follow [local reproduction and evidence](references/local-eval.md) when reproducing or reading local results. Continue directly when the requested debug scope already authorizes the model, backend, rows, and attempts; honor logs-only requests and missing execution prerequisites.

Use `osmosis --json eval info <name>`, `eval logs <name>`, or `train logs <name>` for managed evidence, with an explicit workspace outside the clone. Hosted-only Git sync, secret injection, or scheduling failures need that environment's evidence; a successful local eval cannot validate those paths. When existing evidence already establishes a cause, avoid an unchanged rerun and verify the fix when authorized.

| Symptom | Evidence that distinguishes causes |
| --- | --- |
| Layout, import or startup failure | Inspect the config's entrypoint under `rollouts/<name>/`, the rollout's own dependencies and server port. If an SDK import or call fails, check the installed module or signature in the rollout environment. Use `osmosis --json doctor` for workspace problems. For managed runs compare the requested Git revision and the synced revision; a local edit cannot repair a running remote server. |
| Missing data or label | Inspect the failing local row directly, or use `dataset preview` for platform data. Check whether the workflow consumed `ground_truth` (alias `label`) or task metadata. A metadata-only row need not have a prompt or label; an upload validator's row-count error does not invalidate a one-row local smoke test. |
| No trajectory or reward | For a Python workflow, inspect the returned history or registered sample source, policy calls through the active rollout context, and the async grader's `ctx.set_reward(...)`. For native Harbor, inspect the selected task, native agent, and verifier output. Its `tests/test.sh` takes precedence over a Python grader. An oracle produces no model trajectory. |
| Graded zero vs. missing grade | Trace one input through the policy output and grader/verifier. Distinguish a legitimate task failure from malformed output, an intentional skip, timeout, or unavailable model/sandbox. For hangs, check blocking calls in async code and sandbox/model-bridge connectivity before raising timeouts. |
| Local publish or managed preflight failure | Check terminal status, workspace identity, named secrets (never values), and the exact error. Local execution, result upload, and fetching managed source from Git are different operations. |

For a diagnosis request, return the failing phase, affected input, command/run name and evidence paths, likely cause, and the smallest check that could disprove it. If a fix is requested, verify it on the same affected input and report the before/after evidence; a code edit alone is not a verified fix. `train submit` and `eval submit` can start paid runs; neither is a diagnostic dry run.
