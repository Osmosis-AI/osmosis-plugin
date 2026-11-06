# Configure a benchmark run

Use the workspace's `configs/benchmark/default.toml` as a starting point when available. Outside a clone, `benchmark submit` accepts any readable TOML with an explicit workspace. Use the benchmark identifier and task IDs from the manifest. Select by `task_names`, `categories`, or both (their union); a named `task_set` overrides both.

Each `[[agents]]` entry has its own `[agents.model]`. Check whether the benchmark permits a custom harness, requires one, or runs its own scaffold. Use the reported default for comparison with published results.

| Model type | Required values |
| --- | --- |
| `provider` | Provider model identifier and `api_key_secret` record name |
| `endpoint` | Model identifier, `base_url`, and `api_key_secret`; `extra_headers` are literals, never credentials |
| `hosted` | Matching `base_model` and `lora_model_name` from `model list --type lora`; the LoRA must already be deployed, with no model key |

When `osmosis --json benchmark info <key>` reports `requires_model_context_window: true`, use provider or endpoint models and set each `[agents.model].max_input_tokens` to that model's supported input limit.

Use `[execution].attempts_per_task` for repeated trials. Set judge fields according to `requires_judge_api_key` and `requires_judge_model`. Some benchmarks require a judge credential but reject a model override; where a model override is supported, the benchmark can supply its default.

- Model, judge, verifier, and harness credential fields refer to secret **records**. Cursor CLI requires `harness_api_key_secret = "CURSOR_API_KEY"`. Mini SWE-agent uses the provider/endpoint model key as `MSWEA_API_KEY`; it does not accept a separate harness key.
- Use `[verifier].required` only for a benchmark supporting verifier secrets. Use `[secrets].required` for additional task credentials not already referenced; these can be supplied at submit and are not persisted.
- `[env]` and `[agents.env]` hold visible literals. Avoid collisions with injected keys, sandbox credentials, or the reserved `_OSMOSIS_` prefix. Never put credentials in headers, TOML values, or chat. Preparing a run does not authorize changing a secret record.

For the full field definitions and a worked config, see the [benchmark config reference](https://docs.osmosis.ai/cli/config-files#benchmark-config).
