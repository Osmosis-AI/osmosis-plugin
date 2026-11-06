# Harbor task and server wiring

## Create a task from a repository

Start from the user's repository, requested change, and test command. Identify the starting revision, working directory inside the sandbox, required runtime/dependencies, expected output, and observable pass/fail rule. Inspect repository test configuration to fill gaps; ask only for missing task requirements. Keep the user's checkout intact while preparing a reproducible starting snapshot.

Create the task under `rollouts/<name>/tasks/<task-id>/`, using the installed Harbor scaffold and schema (`harbor tasks init --help`, `harbor tasks schema`) to match its format. Complete these artifacts before wiring the rollout:

| Artifact | Required behavior |
| --- | --- |
| `instruction.md` | Describe the task, sandbox paths, constraints, and success criteria the agent must satisfy. Keep the reference answer out of the instructions. |
| `task.toml` | Use the installed schema; set realistic `[agent]` and `[verifier]` `timeout_sec`, and `[environment]` build timeout/resources for this task. Select the image contract below. |
| `environment/` | Provide the image recipe and required build context, with the repository at the intended starting revision, pinned dependencies, and an explicit working directory. Docker `COPY` sources must be inside the build context. Include only task inputs; keep reference solutions, hidden verifier tests, and credentials outside the image. |
| `tests/test.sh` | Run the actual acceptance checks against the agent's work, using explicit paths. Harbor uploads this directory to `/tests` for verification. Preserve test output and write the resulting numeric reward. |
| `solution/solve.sh`, when using oracle | Apply a known-good solution from the same starting state. Harbor supplies it to the oracle at `/solution`; it is not an agent input. A reference solution is optional for the task format. |

For binary grading, write `1` for a passing result and `0` for a failing result to `/logs/verifier/reward.txt`, or use the numeric `reward` key in `/logs/verifier/reward.json`. Capture the real test status, including pipeline failures; with `set -e`, guard the test command so an expected assertion failure still writes zero. Keep dependency, runner, and timeout failures distinguishable in the logs. A process exit code alone is not a reward. Use one reward format and finite numbers; the verifier must evaluate the requested behavior rather than merely whether the script ran.

Local Docker can build `environment/Dockerfile`. OpenSandbox requires an already-built `[environment].docker_image` and never builds that Dockerfile: retain the recipe, build/publish within the authorized scope, and pin the deployed image by digest. An image reference that the backend cannot pull is an execution blocker. Keep task dependencies in the image and let the selected harness manage agent setup. Managed sandbox credentials and a customer's own provider credentials are different; configure the account actually used. See the [Harbor backend reference](https://docs.osmosis.ai/sdk/execution-backends/harbor-backend) for provider setup.

## Connect a new or existing task

For existing tasks, inspect and reuse their instruction, environment, and verifier contract; creating another task or reference solution is unnecessary unless the requested behavior changes them. A task's `tests/test.sh` takes precedence even when a Python grader is configured. Use `grader=None` with task tests; supply a Python grader when the task has no verifier. For a Python workflow or grader, keep its importable package with `pyproject.toml` and set `code_dir` if the SDK cannot infer it.

Pass an SDK-registered name such as `agent="opencode"` for a native harness. The backend wires the active model endpoint; no Python workflow class is needed. Preserve the scaffold's environment selection, `create_rollout_server(backend=backend, lifespan=backend.prewarm_lifespan())`, and server port.

- In `task_mode="dataset"`, point `tasks_dir` at the parent directory and select `<task-id>` through the row's `metadata.harbor_task_id`. Each task keeps its own `instruction.md`; row prompts do not replace it. Use this for tasks with complete instructions, including a dataset containing one task.
- In `task_mode="template"`, point `tasks_dir` at the shared task directory. A non-empty request prompt replaces `instruction.md`; an empty prompt keeps the file. Supply one or the other. For a native agent, this is task input, not a system-prompt override; inspect the materialized file to verify prompt rendering.

Treat dynamic `metadata.harbor_task` and Git sources as executable code: use trusted tasks and pin external revisions. Prepare representative metadata rows and a matching eval config as part of the rollout.

## Validate the task, then the rollout

1. Validate the task config and paths with the installed Harbor parser, and check shell syntax. This establishes a loadable task, not a working environment or verifier.
2. Within the authorized execution scope, verify the known-good solution and a deliberately incorrect or unchanged state in separate clean sandboxes. Use Harbor's `oracle` when `solution/solve.sh` exists; otherwise apply a known-good fixture directly. For the negative case, Harbor CLI's `nop` can leave an initially unsolved task unchanged; use an explicitly wrong fixture if the starting state already passes. `nop` is not an SDK-registered native agent. Require the expected rewards and inspect verifier output; missing rewards or setup failures do not count as a successful negative check.
3. Run one representative row and one attempt through Osmosis `eval run` with the intended native model and backend, as described in the parent skill. Confirm model calls, a captured trajectory, the agent's actual changes/output, and matching verifier evidence. Oracle validates the task and reward path but produces no trainable model trajectory.

Report task checks separately from native rollout acceptance. If a model, image, backend, or credential is unavailable, finish the files and prepared commands, identify the missing input, and leave that runtime check explicitly unverified.
