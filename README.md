# Osmosis agent plugin

Go from an idea or an existing agent to an evaluation, training experiment, or deployed model with your coding agent. Osmosis skills guide the workflow, prepare the files, interpret results, and help you decide what to do next.

[Website](https://osmosis.ai) · [Open your workspace](https://platform.osmosis.ai) · [Documentation](https://docs.osmosis.ai/) · [Contact Osmosis](mailto:contact@osmosis.ai)

## Install with your agent

Paste this into your coding agent:

```text
Install the Osmosis plugin from https://github.com/Osmosis-AI/osmosis-plugin for this agent, following the repository's installation instructions.
```

Prefer to run the commands yourself? Choose your client:

**Codex**

```sh
codex plugin marketplace add Osmosis-AI/osmosis-plugin
codex plugin add osmosis@osmosis
```

**Claude Code**

```sh
claude plugin install osmosis --marketplace Osmosis-AI/osmosis-plugin
```

Already in a Claude Code session? Use `/plugin install osmosis --marketplace Osmosis-AI/osmosis-plugin`. On older clients, run `claude plugin marketplace add Osmosis-AI/osmosis-plugin` followed by `claude plugin install osmosis@osmosis`.

**VS Code:** [Install Osmosis in VS Code](https://vscode.dev/redirect?url=vscode%3A%2F%2Fchat-plugin%2Finstall%3Fsource%3DOsmosis-AI%252Fosmosis-plugin%26plugin%3Dosmosis). Open the desktop app, choose **Install** for `osmosis`, then **Trust** if you recognize `Osmosis-AI/osmosis-plugin`. This installs the eleven skills and the docs connection. Copilot Chat may ask you to sign in on your first request.

**Cursor (individual):** Run this in the project where you want to use the eleven skills:

```sh
npx skills add Osmosis-AI/osmosis-plugin --agent cursor --skill '*'
```

Open that project in Cursor and start a new chat. For live docs search, add `https://docs.osmosis.ai/mcp` as a remote MCP server in Cursor. Teams can also import this repository into a [Cursor team marketplace](https://cursor.com/docs/plugins#add-a-team-marketplace).

**Other clients:** For GitHub Copilot CLI, use `copilot plugin install Osmosis-AI/osmosis-plugin:plugins/osmosis`. For Grok Build, use `grok plugin install 'Osmosis-AI/osmosis-plugin#plugins/osmosis' --trust`. For Antigravity, OpenCode, or Gemini CLI, use the Cursor command above with `--agent antigravity`, `--agent opencode`, or `--agent gemini-cli`, respectively.

## Tell your agent what you want to do

Start a fresh session after installation, in your project if you have one, and say:

> I'm new to Osmosis. Check what I already have and guide me through my first evaluation.

The `get-started` skill checks your goal and existing work, recommends a route, and helps with the next step. You do not need to choose skills or read a tutorial first. If automatic selection misses it, ask “Use Osmosis get-started,” select it in your client's skills picker, or use `/osmosis:get-started` in Claude Code.

To try the plugin without an Osmosis account, CLI, or dataset, ask **“Help me plan a fair comparison of two support-ticket agents. I have labeled examples; don't run an evaluation.”**

You can also ask **“Show me the bundled Osmosis sample evaluation. Which support-ticket agent would you choose, and why?”** It compares two agents on five synthetic tickets, including timeouts.

A typical route is **define the task → prepare data → create and verify a rollout → evaluate results → train and compare checkpoints → deploy when needed**. A rollout is your agent's work on one input plus how its result is scored. You can stop at a useful evaluation, start from an existing result, or go straight to catalog benchmarks. Your agent reuses completed work and explains what each result supports before recommending the next step.

Try a more specific request when you already know your goal:

| You want to… | Tell your agent… |
| --- | --- |
| Explore an idea | “Help me plan an evaluation of a customer-support classifier. I have no data yet; don't run anything.” |
| Evaluate an existing agent | “Help me evaluate this agent on these examples. Inspect the project and prepare the first check.” |
| Improve a model | “Help me plan training for this task, including a baseline, reward, and held-out comparison.” |
| Run a benchmark | “Help me choose a catalog benchmark and estimate the trial budget before running it.” |
| Understand results | “Analyze these evaluation results and recommend my next step using the existing files.” |

## Setup when you need it

You can plan an experiment or analyze supplied files with just the plugin. For execution, your agent helps set up the [Osmosis CLI and SDK](https://docs.osmosis.ai/sdk/installation), the workflow's dependencies, and model credentials.

Managed evaluation, training, platform datasets, and deployment use an Osmosis account and workspace. Your agent guides you through [workspace onboarding](https://docs.osmosis.ai/platform/onboarding), using the existing `osmosis quickstart` wizard when appropriate, and continues in your connected workspace repository. Installing this plugin does not install the CLI, create an account, or connect that repository for you.

Model calls, sandboxes, and managed runs can incur charges. Your agent carries out the scope you authorize; uploads, Git publishing, paid execution, and deployment need that scope to be clear. Keep credentials in your trusted environment or secret manager.

## Included skills

The plugin includes eleven skills. Your agent can select them from your request:

| Skill | What it helps you do |
| --- | --- |
| `get-started` | Find your starting point and follow the workflow toward a useful result. |
| `plan-eval` / `plan-training` | Define the experiment, grading or reward, data, and budget. |
| `prepare-datasets` | Prepare, validate, split, upload, or download data. |
| `create-rollouts` | Build an agent workflow and its grader or Harbor verifier. |
| `test-rollouts` / `debug-rollouts` | Verify workflow behavior and investigate failures. |
| `run-evals` / `run-training` | Prepare and manage runs, analyze results, and compare checkpoints. |
| `run-benchmarks` | Select catalog tasks and harnesses, manage runs, and analyze results. |
| `deploy-models` | Select and serve a checkpoint, or use its inference endpoint. |

Each skill carries its own instructions and resources. The onboarding route is installed with `get-started`; it does not depend on the agent reading this README. For precise command details, skills check the installed SDK and current documentation.

## Help and updates

If a skill is missing, confirm installation in the current client and start a fresh session. To update, run `codex plugin marketplace upgrade osmosis` followed by `codex plugin add osmosis@osmosis` in Codex, or `claude plugin marketplace update osmosis` followed by `claude plugin update osmosis@osmosis` in Claude Code. If you installed with `npx skills`, rerun that command.

The included `osmosis-docs` connection uses the public [documentation MCP endpoint](https://docs.osmosis.ai/mcp) without an API key. If unavailable, your agent can use the [documentation index](https://docs.osmosis.ai/llms.txt). Documentation queries go to the hosted service; use public product terms. Documentation feedback is sent only when requested. Uploading a local result sends its dataset rows, logs, trajectories, and artifacts to the selected workspace.
