# Python integrations

Use the installed scaffold's `LocalBackend(workflow=..., grader=...)`, `create_rollout_server`, and server startup. Declare `server` and the relevant integration extras in the rollout's own `pyproject.toml`. Avoid hard-coding a provider or policy model in the workflow: Osmosis routes the policy and records its trajectory.

When adding modules to a packaged scaffold, configure its build backend's package discovery. For a setuptools flat layout with `main.py` and an imported `existing_agent.py`, declare both in `[tool.setuptools].py-modules`; keep tests outside the runtime module list. A direct import can succeed while the first `eval run` fails during editable installation, so include dependency sync in runtime acceptance.

Despite its name, the active context's `chat_completions_url` is an OpenAI-compatible **base URL**. Pass it unchanged to an OpenAI client's `base_url` or an integration's `api_base`. For a raw HTTP POST, append `/chat/completions` after stripping a trailing slash; preserve the supplied path rather than adding another `/v1`. Use the context's API key so the request stays on the rollout's model route.

| Who owns the message history? | How to return one sample |
| --- | --- |
| Workflow | Return a message list, or `AgentWorkflowOutput(messages=..., metrics=...)`. Metrics must be finite numbers. |
| Strands integration | Construct `OsmosisStrandsAgent` inside `run` with `messages=ctx.prompt` and `OsmosisRolloutModel`. Await `agent.invoke_async()` and return `None`. |
| OpenAI Agents integration | Construct `OsmosisAgent` with `OsmosisRolloutModel()` and one `OsmosisMemorySession()` inside `run`; pass that session to `Runner.run` and return `None`. |
| Custom integration | Register one source with `get_rollout_context().set_sample_source(...)` and return `None`, or return the history yourself. |

Check the reward on a representative success and failure. In metadata mode the prompt may be empty; grade the agreed task outcome rather than assuming a label exists.
