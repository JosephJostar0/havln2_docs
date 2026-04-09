# HA-VLN Challenge Getting Started

This guide describes the current challenge participation flow based on the official Docker image and the executable submission package interface.

## Quick Path

1. prepare the repository and public data locally
2. prepare your submission directory with a `run.sh` entrypoint
3. copy `docker-compose-template.yml` to `docker-compose.yml`
4. replace host-side mount paths with your absolute local paths
5. run the official image locally
6. inspect outputs written to `/app/result`

## 1. Prepare Local Assets

Before using the Docker workflow, make sure you have:

- a local checkout of this repository
- the public development data you need under a local `Data` directory
- your own agent code packaged in a separate submission directory or in a prepared agent workspace
- Docker and Docker Compose available on the host

If your method needs extra Python packages or custom runtime setup, install them from inside `run.sh` or from scripts called by `run.sh`.

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
- your local output directory to `/app/result:rw`

## 4. Prepare the Submission Entry Script

The current runtime contract expects the container to execute:

```bash
bash /app/agent/run.sh
```

Your submission package must therefore include a runnable `run.sh` file.

A minimal example looks like this:

```bash
#!/usr/bin/env bash
set -euo pipefail

cd /app/agent

# Install any submission-specific dependencies here if needed.
# Example:
# pip install -r requirements.txt

python your_agent_entry.py --data-root /app/Data --output-dir /app/result
```

## 5. Run the Container Locally

Start the evaluation service with:

```bash
docker compose up
```

This is the fastest way to validate that your package can boot inside the official image and access the expected mounted paths.

## 6. Debug Interactively When Needed

If you want to inspect the runtime before `run.sh` executes, open an interactive shell instead:

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
- exported logs or prediction artifacts under `/app/result`

## 8. What to Export

The current stable expectation is that your method writes any exported artifacts needed for inspection or downstream evaluation to `/app/result`.

Typical outputs may include:

- prediction files
- logs
- copied checkpoints or metadata used for reproducibility

If a later evaluator revision requires a stricter output schema, that should be documented explicitly at that time.

## Common Issues

### `run.sh` is missing or not runnable

Make sure your submission package contains `/app/agent/run.sh` and that the script can execute under `bash`.

### Paths work locally but fail in Docker

Use container paths inside your submission code:

- `/app/Data`
- `/app/agent`
- `/app/result`

Do not rely on host-side absolute paths once you are inside the container.

### Extra dependencies are unavailable

Install them from `run.sh` or from a helper script called by `run.sh`.

### Results are not visible on the host

Write exported artifacts to `/app/result`, not only to internal temporary directories.

## Next Reading

- [Challenge Overview](overview.md)
- [Agent Integration Guide](integration_guide.md)
- [Submission Format](submission_format.md)
