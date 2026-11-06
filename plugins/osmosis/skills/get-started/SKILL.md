---
name: get-started
description: Guides Osmosis onboarding, the bundled sample evaluation, and next steps from the user's goal and existing project. Use for first-time setup, a guided workflow, or deciding what to do next; specific run or debugging requests use their task skill.
---

# Get started with Osmosis

Help the user reach a useful result without needing to learn the skill catalog. Identify their starting point, recommend a short route, and carry out the next available step within the requested scope. Reuse progress from this conversation and existing files.

When the user asks to try the bundled demo, open the [sample evaluation](references/sample-evaluation.md) and walk through its synthetic results. It works from this skill alone in an empty project. Use the user's own results when supplied; only use the sample for a requested demonstration.

Use `osmosis --help` to find commands and the installed subcommand's `--help` to check its flags. Resolve uncertainty with `osmosis-docs` or the [docs index](https://docs.osmosis.ai/llms.txt), using public terms only. Prefer current docs to bundled references; verify managed limits separately. Report mismatches and check [PyPI](https://pypi.org/project/osmosis-ai/) before recommending an upgrade. Upgrade or send docs feedback only when requested.

## Find the starting point

Infer the goal from the request: evaluate a model or agent, improve it through training, run a catalog benchmark, or explore. If the goal is unclear, ask what task or decision the user wants to improve, with a concrete example if helpful. Explain a rollout as the agent's work on one input plus how its result is scored when that term first matters.

Inspect the relevant project instructions and existing data, agent code, `rollouts/`, `configs/`, and result files when available. Use what is already supplied before asking for more. The plugin's installation directory contains guidance; experiments belong in the user's project or connected workspace repository. Installing this plugin from GitHub does not connect that workspace to Osmosis.

For exploration, planning, or analysis of supplied files, start directly from the task and examples. CLI installation, login, billing, and a workspace are needed only for operations that use them. If the next step requires execution or platform access, read [setup and workspace access](references/setup.md) and resolve only the missing prerequisites for that step.

## Choose and follow the route

Give a brief route in terms of the user's outcome, then work on the current step. Use the relevant installed skill for its detailed procedure; the user need not invoke it or memorize its name. These are routing hints, not installation dependencies. If a skill is unavailable, use the linked public documentation and installed CLI help to perform the same task; this skill must remain useful when installed alone.

| Starting point | Next work | Evidence to carry forward |
| --- | --- | --- |
| An idea with no measurement plan | `plan-eval`: define the task, a representative example, grading rule, comparison, and row/attempt budget. For a training goal, use `plan-training` to define the target behavior, reward, and held-out decision. | A concrete plan and the next missing input; no setup required for planning. |
| A plan and source data | `prepare-datasets`: map fields, validate examples, and prepare any requested train/held-out split. With no data, agree on representative cases; label illustrative examples as synthetic. | Output files, field mapping, counts, and data-quality findings. Synthetic smoke data is not a quality benchmark. |
| Data and an agent or task to evaluate | `create-rollouts`: reuse the agent or a rollout template, implement its grading path, and complete requested validation. For an existing rollout, use `test-rollouts` if mechanics are unverified. | Matching config and evidence that the selected sample produces a trajectory and the intended grade. Use `debug-rollouts` for a failure. |
| A working rollout | `run-evals`: establish the baseline or compare models on matched rows, grading, and attempts. Choose local or managed execution for the user's needs. | Actual row coverage, quality results, ungraded failures, and what the evidence supports. |
| A training goal with baseline evidence | `plan-training` if the experiment is still undecided, then `run-training` for configuration and authorized execution. Reuse adequate readiness evidence. | A bounded training configuration, actual run status, and candidate checkpoints; compare checkpoints on held-out data with `run-evals`. |
| A catalog benchmark goal | `run-benchmarks`: choose catalog tasks, harnesses, models, and a trial budget directly. | A reviewable benchmark configuration or, when execution is requested, results with actual coverage. Custom data and a custom rollout are not prerequisites. |
| Existing results or a checkpoint | Analyze eval, training, or benchmark evidence with its task skill. Use `deploy-models` when serving is the user's goal, including any deployment prerequisite for checkpoint inference. | A supported conclusion or checkpoint choice, followed by the requested serving action and verified status. |

For an undecided newcomer, a small evaluation baseline is a useful first destination. A first workflow check can use one representative row and one attempt; a meaningful quality decision needs suitable evaluation coverage. Honor an explicit benchmark, training, serving, or exploration goal instead of forcing everyone through every stage. Inspect available templates and project examples before choosing one; do not assume a named starter is present.

## Keep the user moving

Continue through authorized preparation and execution in the same task. When a browser login, workspace selection, or missing model/budget requires user input, finish independent preparation and give one specific action to unblock it. Reuse authorization already given; a general onboarding request does not authorize uploads, Git publishing, paid runs, billing changes, or deployment.

At each meaningful milestone, report the artifact or result, what was actually verified, and one recommended next action tied to the goal. For example, a captured trajectory and correct grade justify preparing the baseline comparison; a baseline with enough task failures may justify a training experiment. Setup completion alone proves neither runtime behavior nor model quality. If the user stops after planning or preparation, report that completed scope and the next action without implying a run happened.
