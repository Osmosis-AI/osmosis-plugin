# Dataset shape and validation

Check upload constraints with `osmosis dataset validate <path>`. It accepts CSV, JSONL, and Parquet; the current contract requires at least four rows for upload. Local rollout smoke tests may use fewer rows. For the public reference see [datasets](https://docs.osmosis.ai/platform/datasets).

| Mode | Row contract |
| --- | --- |
| Prompt/answer | No `metadata` column; supply `user_prompt` and `ground_truth` or its alias `label`. `system_prompt` is optional. |
| Metadata | The presence of a `metadata` column selects this mode. Every row needs a non-empty JSON object there; prompt and answer columns are optional. Verify the actual keys consumed by the task or grader. |

For a Harbor rollout, check its `task_mode` before mapping fields:

- `dataset`: To select a task under `tasks_dir`, use a JSONL row such as `{"metadata":{"harbor_task_id":"fix-parser"}}`. Verify that the ID matches a task directory. If the task provides its own `instruction.md` and verifier and needs no other row inputs, this is the entire row: neither `user_prompt` nor `ground_truth` is required.
- `template`: If instructions vary by row, keep `user_prompt`; a non-empty prompt replaces the shared `instruction.md` for that row. Without a prompt, the template must provide the instruction file.

Column names are case-sensitive. JSONL rows must have the same complete set of top-level fields; additional columns are allowed. A blank `metadata` export column still selects metadata mode: resolve whether it should be mapped, populated from real source data, or removed rather than fabricating `{}`. Preserve conflicting `label` and `ground_truth` values for review instead of silently choosing one.

Metadata may be an object or an encoded JSON-object string; in CSV it must be correctly quoted JSON. The validator rejects empty/nested empty objects, inconsistent value types at the same metadata key across rows, and integers outside the signed 64-bit range. Inspect errors against the original row before normalizing types.

Use CSV/JSON readers and writers that preserve quoting, embedded newlines, Unicode, and numeric-looking strings. Read Parquet with an available compatible reader; without the optional Parquet dependency, the CLI may return success with a warning that content validation was skipped. Report that limitation and validate with a suitable reader before claiming content readiness.

Messages-based input for `osmosis eval rubric` is a different contract. Preserve the requested consumer instead of forcing that input into the rollout dataset shape.
