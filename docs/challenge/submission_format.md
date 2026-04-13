# HA-VLN Challenge Submission Format

This page defines the current submission contract for the HA-VLN challenge workflow.

A submission is an `agent/` package that will be mounted into the official evaluation container at `/app/agent`. Once `agent/` is mounted, `bash /app/agent/run.sh` should be able to start the full evaluation flow directly.

## Required Files And Implementations

Your `agent/` package must keep the following files and flow:

- `run.sh` is required. We provide a reference implementation, but you must customize it for your own method.
- `run.py` is required and must be kept.
- `eval.py` is required and must be kept.
- `config/challenge_submission.yaml` is required and must be kept. We provide a template, but you must customize its contents for your own method.

Inside `run.sh`, you must call:

```bash
python /app/agent/run.py   --run-type eval   --exp-config /app/agent/config/challenge_submission.yaml   RESULTS_DIR /app/result   LOG_FILE /app/result/challenge_eval.log
```

`run.sh` is where you should install any extra dependencies into the provided environment and prepare your own method code before launching the official runner.

Your own implementation may include any internal agent design, but it should stay behind this official entry flow instead of replacing it with a different Python entrypoint.

## Recommended Package Layout

A recommended `agent/` package layout is:

```text
agent/
|-- run.sh                          # Required. We provide a reference implementation; you must customize it.
|-- run.py                          # Required. Official runner file. Keep this path.
|-- eval.py                         # Required. Official evaluator file. Keep this path.
|-- config/
|   `-- challenge_submission.yaml   # Required. We provide a template; you must customize its contents.
|-- submission/                     # Optional. Your own agent implementation code.
|-- checkpoints/                    # Optional. Your model weights.
`-- requirements.txt                # Optional. Extra dependencies installed from run.sh.
```

The files under `submission/`, `checkpoints/`, and any other method-specific directories are fully controlled by you.

The official image also provides `/app/official_agent_template` as a reference starter package. In particular, `run.sh` and `config/challenge_submission.yaml` should be treated as official templates that you customize for your own method, not files to use unchanged.

## Validation Checklist

Before using a package, verify that:

- `bash /app/agent/run.sh` starts successfully inside the container.
- `run.sh` eventually calls the required `python /app/agent/run.py ...` command above.
- your code reads inputs from `/app/Data` and writes useful outputs to `/app/result`.
- your package includes all code, weights, and files needed at runtime.

## Related Pages

- [Getting Started](getting_started.md)
- [Agent Integration Guide](integration_guide.md)
- [Evaluation Metrics](../api/evaluation_metrics.md)
