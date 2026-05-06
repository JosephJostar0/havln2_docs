# Develop Your Agent

This page is for researchers and developers who want to build or adapt an agent on top of HA-VLN.

The main idea is straightforward:

- HA-VLN provides the simulator and the public data
- you provide or adapt a navigation agent
- your agent interacts with the HA-VLN environment through the simulator/task configuration and available APIs

The repository `agent/` directory should be treated as reference code and baseline material, not as a required starting point. You are free to build your own agent code as long as it is compatible with the HA-VLN environment and evaluation workflow.

## What Developers Usually Need from the Simulator

When developing an agent, you typically need to understand three things:

1. how to point their code to the HA-VLN task configuration
2. which simulator or task APIs expose dynamic-human information
3. which metrics and runtime behaviors matter during evaluation

## Start from the HA-VLN Task Configuration

The first integration step is to switch your agent to the HA-VLN task configuration.

See the detailed integration notes here:

- [Agent Integration Notes](../quick_start/integration.md)

In particular, check:

- `BASE_TASK_CONFIG_PATH`
- `SIMULATOR.ADD_HUMAN`
- `SIMULATOR.ALLOW_SLIDING`
- measurement fields used during evaluation

## API Categories You Will Likely Use

### 1. Human State APIs
Use these when your agent needs information related to nearby humans, distances, angles, or human-aware observations.

- [Human State Queries](../api/human_state.md)

### 2. Dynamic Scene Update APIs
Use these when your agent or wrapper needs to stay synchronized with the dynamic human timeline.

- [Dynamic Scene Updates](../api/scene_updates.md)

### 3. Collision and Safety APIs
Use these when you want to inspect collision-related behavior or build human-aware evaluation logic.

- [Collision Checks](../api/collision_checks.md)

### 4. Evaluation Metrics
Use these when you want to understand how success and collision-aware performance are measured.

- [Evaluation Metrics](../api/evaluation_metrics.md)

## Typical Development Workflow

A practical development workflow is:

1. start from an existing VLN agent or your own new implementation
2. switch it to the HA-VLN task configuration
3. make sure it can run in the dynamic human environment
4. use the simulator APIs above to inspect human-aware state when needed
5. validate your behavior and metrics on public splits

## Important Development Notes

- HA-VLN is not limited to one specific agent architecture
- you can develop your own agent or adapt an existing one
- the repository `agent/` directory is optional reference material rather than required agent code
- the important requirement is compatibility with the HA-VLN environment and evaluation workflow

If you need low-level integration details such as wrapper synchronization, provided text resources, or metric injection, read:

- [Agent Integration Notes](../quick_start/integration.md)

## Next Step

After your agent can run inside HA-VLN, move on to:

- [Test Your Agent](test_agent.md)
