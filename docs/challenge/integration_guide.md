# HA-VLN Challenge Agent Integration Guide

This guide explains how to adapt an existing navigation method to the current HA-VLN challenge runtime.

The key point is that your method must run inside the official Docker image through `/app/agent/run.sh`, and the mounted challenge package must preserve the official HA-VLN runner path.

## 1. Start from the Runtime Contract

Inside the official container, the current validated mount points are:

- `/app/Data` for challenge data
- `/app/agent` for your mounted challenge package

The current local workflow uses `/app/result` as the default output location for exported evaluation artifacts, but that path should not yet be treated as the finalized stable submission artifact contract.

Start the container, attach a shell, and run your code with `bash /app/agent/run.sh`. So the first integration task is not a JSON exporter. It is making sure your method can boot from that entry script, operate on mounted container paths, and still use the official challenge runner path under `/app/agent`.

## 2. Integration Boundary

In the current workflow, it helps to think in three layers.

### Official runner layer

This layer should remain aligned with the official challenge package:

- `/app/agent/run.py`
- the evaluation flow `run.py -> eval.py`
- the required run mode `--run-type eval`
- `/app/agent/config/challenge_submission.yaml`

### Participant wrapper layer

This layer is participant-owned:

- `/app/agent/run.sh`
- environment activation
- participant-specific dependency installation
- launching the official runner path

### Participant method layer

This layer is where your own method lives:

- adapter module path and factory wiring
- method code
- checkpoints
- custom dependencies and helper files

Participants are free to adapt their own method behind the wrapper and adapter layers, but they should not replace the official HA-VLN challenge runner path itself.

## 3. Wrap Your Existing Agent Behind `run.sh`

If you already have a working HA-VLN or VLN codebase, package it into an agent/ directory or archive so that `run.sh` becomes the integration hook.

In the current challenge workflow, `run.sh` is your entry script, but the Python evaluation entrypoint is not arbitrary. Your `run.sh` should finish by calling the official `/app/agent/run.py` in `eval` mode.

For the current workflow, the outer config file should live at:

```text
/app/agent/config/challenge_submission.yaml
```

A practical local starting point is to create your own challenge submission config at that path and point it to your adapter module, method-specific configuration, and checkpoints.

The participant method is currently instantiated through `CHALLENGE_AGENT.MODULE`, `CHALLENGE_AGENT.CONFIG`, and optional `CHALLENGE_AGENT.FACTORY_FN`, with `build_agent` as the default factory name. `CHALLENGE_AGENT.MODULE` should be a Python import path such as `submission.my_agent_adapter`. The returned agent object must implement `reset`, `act`, and `close`.

A practical `run.sh` template is:

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

In the current official image, starting a shell does not automatically activate the prebuilt Conda environment for you. If you want to use the image's validated Python stack, explicitly activate `havlnce` inside `run.sh`.

The official image also provides `/app/official_agent_template` as a backup/reference starter package. That package includes `run.py`, `eval.py`, `run-example.sh`, `config/challenge_submission.yaml`, and `submission/` adapter examples. Treat it as a reference, not as a substitute for your own submission `run.sh`.

In this workflow, `run.sh` is an integration hook, not a license to redefine the evaluator entry contract.

## 4. Convert Host Paths to Container Paths

A common failure mode is keeping host-side absolute paths inside configs or launch scripts. During challenge execution, your code should read from container-visible locations instead.

Use container paths such as:

- `/app/Data`
- `/app/agent`
- `/app/result` for the default exported outputs of the current workflow

Do not assume the evaluator can see paths from your own workstation.

## 5. Connect HA-VLN Task Configurations Carefully

When adapting an existing agent, keep the real HA-VLN task configuration in view. For example, the repository already uses HA-VLN-specific task and dataset fields such as `episode_id`, `trajectory_id`, and `scene_id`. In the current official evaluator path, exported evaluation outputs are written through `RESULTS_DIR`, with the current local workflow using `/app/result` as the default output location.

Your outer config should still enter through `/app/agent/config/challenge_submission.yaml`, even if the method-specific settings behind that file come from your own adapter or baseline-derived configuration.

That means an integration usually needs three layers:

1. preserve the official runner path under `/app/agent`
2. make the method runnable from `run.sh`
3. make the method expose whatever outputs the current evaluator or local workflow expects

If your method previously started from a custom script such as `my_eval.py`, move that logic behind your adapter or wrapper layer and still enter through the official `/app/agent/run.py` path.

## 6. Export Results Explicitly

Some existing training or evaluation code writes results into internal experiment directories. In the challenge workflow, you should make the useful exported artifacts easy to recover from `/app/result`, the default output location used by the current local workflow.

The current local workflow uses `/app/result` as the default exported output location. Typical approaches include:

- configuring your evaluation output directory to `/app/result`
- copying final predictions from an internal experiment directory into a host-visible mounted path at the end of `run.sh`
- writing a concise metadata file that explains what was produced

## 7. Keep the Method Contract Open

The challenge should not force a single internal design. Your submission may be:

- a classic policy-style VLN agent
- a planner with external memory
- a world-model-based agent
- another executable navigation system

What matters is that the package runs inside the official image, respects the mounted path contract, and keeps the official challenge runner path intact.

## 8. Local Validation Workflow

Use the repository root compose template to validate your integration locally:

```bash
cp docker-compose-template.yml docker-compose.yml
# edit the host-side mount paths

docker compose up -d
docker compose exec evaluator bash
bash /app/agent/run.sh
```

If you need to debug interactively:

```bash
docker compose run --rm --entrypoint bash evaluator
```

## 9. Suggested Integration Checklist

Before calling the package challenge-ready, verify that:

- the container starts cleanly and stays available for shell attach
- `run.sh` prepares your environment and then launches the official runner path
- your method can find all required data under `/app/Data`
- model checkpoints are accessible inside the mounted package or through another documented mechanism
- exported outputs appear under `/app/result`
- no step depends on hidden host-specific paths

## Related Pages

- [Challenge Overview](overview.md)
- [Getting Started](getting_started.md)
- [Submission Format](submission_format.md)
