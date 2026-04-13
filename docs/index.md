# HA-VLN Participant Documentation

HA-VLN provides two things to challenge participants:

- `HASimulator`, the simulator stack for human-aware vision-language navigation
- the public HA-VLN data package, including the human-populated navigation data needed for development and validation

If you are a participant, you usually only need to answer three questions:

1. How do I install `HASimulator` and obtain the public data?
2. Which APIs can I call when building my own agent?
3. After my agent is ready, how do I test it?

This documentation is organized around exactly those three tasks.

## Recommended Reading Order

### 1. Install HASimulator and Prepare Data
Start here if you are setting up the environment for the first time.

- [Install HASimulator and Get Public Data](participant/install_and_data.md)

### 2. Develop Your Agent with HASimulator APIs
Read this when you already have the environment running and want to build your own method.

- [Develop Your Agent](participant/develop_agent.md)

### 3. Test Your Agent
Read this after your agent can run and you want to validate it on the public workflow.

- [Test Your Agent](participant/test_agent.md)

## Using the Current Docker Challenge Runtime

If you are validating against the current executable challenge workflow, the most relevant next pages are:

- [Challenge Overview](challenge/overview.md)
- [Challenge Getting Started](challenge/getting_started.md)
- [Challenge Agent Integration Guide](challenge/integration_guide.md)
- [Challenge Submission Format](challenge/submission_format.md)

Use these pages when you need the current mounted-path contract, the official runner path under `/app/agent`, the current adapter entry through `CHALLENGE_AGENT`, or the Docker-based local validation flow.

## Reference Material

If you need more detail, the reference pages are still available:

- installation details: [Dependencies](quick_start/dependencies.md), [Installation Steps](quick_start/installation.md), [Data Download](quick_start/data.md)
- integration details for local method development outside the Docker challenge runtime: [Agent Integration Notes](quick_start/integration.md)
- simulator and metric APIs: [Human State](api/human_state.md), [Scene Updates](api/scene_updates.md), [Collision Checks](api/collision_checks.md), [Evaluation Metrics](api/evaluation_metrics.md)
