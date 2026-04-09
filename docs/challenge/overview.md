# HA-VLN Challenge Overview

The current `challenge-next` package is moving from a research repository toward a challenge-ready delivery package. At this stage, the stable participant interface is an executable submission package that runs inside the official Docker image, not a fixed JSON-only upload contract.

## Core Goal

The HA-VLN challenge evaluates instruction-following navigation in indoor scenes with dynamic humans. A valid solution should:

- follow natural-language navigation instructions
- navigate in human-populated environments
- avoid unnecessary human collisions
- remain compatible with the official evaluation runtime

The challenge should stay method-open. Participants are not restricted to a single policy style and may use broader designs, including world-model-based agents, as long as they satisfy the executable submission interface.

## Current Evaluation Model

The current evaluation workflow is based on an official prebuilt Docker image and a mounted submission directory.

### Runtime Contract

During evaluation, the runtime mounts:

- participant data mount at `/app/Data` (read-write)
- participant submission package at `/app/agent` (read-write in the current validated compose setup)
- participant output directory at `/app/result` (read-write)

The container entrypoint executes:

```bash
bash /app/agent/run.sh
```

This means the stable contract today is:

1. your submission must be packaged as runnable code
2. your package must provide `/app/agent/run.sh`
3. your code must read inputs from mounted paths inside the container
4. your code should write exported artifacts to `/app/result`

## Public Development Data vs Organizer-Only Final Scoring

Participants should assume that local development uses public training and validation data, while final challenge evaluation may use a different dataset mount under the same `/app/Data` path. The mount should remain writable because the runtime may generate mesh-related artifacts under `Data`.

This is why the challenge documentation should avoid over-specifying evaluation details that are not part of the stable participant interface.

## What Is Stable Right Now

The following pieces are already concrete in the repository:

- the official image name in `docker-compose-template.yml`
- the mounted container paths `/app/Data`, `/app/agent`, and `/app/result`
- the entry script contract `bash /app/agent/run.sh`
- the local compose-based workflow for participant-side validation

## Subject To Change

The following pieces are still intentionally lightweight and may change as the challenge package is finalized:

- the final scoring pipeline details
- whether a specific exported prediction file schema will be required
- leaderboard policy and submission portal details

## Participant Workflow

At a high level, the current participant workflow is:

1. prepare the required data locally
2. package your method as a runnable submission directory
3. provide a `run.sh` entry script
4. mount data, submission, and output paths through Docker Compose
5. run the official image locally for integration testing
6. export any predictions, logs, or result artifacts to `/app/result`

## Reference Code and Baselines

The repository `agent/` directory should be understood as reference and baseline material. Participants do not need to use that codebase directly.

What participants must satisfy is the simulator and runtime contract built around `HASimulator`, the mounted challenge data, and the executable submission interface.

## Baselines and Documentation

Baseline agents are useful in two roles:

- onboarding examples for participants
- regression targets for evaluator and runtime integration

The challenge documentation should therefore stay aligned with real baseline execution paths, mounted directories, and evaluator assumptions instead of relying on older repo folklore.

## Related Pages

- [Getting Started](getting_started.md)
- [Agent Integration Guide](integration_guide.md)
- [Submission Format](submission_format.md)
- [Evaluation Metrics](../api/evaluation_metrics.md)
