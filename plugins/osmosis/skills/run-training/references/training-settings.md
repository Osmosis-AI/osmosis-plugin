# Training settings

Start with the generated config and override only fields needed for the experiment. The [training config reference](https://docs.osmosis.ai/cli/config-files#training-config) describes the supported fields and ranges. The installed CLI parses the config; the target platform determines managed limits. Verify model/backend-specific overrides with public docs or supplied platform evidence. If support is unknown, keep the documented range or report the unresolved capability. Successful parsing is not proof of server support, and submission is not a dry run.

- **Rollout load:** `rollout_batch_size × n_samples_per_prompt` is rollout demand per batch, not the number of active sandboxes. Consider backend capacity, per-trial latency, and model limits before raising either. Inspect timeouts and queueing before increasing deadlines.
- **Checkpoints:** use `checkpoint_save_freq` to control saves. Training does not schedule periodic evaluations; plan a separate evaluation for checkpoints that need comparison.
- **Environment:** `[env]` contains visible literal values. Put credential names in `[secrets].required` instead, keeping the namespaces disjoint and avoiding the reserved `_OSMOSIS_` prefix. Values supplied at submit (for example with `--secrets-file`) are not stored for future runs. Never place credential values in TOML, command arguments, or chat.
