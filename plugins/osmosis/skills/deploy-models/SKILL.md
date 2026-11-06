---
name: deploy-models
description: Selects and serves Osmosis LoRA checkpoints. Use for deployment, undeployment, deployment capacity, or inference endpoint usage.
---

# Serve a LoRA checkpoint

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

Use `osmosis --json model list --type lora` and `osmosis --json model info <name>` to find a checkpoint, its deployment status, and its inference model ID. Outside a connected clone add root `--workspace <workspace-name>`. For a recommendation, use evidence for the user's task: checkpoint step, held-out evaluation if available, and training reward. Listing or recommending a model ends without changing deployments.

- **Deploy or undeploy:** for the explicitly requested workspace, checkpoint and operation, run `osmosis --json model deploy <name>` or `osmosis --json model undeploy <name>`. Only LoRA models are deployable. Check the returned status; do not claim readiness from an accepted request alone. At the deployment limit, report `active_deployments` and `max_active_deployments`; never evict another model without authorization.
- **Call the inference API:** when inference is requested, use the OpenAI-compatible request shown by `osmosis model info <name>` with its exact `inference_model` and a bearer credential from `OSMOSIS_API_KEY`. Load the key through the user's trusted environment; never print it or request it in chat. For a usage example, show the variable reference without issuing a live call.
