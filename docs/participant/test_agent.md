# Test Your Agent

This page is for researchers and developers who already have a runnable HA-VLN agent and want to validate it.

From a development perspective, testing answers one question:

- can my agent run correctly on the public HA-VLN workflow?

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

## Recommended First Validation Loop

A practical first loop is:

1. run your package on a public split such as `val_unseen`
2. confirm that the evaluation script starts cleanly and uses the intended HA-VLN task configuration
3. inspect the result files produced by your current local workflow
4. read the exported score summary before debugging deeper issues

Depending on your launch script, exported files often include:

- `score_summary.json`
- `episode_metrics.json`
- `stats_ckpt_0_<split>.json`
- evaluation logs

## Practical Testing Checklist

Before you move on, confirm that:

- your agent loads the correct HA-VLN task configuration
- the required public data exists under the expected paths
- your agent can finish evaluation runs without runtime errors
- the outputs you care about are written correctly
- your metrics are interpretable and consistent with HA-VLN definitions

## Common Failure Patterns

### Config or path mismatch

If the environment boots but evaluation fails quickly, check:

- the task config path used by your method
- checkpoint paths inside the mounted package
- whether your code is still using host-side absolute paths instead of container paths

### Metrics look surprising

If plain success looks reasonable but strict success `SR` looks low, revisit:

- [Evaluation Metrics](../api/evaluation_metrics.md)
- [Collision Checks](../api/collision_checks.md)

In the current evaluator, `SR` is a strict success metric rather than a direct alias for plain environment `success`. For summary files, `score_summary.json` reports `NE`, while `stats_ckpt_0_<split>.json` keeps the underlying aggregated key name `distance_to_goal`.

## If You Need More Detail

For low-level integration and evaluation details, see:

- [Agent Integration Notes](../quick_start/integration.md)
- [Evaluation Metrics](../api/evaluation_metrics.md)
