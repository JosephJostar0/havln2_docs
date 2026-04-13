# Challenge Participation

This page is the short version of the current challenge workflow. For the full contract, see the [Challenge Overview](../challenge/overview.md).

## Current Challenge Interface

The current interface is an agent/ package that runs inside the official Docker image.

Participants start evaluation inside the container with:

```bash
bash /app/agent/run.sh
```

The stable mounted paths are:

- `/app/Data` (read-write)
- `/app/agent`

The current local workflow uses `/app/result` as the default output location for exported evaluation artifacts, but that output path is not yet the finalized stable submission artifact contract.

Participants must provide their own `/app/agent/run.sh`, but the mounted challenge package should still use the official HA-VLN runner path built around `/app/agent/run.py`, `eval.py`, and `/app/agent/config/challenge_submission.yaml` in `eval` mode. The participant method is currently instantiated through `CHALLENGE_AGENT.MODULE`, `CHALLENGE_AGENT.CONFIG`, and optional `CHALLENGE_AGENT.FACTORY_FN`, with `build_agent` as the default factory name.

## Quick Checklist

### 1. Prepare Local Assets
- [ ] Prepare a local `Data` directory with the public data you need
- [ ] Prepare an agent/ package containing your `run.sh`, agent code, weights, and the official runner path
- [ ] Make sure the package includes your own `run.sh`
- [ ] Make sure `run.sh` prepares your method environment and then launches `/app/agent/run.py --run-type eval --exp-config /app/agent/config/challenge_submission.yaml RESULTS_DIR /app/result LOG_FILE /app/result/challenge_eval.log`

### 2. Prepare Docker Workflow
- [ ] Pull `ghcr.io/josephjostar0/havln-eval-image:latest`
- [ ] Copy `docker-compose-template.yml` to `docker-compose.yml`
- [ ] Update host-side volume paths
- [ ] Decide whether to keep the default single-GPU setting or export a multi-GPU `HAVLN_CUDA_VISIBLE_DEVICES` value

### 3. Validate Runtime Integration
- [ ] Confirm your code reads from `/app/Data`
- [ ] Confirm your package can be started from `/app/agent/run.sh`
- [ ] Confirm your package preserves the official runner files under `/app/agent`
- [ ] Confirm exported outputs appear under `/app/result`

## Starting `run.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

source /opt/conda/etc/profile.d/conda.sh
conda activate havlnce

cd /app/agent

# Install any extra dependencies into the provided environment if needed.
# pip install -r requirements.txt

python /app/agent/run.py \
  --run-type eval \
  --exp-config /app/agent/config/challenge_submission.yaml \
  RESULTS_DIR /app/result \
  LOG_FILE /app/result/challenge_eval.log
```

The official image includes `/app/official_agent_template` as a backup/reference starter package. That package includes `run.py`, `eval.py`, `run-example.sh`, `config/challenge_submission.yaml`, and `submission/` adapter examples, but participants must still provide their own `/app/agent/run.sh`.

## Local Validation Commands

```bash
docker compose up -d
docker compose exec evaluator bash
bash /app/agent/run.sh
```

For interactive debugging:

```bash
docker compose run --rm --entrypoint bash evaluator
```

## What To Avoid

- do not assume the challenge only accepts a fixed JSON action file
- do not depend on host-side absolute paths from your own machine
- do not write final exported artifacts only to internal temporary directories
- do not replace the official runner with an arbitrary Python entrypoint
- do not treat `/app/official_agent_template` as a replacement for `/app/agent`

## Next Reading

- [Challenge Overview](../challenge/overview.md)
- [Getting Started](../challenge/getting_started.md)
- [Agent Integration](../challenge/integration_guide.md)
- [Submission Format](../challenge/submission_format.md)
