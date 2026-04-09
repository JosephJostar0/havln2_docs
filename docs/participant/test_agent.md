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

These pages are useful when you want to confirm the current executable participant interface such as mounted paths and `run.sh` behavior.

## Practical Testing Checklist

Before you move on, confirm that:

- your agent loads the correct HA-VLN task configuration
- the required public data exists under the expected paths
- your agent can finish evaluation runs without runtime errors
- the outputs you care about are written correctly
- your metrics are interpretable and consistent with HA-VLN definitions

## If You Need More Detail

For low-level integration and evaluation details, see:

- [Agent Integration Notes](../quick_start/integration.md)
- [Evaluation Metrics](../api/evaluation_metrics.md)
- [Challenge Runtime Docs](../challenge/overview.md)
