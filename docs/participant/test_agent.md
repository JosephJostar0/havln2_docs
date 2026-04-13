# Test Your Agent

This page is for participants who already have a runnable HA-VLN agent and want to validate it.

From a participant perspective, testing answers one question:

- can my agent run correctly on the public HA-VLN workflow before submission?

## What to Test

Before considering your agent ready, test the following:

- the agent can start correctly in the HA-VLN environment
- the required data paths are resolved correctly
- the agent behaves correctly in dynamic human scenes
- the expected outputs and metrics can be produced

## Recommended Testing Path

### 1. Test on public development splits

Run your agent on the public validation workflow first. This is the safest way to catch:

- path issues
- configuration mistakes
- broken environment synchronization
- missing dependencies
- incorrect metric handling

### 2. Inspect collision-aware behavior

Because HA-VLN is human-aware, test not only navigation success but also collision-related behavior.

Use:

- [Collision Checks](../api/collision_checks.md)
- [Evaluation Metrics](../api/evaluation_metrics.md)

### 3. Use the participant runtime workflow when needed

If you are using the current Docker-based participant workflow, the integrated challenge pages describe the current runtime contract and testing interface:

- [Challenge Getting Started](../challenge/getting_started.md)
- [Challenge Agent Integration Guide](../challenge/integration_guide.md)
- [Challenge Submission Format](../challenge/submission_format.md)

These pages are useful when you want to confirm the current executable participant interface such as mounted paths, the official runner path, and `run.sh` behavior.

## Recommended First Validation Loop

If you are validating against the current Docker challenge workflow, a practical first loop is:

1. run your package on a public split such as `val_unseen`
2. confirm that `run.sh` starts cleanly and enters the official runner path
3. inspect the host-visible result files produced by your current local workflow
4. read the exported score summary before debugging deeper issues

In the current workflow, exported files under `/app/result` often include:

- `score_summary.json`
- `episode_metrics.json`
- `stats_ckpt_0_<split>.json`
- `challenge_eval.log`

The current local workflow uses `/app/result` as the default output location, so these files are usually easiest to inspect there.

## Practical Testing Checklist

Before you move on, confirm that:

- your agent loads the correct HA-VLN task configuration
- the required public data exists under the expected paths
- your `run.sh` prepares the runtime and then launches the official runner path
- your agent can finish evaluation runs without runtime errors
- the outputs you care about are written correctly
- your metrics are interpretable and consistent with HA-VLN definitions

## Common Failure Patterns

### Config or path mismatch

If the environment boots but evaluation fails quickly, check:

- the task config path used by your method
- checkpoint paths inside the mounted package
- whether your code is still using host-side absolute paths instead of container paths

### Runner contract mismatch

If your package starts but does not behave like the current challenge workflow, check whether `run.sh` is still launching:

```bash
python /app/agent/run.py --run-type eval --exp-config /app/agent/config/challenge_submission.yaml RESULTS_DIR /app/result LOG_FILE /app/result/challenge_eval.log
```

### Metrics look surprising

If plain success looks reasonable but challenge-facing `SR` looks low, revisit:

- [Evaluation Metrics](../api/evaluation_metrics.md)
- [Collision Checks](../api/collision_checks.md)

In the current evaluator, `SR` is a strict success metric rather than a direct alias for plain environment `success`. For summary files, `score_summary.json` reports `NE`, while `stats_ckpt_0_<split>.json` keeps the underlying aggregated key name `distance_to_goal`.

## If You Need More Detail

For low-level integration and evaluation details, see:

- [Agent Integration Notes](../quick_start/integration.md)
- [Evaluation Metrics](../api/evaluation_metrics.md)
- [Challenge Runtime Docs](../challenge/overview.md)
