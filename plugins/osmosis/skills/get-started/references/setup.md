# Setup and workspace access

Read this when the next step needs the CLI, a rollout runtime, or platform resources. Check existing state first and preserve the user's project and environment.

## CLI and runtime

The plugin supplies skills and documentation access; `osmosis-ai` supplies the SDK and `osmosis` CLI. Check `osmosis --version` when the CLI is needed. If missing, follow [CLI installation](https://docs.osmosis.ai/cli/installation) using the user's package manager and Python 3.12+ environment. Install only the [SDK extras](https://docs.osmosis.ai/sdk/installation) the selected workflow needs; local evaluation needs `eval`, while rollout dependencies live in their own environment. Installing the CLI alone does not update rollout dependencies.

Planning and analysis of supplied files require neither package nor platform access. Local-file evaluation without upload needs its model/backend credentials, but no Osmosis login. Model calls and cloud sandboxes may still incur charges. Read credential presence without printing values; use the trusted environment or secret manager rather than chat.

## Platform setup

For platform datasets, managed execution, or serving, establish the intended workspace and reuse its connected repository. When the CLI is available:

- `osmosis --json auth whoami` verifies identity and may report a linked workspace. An authenticated response without a workspace does not mean login failed.
- `osmosis --json doctor` checks the current project's workspace layout. It does not establish authentication, billing readiness, or hosted execution. Use fixes only when a layout change is needed and within scope.
- For platform-only operations outside the clone, use root `--workspace <name>` once the target is known. Avoid inferring it from an unrelated repository.

If setup is missing, use the existing [onboarding flow](https://docs.osmosis.ai/platform/onboarding). The platform's Quickstart page leads to the same wizard:

```sh
osmosis quickstart
# When the target workspace is already known:
osmosis quickstart --workspace <name>
```

This is an interactive wizard, not a status check: it can sign in, clone a repository, remember its path, and record setup completion. It needs a human-interactive terminal and rejects `--json`, `--plain`, and non-TTY execution. When the agent cannot provide that interaction, have the user run it in their terminal, or guide only the missing manual steps from the onboarding documentation. Do not repeatedly retry it in a noninteractive tool.

The flow authenticates, resolves an existing workspace, waits for its GitHub connection, reuses or clones its repository, checks billing, and asks for a goal. A new user creates or joins a workspace on the [platform](https://platform.osmosis.ai); a workspace owner or admin connects GitHub when needed. Invited members use the existing connected repository. Preserve existing directories and clone into a new path only when needed.

Billing can be deferred during setup; paid platform operations check it separately. The wizard's completion flag does not prove billing or execution readiness. It produces a task prompt for training, evaluation, or benchmarks; continue from that intent, mapping older skill names to the skills actually installed (the current catalog benchmark skill is `run-benchmarks`).

Resume in the returned workspace directory and inspect its existing files before creating anything. Retain the user's goal, known workspace, and completed steps across the handoff. For project-scoped skills-only installs, verify skill discovery in the destination workspace before starting a new session; install there from the original GitHub source if needed, preserving the chosen scope rather than silently switching to a global install.

Before managed execution, the relevant execution skill checks the dataset, secrets, billing/access, and pushed source revision; local success or an uploaded local result does not verify the hosted path.
