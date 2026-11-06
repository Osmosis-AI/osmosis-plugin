# Analyze training and checkpoints

Use `osmosis --json train info <run-name>` for configuration, metrics, progress, and checkpoint availability, and `train logs <run-name>` for observed failures. To save metrics, use `train info <run-name> --output <new-file.json>`; preserve previous exports. Select the workspace explicitly outside a connected clone. If metrics are pending or unavailable, state that rather than treating missing values as zero. Use customer-visible CLI output or supplied artifacts; analysis does not require internal infrastructure access.

Establish the base model, training data, rollout/grader revision, reward definition, training settings, and metric axes. Keep rollout batches, optimizer steps, and checkpoint steps distinct unless the evidence maps them. Compare the same metric and sampling window; an aggregate reward alone cannot show a learning trend.

| Question | Evidence and interpretation |
| --- | --- |
| Is the run making progress? | Inspect available step/timestamp series, completed samples, logs, and timeout/error counts. Distinguish queueing or slow rollouts from optimization. Missing telemetry does not prove a stalled run. |
| Does rising reward mean improvement? | Compare fixed held-out evaluations of the baseline and checkpoints. Match row identities, attempts, grader/threshold, rollout revision, and relevant settings. Count graded and ungraded outcomes separately with explicit denominators; changing completion rates can distort averages. Training reward alone does not establish held-out improvement. |
| Why is held-out performance worse? | Check coverage and comparison mismatches first, then representative outputs for task regressions, reward shortcuts, or verifier problems. Overfitting and reward gaming are hypotheses until the evidence supports them; increasing epochs or timeouts is not a diagnosis. |
| Which checkpoint should be used? | Rank available checkpoints by comparable held-out evidence for the user's target, including completion reliability. Report a tie or insufficient evidence when appropriate. The latest checkpoint or highest training reward need not be the best candidate. |

Read existing eval artifacts or `osmosis --json eval info <run-name>` for held-out evidence; when sample outcomes are needed, use the installed `eval download` selectors with a fresh output directory. Align examples by identity instead of assuming equal-sized datasets match. Training does not schedule periodic evaluations automatically; absent checkpoint evaluations are a missing measurement to propose, not a reason to launch paid work during analysis.

Return the observed trend, comparable held-out result, evidence for the likely cause, recommended checkpoint or next experiment, and remaining uncertainty. Analysis alone leaves training runs, configs, datasets, and deployments unchanged; stopping, submitting, or deploying requires authorization for that action.
