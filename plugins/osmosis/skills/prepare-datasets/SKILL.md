---
name: prepare-datasets
description: Prepares, validates, splits, uploads, or downloads datasets for Osmosis. Use for concrete data files or platform datasets; experiment design belongs to the planning skills.
---

# Prepare a dataset

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

Identify the source, consumer (workflow, grader, or task verifier), and requested output: inspection, local preparation, or platform publication. Use existing files and an explicit mapping where available; resolve ambiguous answer fields or task metadata before transforming them. Local preparation and validation need no platform login or upload.

## Local preparation

1. Inspect representative rows and the full file's schema and counts. Read [the dataset contract](references/schema.md) when mapping fields or validating a file. Choose prompt/answer or metadata mode according to the consumer. For Harbor, check whether each row selects an existing task by metadata or supplies a prompt to a shared template. Report missing inputs and conflicting labels instead of inventing replacements.
2. Write requested transformations to a new path, preserving the source, meaningful types, text, and identifiers. Keep CSV identifiers and answers such as `001` as strings unless the consumer requires another type. Report duplicates and missing values; remove, impute, or relabel them only under an agreed rule. For a requested split, preserve existing assignments or use a reproducible seed and task-appropriate grouping/time boundary. Check overlapping examples and groups across splits, and keep held-out data out of training. A local split file or column does not establish that the platform uses that split: verify the consumer's selection.
3. Validate upload-ready files with `osmosis --json dataset validate <path>`, then inspect warnings as well as success. A one-row local smoke file can be usable despite the upload minimum; preserve it and report local suitability separately. Reconcile input/output counts and read the written output back to check the mapping. Schema validity alone does not establish label quality or absence of leakage.

Return the output path and schema, field mapping, row counts, duplicate/missing-value and split findings, and validation result with any unresolved issue. Inspection alone leaves files unchanged; preparation ends without publishing or launching a run.

## Platform operations

Use the connected repository's workspace, or prefix platform commands with `osmosis --workspace <workspace-name> --json` when the target is explicit. Read existing data with `dataset list`, `dataset info <name>`, and `dataset preview <name> --rows 5`. Download with `dataset download <name> --output <new-path>` when local contents are needed; preserve existing files unless replacement is requested.

For an authorized upload of the reviewed file to that workspace, use `osmosis --json dataset upload <path> --yes`. The dataset name comes from the filename stem; preserve an explicitly requested dataset name. If that name already exists, report the conflict instead of silently choosing another name. A same-name conflict does not authorize `--overwrite`; replace only when that dataset replacement is requested. An accepted upload can still be processing: check `dataset info <name>` and use `dataset logs <name>` on failure. After an ambiguous upload response, inspect the existing dataset before retrying. Report its name, processing status, and URL when returned; claim readiness only when the platform reports it.
