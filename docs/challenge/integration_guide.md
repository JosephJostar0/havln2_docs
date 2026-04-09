# HA-VLN Challenge Agent Integration Guide

This guide explains how to adapt an existing navigation method to the current HA-VLN challenge runtime.

The key point is that the current participant interface is executable: your method must run inside the official Docker image through `/app/agent/run.sh`.

## 1. Start from the Runtime Contract

Inside the official container, the current validated mount points are:

- `/app/Data` for challenge data
- `/app/agent` for your submission package
- `/app/result` for exported artifacts

The container starts your code with:

```bash
bash /app/agent/run.sh
```

So the first integration task is not a JSON exporter. It is making sure your method can boot from that entry script and operate on mounted container paths.

## 2. Wrap Your Existing Agent Behind `run.sh`

If you already have a working HA-VLN or VLN codebase, package it so that `run.sh` becomes the only required external entrypoint.

A practical `run.sh` template is:

```bash
#!/usr/bin/env bash
set -euo pipefail

cd /app/agent

# Optional dependency installation.
# pip install -r requirements.txt

# Optional environment activation.
# source /opt/conda/etc/profile.d/conda.sh
# conda activate your-env

python your_agent_entry.py \
  --data-root /app/Data \
  --output-dir /app/result
```

## 3. Convert Host Paths to Container Paths

A common failure mode is keeping host-side absolute paths inside configs or launch scripts. During challenge execution, your code should read from container-visible locations instead.

Use container paths such as:

- `/app/Data`
- `/app/agent`
- `/app/result`

Do not assume the evaluator can see paths from your own workstation.

## 4. Connect HA-VLN Task Configurations Carefully

When adapting an existing agent, keep the real HA-VLN task configuration in view. For example, the repository already uses HA-VLN-specific task and dataset fields such as `episode_id`, `trajectory_id`, and `scene_id`, and the baseline stack saves evaluation outputs through `RESULTS_DIR` when `EVAL.SAVE_RESULTS` is enabled.

That means an integration usually needs two layers:

1. make the method runnable from `run.sh`
2. make the method export its useful outputs to a location you can copy into `/app/result`

## 5. Export Results Explicitly

Some existing training or evaluation code writes results into internal experiment directories. In the challenge workflow, you should make the final exported artifacts easy to recover from `/app/result`.

Typical approaches include:

- configuring your evaluation output directory directly under `/app/result`
- copying final predictions from an internal experiment directory into `/app/result` at the end of `run.sh`
- writing a concise metadata file that explains what was produced

## 6. Keep the Method Contract Open

The challenge should not force a single internal design. Your submission may be:

- a classic policy-style VLN agent
- a planner with external memory
- a world-model-based agent
- another executable navigation system

What matters is that the package runs inside the official image and respects the mounted path contract.

## 7. Local Validation Workflow

Use the repository root compose template to validate your integration locally:

```bash
cp docker-compose-template.yml docker-compose.yml
# edit the host-side mount paths

docker compose up
```

If you need to debug interactively:

```bash
docker compose run --rm --entrypoint bash evaluator
```

## 8. Suggested Integration Checklist

Before calling the package challenge-ready, verify that:

- `run.sh` boots without manual intervention
- your method can find all required data under `/app/Data`
- model checkpoints are accessible inside the mounted package or through another documented mechanism
- exported outputs appear under `/app/result`
- no step depends on hidden host-specific paths

## Related Pages

- [Challenge Overview](overview.md)
- [Getting Started](getting_started.md)
- [Submission Format](submission_format.md)
