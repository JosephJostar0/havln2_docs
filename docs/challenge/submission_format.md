# HA-VLN Challenge Submission Format

This page defines the current participant-facing submission contract for the `challenge-next` package.

The current contract is an executable submission package, not a fixed JSON-only file format.

## Submission Unit

Participants should assume that a submission is a directory mounted into the official evaluation container at:

```text
/app/agent
```

The container then runs:

```bash
bash /app/agent/run.sh
```

## Required Files

### `run.sh`

Your submission package must provide a shell entry script at:

```text
/app/agent/run.sh
```

This script is the stable start point for participant code in the current workflow.

## Recommended Package Layout

A typical submission directory may look like this:

```text
submission/
├── run.sh
├── requirements.txt
├── your_agent_entry.py
├── src/
├── configs/
└── checkpoints/
```

The internal layout is flexible as long as `run.sh` can launch your method successfully.

## Runtime Mounts

The validated compose workflow currently mounts:

| Host-side role | Container path | Mode | Purpose |
|---|---|---|---|
| challenge data | `/app/Data` | read-write | public development data locally, challenge evaluation data during final runs, and any mesh-related artifacts generated under Data |
| submission package | `/app/agent` | read-write | participant code and runtime working area |
| output directory | `/app/result` | read-write | exported artifacts |

## What Your Submission Must Do

Your submission should:

- start from `run.sh`
- read required inputs from mounted paths inside the container
- avoid assuming access to host-only absolute paths
- write exported outputs to `/app/result`

Your submission may:

- install additional dependencies from `run.sh`
- activate or create environments needed by your own method, if that remains compatible with the provided image
- use any internal architecture, including broader agent designs such as world-model-based systems

## Subject To Change

The items in this section are not finalized yet and may change as the challenge package is refined.

## What Is Not Fixed Yet

The following are intentionally not hard-coded as part of the stable contract today:

- a mandatory JSON prediction schema for every submission
- a mandatory internal model architecture
- a mandatory evaluation-time invocation beyond `bash /app/agent/run.sh`

If a future evaluator requires a stricter exported artifact schema, that change should be documented explicitly and validated against code.

## Output Expectations

Write any challenge-facing artifacts to:

```text
/app/result
```

Typical outputs may include:

- predictions
- logs
- metrics summaries
- copied metadata needed for debugging or reproducibility

A practical pattern is to keep all final exported files under `/app/result` even if your method also uses temporary working directories elsewhere.

## Minimal Example

Example `run.sh`:

```bash
#!/usr/bin/env bash
set -euo pipefail

cd /app/agent

# Optional setup steps.
# pip install -r requirements.txt

python your_agent_entry.py --data-root /app/Data --output-dir /app/result
```

## Validation Checklist

Before handing off a submission package, verify that:

- `run.sh` exists at the package root
- `docker compose up` can start your method in the official image
- your code reads inputs from container paths rather than host paths
- exported artifacts appear under `/app/result`
- the package does not depend on unmounted private local files

## Related Pages

- [Getting Started](getting_started.md)
- [Agent Integration Guide](integration_guide.md)
- [Evaluation Metrics](../api/evaluation_metrics.md)
