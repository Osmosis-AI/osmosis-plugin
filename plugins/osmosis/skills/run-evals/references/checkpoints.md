# Evaluate a trained checkpoint

Resolve the target in its workspace with `osmosis --json model list --type lora` and `osmosis --json model info <checkpoint-name-or-id>`. Record its `id`, `model_name`, training run, checkpoint step, `deployment_status`, and exact `inference_model`. Outside a connected clone, add root `--workspace <workspace-name>`. The callable identifier is `inference_model`, typically `<base_model_path>:<checkpoint-name>`; a display name or checkpoint UUID is not a CLI eval `model_path`.

Current Osmosis-hosted LoRA evaluation requires an `active` inference deployment for both managed and local execution: managed submission rejects an inactive checkpoint, and the inference endpoint serves only deployed adapters, so local calls fail with `adapter not registered`. An undeployed checkpoint can still have an `inference_model`; that field alone does not establish readiness. If deployment is already authorized, use `osmosis --json model deploy <model_name>` and recheck `model info` until active before dispatch. Otherwise prepare the config and identify deployment as the remaining prerequisite; obtain any missing deployment authorization only when execution is requested. Reuse existing authorization, and never evict another deployment to make room without its own authorization. If inference details are absent, resolve account availability or the missing base model before claiming the config is executable.

## Managed evaluation

Set `[experiment].model_path` to the exact `inference_model` returned by `model info`, for example `Qwen/Qwen3-4B:math-step-100`. The CLI accepts this canonical base-path/checkpoint-name form; it does not accept a bare checkpoint name or UUID in this field. The platform resolves the checkpoint in the workspace, checks its base model and active deployment, and supplies the inference endpoint and run credential. Keep any separate secrets required by the rollout or grader; the hosted checkpoint itself does not require a user-supplied `OPENAI_API_KEY`.

Prepare the uploaded held-out dataset name, explicit row limit, attempts, grader settings, and rollout Git revision in the normal eval config. Submit through the managed command in `SKILL.md` only within the authorized execution scope. Do not add a local `openai/` prefix or endpoint override to this managed checkpoint configuration.

## Local evaluation

The local runner treats `model_path` as a LiteLLM model ID and does not resolve platform checkpoints. Set it to `openai/` followed by the exact `inference_model`, for example `openai/Qwen/Qwen3-4B:math-step-100`. Keep the rollout's model client on its provided context endpoint so calls pass through the local eval bridge and retain trajectories and usage.

Use the OpenAI-compatible base URL `https://inference.osmosis.ai/v1`. Once the checkpoint is active, plain `osmosis model info <model_name>` output shows this endpoint (without `--json`); remove `/chat/completions` and keep `/v1`. Its host comes from the CLI's `OSMOSIS_INFERENCE_URL`, so use that override's host only when the target environment requires one. Set `OPENAI_BASE_URL` for the command, which takes precedence over any ambient `OPENAI_API_BASE`, and bind `OPENAI_API_KEY` to the Osmosis inference API key from the trusted environment or secret manager. Platform CLI login is not an inference API key. Keep credential values out of TOML, output, and chat.

For an authorized run, after filling the config with the local model ID and setting `checkpoint_api_base` to that base URL:

```bash
OPENAI_BASE_URL="$checkpoint_api_base" \
OPENAI_API_KEY="${OSMOSIS_API_KEY:?Set the Osmosis inference key in the trusted environment}" \
osmosis --json eval run configs/eval/checkpoint-heldout-local.toml --dataset-file data/heldout.jsonl --yes
```

Local-file execution does not need a platform dataset lookup or result upload. It still calls the deployed model and incurs inference charges; preparing this command does not execute it. Add `--upload` only when publishing is requested.

## Make the comparison meaningful

Use a held-out split whose provenance excludes training rows, and freeze its content and selected row identities. Match rollout revision, grader/verifier, thresholds, attempts, and generation settings across the checkpoint and baseline. For local all-row coverage omit both `--rows` and `[evaluation].limit`; for managed all-row coverage set the uploaded dataset's actual row count as the limit. A dataset name or equal limit alone does not guarantee paired rows. Prepare separate named configs and fresh result names for each model; report preparation, deployment readiness, and completed measurement separately. After execution, use [evaluation analysis](analysis.md) to compare matched outcomes and account for ungraded or missing samples.
