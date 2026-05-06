# HA-VLN Documentation

HA-VLN provides human-aware navigation data and environments for studying instruction-following agents in scenes with dynamic humans.

This documentation focuses on the public HA-VLN workflow:

- installing the HA-VLN runtime and `HASimulator`
- preparing the public HA-VLN data package
- developing and testing agents against the human-aware environment
- understanding the simulator APIs and human-aware metrics

If you are getting started, you usually need to answer three questions:

1. How do I install `HASimulator` and obtain the public data?
2. Which APIs can I call when building or adapting an agent?
3. After an agent can run, how do I test it on public splits?

This documentation is organized around exactly those three tasks.

## Recommended Reading Order

### 1. Install HASimulator and Prepare Data
Start here if you are setting up the environment for the first time.

- [Install HASimulator and Get Public Data](participant/install_and_data.md)

### 2. Develop an Agent with HASimulator APIs
Read this when you already have the environment running and want to build your own method.

- [Develop Your Agent](participant/develop_agent.md)

### 3. Test an Agent
Read this after your agent can run and you want to validate it on public HA-VLN data.

- [Test Your Agent](participant/test_agent.md)

## Reference Material

If you need more detail, the reference pages are still available:

- installation details: [Dependencies](quick_start/dependencies.md), [Installation Steps](quick_start/installation.md), [Data Download](quick_start/data.md)
- integration details for local method development: [Agent Integration Notes](quick_start/integration.md)
- simulator and metric APIs: [Human State](api/human_state.md), [Scene Updates](api/scene_updates.md), [Collision Checks](api/collision_checks.md), [Evaluation Metrics](api/evaluation_metrics.md)
