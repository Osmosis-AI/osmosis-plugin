# Reproduce and inspect a local failure

Use the workspace's installed CLI and rollout environment. Select the failing source row rather than an arbitrary first row; preserve the relevant model, backend, data, grader, and timeouts. Local execution follows the backend in the entrypoint, which may use a paid cloud sandbox. Check available credentials without displaying values; platform secret records alone do not supply local workflow secrets.

For a single-row diagnostic, inspect `[evaluation].n` first. Unless multiple attempts are needed to reproduce the issue, use one attempt in a separate config under `configs/eval/`, preserving the user's original config. `--rows` overrides row selection but does not reduce attempts. For authorized local-file execution:

```sh
osmosis --json eval run configs/eval/<diagnostic>.toml --dataset-file data/<file>.jsonl --rows <source-index> --name <new-run-name> --yes
```

Row indices are zero-based. Omit `--dataset-file` when the reproduction uses the configured platform dataset. A local file without `--upload` requires no platform login, lookup, or publication. Let `eval run` manage dependencies, server startup/health, and model routing instead of starting a second manual server. Add `--verbose` for live log output when useful; detailed logs are saved without it. Read the resulting files even if the final command output is brief or reports failure.

## Evidence to inspect

Use the returned run directory (default `.osmosis/evals/<run-name>/`); failed startup may leave only part of this evidence. Inspect the files present in that directory.

| Evidence | What to establish |
| --- | --- |
| `logs.txt` | First failed phase: provider preflight, dependency sync, entrypoint startup, health, workflow, grader, or model/sandbox connectivity. |
| `manifest.json`, `index.jsonl`/`summary.jsonl`, `metrics.json` | Resolved inputs, sample status, graded versus missing reward, and coverage. Map selected-row indices back to source rows using the selection; selected row 0 can represent source row 3. |
| Trajectory and `rollout_trials/<id>/diagnostics.json` | Actual policy/tool calls and outputs, error type, and failing phase. Missing trajectory before dispatch is not a model-quality failure. |
| `rollout_trials/<id>/logs/` and artifacts | Harbor trial, agent, and verifier detail when present; inspect verifier output and reward files separately from the model's response. |

Report the relevant excerpts and paths, keeping credentials and unrelated private inputs out of diagnostic messages or documentation searches. A graded zero can be a valid task failure; an absent reward, intentional skip, or timeout has a different meaning.

## Verify a repair

When a fix is requested, change the cause supported by evidence and rerun the same input within the authorized budget. Use a new run name after changing code, data, grading, or locked settings; preserve previous results and manifests. Resume with the same name only for unchanged inputs. `--retry-failed` retries failed and skipped work under unchanged inputs, not a patched implementation. Stop repeated attempts with no new evidence and report the remaining blocker. Raising timeouts or switching backend/model without explaining the changed hypothesis can hide the original fault.

Compare the before/after failed phase, captured trajectory, and grader/verifier outcome. State which local behavior is now verified and which hosted-only behavior remains untested. Reproduction does not publish results or start managed evaluation/training.
