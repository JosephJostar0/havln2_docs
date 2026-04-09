# Challenge Participation

This page is the short version of the current challenge workflow. For the full contract, see the [Challenge Overview](../challenge/overview.md).

## Current Challenge Interface

The current participant-facing interface is an executable submission package that runs inside the official Docker image.

The stable entrypoint is:

```bash
bash /app/agent/run.sh
```

The stable mounted paths are:

- `/app/Data` (read-write)
- `/app/agent`
- `/app/result`

## Quick Checklist

### 1. Prepare Local Assets
- [ ] Prepare a local `Data` directory with the public data you need
- [ ] Prepare a submission directory containing your method
- [ ] Make sure the submission directory includes `run.sh`

### 2. Prepare Docker Workflow
- [ ] Pull `ghcr.io/josephjostar0/havln-eval-image:latest`
- [ ] Copy `docker-compose-template.yml` to `docker-compose.yml`
- [ ] Update host-side volume paths

### 3. Validate Runtime Integration
- [ ] Confirm your code reads from `/app/Data`
- [ ] Confirm your package starts from `/app/agent/run.sh`
- [ ] Confirm exported outputs appear under `/app/result`

## Minimal `run.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

cd /app/agent
python your_agent_entry.py --data-root /app/Data --output-dir /app/result
```

## Local Validation Command

```bash
docker compose up
```

For interactive debugging:

```bash
docker compose run --rm --entrypoint bash evaluator
```

## What To Avoid

- do not assume the challenge only accepts a fixed JSON action file
- do not depend on host-side absolute paths from your own machine
- do not write final exported artifacts only to internal temporary directories

## Next Reading

- [Challenge Overview](../challenge/overview.md)
- [Getting Started](../challenge/getting_started.md)
- [Agent Integration](../challenge/integration_guide.md)
- [Submission Format](../challenge/submission_format.md)
