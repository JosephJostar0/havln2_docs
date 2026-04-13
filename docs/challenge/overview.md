# HA-VLN Challenge Overview

The current HA-VLN challenge workflow uses an agent/ package that runs inside the official Docker image, not a fixed JSON-only upload contract.

## Core Goal

The HA-VLN challenge evaluates instruction-following navigation in indoor scenes with dynamic humans. A valid solution should:

- follow natural-language navigation instructions
- navigate in human-populated environments
- avoid unnecessary human collisions
- remain compatible with the official evaluation runtime

The challenge should stay method-open. Participants are not restricted to a single policy style and may use broader designs, including world-model-based agents, as long as they satisfy the executable submission interface.

## Current Evaluation Model

The current evaluation workflow is based on an official prebuilt Docker image and a mounted submission directory.

### Mounted Paths

During evaluation, the runtime mounts:

- participant data at `/app/Data` (read-write)
- the participant challenge package at `/app/agent` (read-write in the current validated compose setup)

The current local workflow also uses `/app/result` as the default output location for exported evaluation artifacts. That default output location is useful for local validation, but it should not yet be treated as the finalized stable submission artifact contract.

### Official Evaluation Start

The current compose template starts a long-lived container for manual validation. Participants then start evaluation inside the container with:

```bash
bash /app/agent/run.sh
```

Participants must therefore provide their own `/app/agent/run.sh`. In practice, the agent/ package should contain `run.sh`, agent code, model weights, and the official runner files.

That script is participant-owned, but it is not an arbitrary evaluator contract. Its job is to prepare the participant-specific environment and then launch the official HA-VLN challenge runner for the mounted `/app/agent` package.

### Official Runner Path Inside `/app/agent`

The current official runner path is built around:

- `/app/agent/run.py`
- the evaluation flow `run.py -> eval.py`
- the required run mode `--run-type eval`
- the current challenge-facing config path `/app/agent/config/challenge_submission.yaml`

Participants may customize their own method code, adapters, checkpoints, and dependency setup behind this path, but they should not replace the official challenge runner with an arbitrary Python entrypoint.

### Current Agent Adapter Entry

Inside the official evaluation path, the current evaluator instantiates the participant method through the submission config section:

- `CHALLENGE_AGENT.MODULE`
- `CHALLENGE_AGENT.CONFIG`
- optional `CHALLENGE_AGENT.FACTORY_FN`

The current default factory name is `build_agent`. `CHALLENGE_AGENT.MODULE` should be a Python import path such as `submission.my_agent_adapter`.

This means participants may choose their own adapter module path and method-specific configuration, while still entering through the official runner path under `/app/agent`.

## Public Development Data

Use the public training and validation data for local development. The `/app/Data` mount should remain writable because the runtime may generate mesh-related artifacts there.

## Participant-Provided vs Official Runner Components

Participants provide:

- `/app/agent/run.sh`
- participant-specific environment activation and dependency installation
- participant method code, adapters, checkpoints, and any extra resources needed by that code
- the adapter module and configuration selected through `CHALLENGE_AGENT`

The official challenge runner defines:

- the evaluation path `run.py -> eval.py`
- the required run mode `--run-type eval`
- the current config entry path `/app/agent/config/challenge_submission.yaml`
- the expected mounted execution layout inside the official image

## What Is Concrete Right Now

The following pieces are already concrete in the repository:

- the official image name in `docker-compose-template.yml`
- the mounted container paths `/app/Data` and `/app/agent`
- the current compose template includes the default output mount `/app/result`
- the current default local output location `/app/result`
- the participant start command `bash /app/agent/run.sh`
- the current official runner path under `/app/agent`
- the local compose-based workflow for participant-side validation

## Workflow

At a high level, the current participant workflow is:

1. prepare the required data locally
2. prepare a mounted `/app/agent` package that follows the official challenge runner layout
3. provide a participant-owned `run.sh` entry script
4. mount data, submission, and output paths through Docker Compose, including `/app/result` as the current default output location
5. start the official image locally, attach a shell, and use `run.sh` to prepare your method environment and launch the official runner in `eval` mode
6. run the official image locally for integration testing
7. inspect the exported outputs under `/app/result`

## Reference Code and Baselines

The repository `agent/` directory should be understood as reference and baseline material. Participants do not need to adopt the repository's baseline method code directly.

What participants must satisfy is the simulator and runtime contract built around `HASimulator`, the mounted challenge data, and the official runner path under the mounted `/app/agent` package.

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
