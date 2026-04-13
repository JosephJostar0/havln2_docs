# HA-VLN Challenge Getting Started

This guide describes the current challenge participation flow based on the official Docker image and the executable submission package interface.

## Quick Path

1. prepare the repository and public data locally
2. prepare your mounted `/app/agent` package with the official runner path and your own `run.sh`
3. copy `docker-compose-template.yml` to `docker-compose.yml`
4. replace host-side mount paths with your absolute local paths
5. start the official image locally and attach a shell
6. inspect the exported outputs under `/app/result`

## 1. Prepare Local Assets

Before using the Docker workflow, make sure you have:

- a local checkout of this repository
- the public development data you need under a local `Data` directory
- an agent/ package containing your run.sh, agent code, weights, and any extra resources needed for evaluation
- Docker and Docker Compose available on the host

If your method needs extra Python packages, install them into the provided environment from inside `run.sh` or from scripts called by `run.sh`.

## 2. Pull the Official Evaluation Image

Pull the official image referenced by the repository compose template:

```bash
docker pull ghcr.io/josephjostar0/havln-eval-image:latest
```

## 3. Create a Local Compose File

Copy the template from the repository root:

```bash
cp docker-compose-template.yml docker-compose.yml
```

Then edit the host-side paths in `docker-compose.yml` so that they mount:

- your local `Data` directory to `/app/Data:rw`
- your submission directory to `/app/agent:rw`
- a local output directory to `/app/result:rw` for the default exported outputs of the current workflow

## 4. Prepare the Submission Entry Script

The current runtime contract expects you to start evaluation from inside the container with:

```bash
bash /app/agent/run.sh
```

Your mounted `/app/agent` package must therefore include a runnable `run.sh` file together with your agent code and weights.

That `run.sh` should not replace the official HA-VLN challenge runner with an arbitrary Python entrypoint. Its role is to install any extra dependencies you need into the provided environment and then launch the official challenge entrypoint in evaluation mode through the mounted `/app/agent` package.

A compliant starting template looks like this:

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

For the current official image, do not assume the container shell has already activated the prebuilt Conda environment. If you want the validated image environment, explicitly activate `havlnce` at the start of the script.

The mounted challenge package should still use the official runner files `run.py`, `eval.py`, and `config/challenge_submission.yaml`. The participant method is currently instantiated through `CHALLENGE_AGENT.MODULE`, `CHALLENGE_AGENT.CONFIG`, and optional `CHALLENGE_AGENT.FACTORY_FN`, with `build_agent` as the default factory name. `CHALLENGE_AGENT.MODULE` should be a Python import path such as `submission.my_agent_adapter`. Participants may adapt the environment setup, dependency installation, and method-specific files behind this path, but they should not replace the official runner with an arbitrary evaluation entrypoint.

The official image also includes `/app/official_agent_template` as a backup/reference starter package. That package includes `run.py`, `eval.py`, `run-example.sh`, `config/challenge_submission.yaml`, and `submission/` adapter examples. It is a starter reference only. Participants are still expected to provide their own `/app/agent/run.sh`.

## 5. Run the Container Locally

Start the evaluation container in the background with:

```bash
docker compose up -d
```

Then attach an interactive shell and start evaluation manually:

```bash
docker compose exec evaluator bash
bash /app/agent/run.sh
```

This keeps the startup flow explicit while still validating that your package can boot inside the official image and access the expected mounted paths.

## 6. Debug Interactively When Needed

If you want a one-off shell without starting the long-lived service first, use:

```bash
docker compose run --rm --entrypoint bash evaluator
```


Useful checks inside the container include:

```bash
cd /app
ls /app/Data
ls /app/agent
ls /app/result
```

## 7. Validate on Public Splits First

Before relying on any final challenge-facing workflow, validate your method on the public training or validation splits available in your local `Data` mount.

This step is especially important for checking:

- path assumptions inside your code
- model checkpoint loading
- any extra dependency installation done from `run.sh`
- whether your method integrates correctly behind the official runner path
- any exported logs or prediction artifacts expected by your current local workflow

## 8. What to Export

The challenge does not yet require a single finalized exported artifact path.

The current local workflow writes exported outputs to `/app/result` by default. Typical outputs may include:

- prediction files
- logs
- copied checkpoints or metadata used for reproducibility

Write useful artifacts to `/app/result` during local validation. If a later evaluator revision requires a stricter output schema or location, that should be documented explicitly at that time.

## Common Issues

### `run.sh` is missing or not runnable

Make sure your mounted challenge package contains `/app/agent/run.sh` and that the script can execute under `bash`.

### Paths work locally but fail in Docker

Use container paths inside your submission code:

- `/app/Data`
- `/app/agent`
- `/app/result` for the default exported outputs of the current workflow

Do not rely on host-side absolute paths once you are inside the container.

### Extra dependencies are unavailable

Install them from `run.sh` or from a helper script called by `run.sh`.

### The official image environment is not active inside `run.sh`

If your script relies on packages that were preinstalled into the official image, explicitly activate the Conda environment first:

```bash
source /opt/conda/etc/profile.d/conda.sh
conda activate havlnce
```

Do not assume a fresh container shell inherits that activation automatically.

### My package starts a custom Python entrypoint instead of the official runner

Do not treat `run.sh` as permission to replace the challenge evaluation flow entirely. Your script should integrate your own method behind the official `/app/agent/run.py --run-type eval --exp-config /app/agent/config/challenge_submission.yaml` path.

### Results are not visible on the host

The current local workflow expects exported artifacts under `/app/result`, so make sure useful files are written or copied there instead of existing only in temporary internal paths.

## Next Reading

- [Challenge Overview](overview.md)
- [Agent Integration Guide](integration_guide.md)
- [Submission Format](submission_format.md)
