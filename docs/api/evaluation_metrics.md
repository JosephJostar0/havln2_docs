# Evaluation Metrics

This document explains the evaluation metrics used in HA-VLN. Understanding these metrics will help you interpret your agent's behavior during development and validation.

## Overview

HA-VLN uses four core metrics:

1. **Success Rate (SR)**
2. **Trajectory Collision Rate (TCR)**
3. **Navigation Error (NE)**
4. **Collision Rate (CR)**

## Metric Definitions

### 1. Success Rate (SR)

**Definition**: Percentage of episodes completed successfully.

**Success Criteria**:
1. the agent reaches the goal position within the success threshold
2. the agent avoids all collisions with dynamic humans under the strict metric rule
3. the episode ends with a valid STOP action when that protocol is used by the evaluation setup

**Formula**:
```text
SR = (Number of successful episodes) / (Total episodes) × 100%
```

Higher is better.

### 2. Trajectory Collision Rate (TCR)

**Definition**: Average number of human-collision events per episode after excluding the pre-computed unavoidable collision component used by the metric implementation.

**Formula**:
```text
TCR = Σ(Net human-collision events) / (Total episodes)
```

Lower is better.

**Interpretation**:
- `TCR = 0`: no counted human-collision events
- `TCR > 0`: some counted human-collision events occurred

### 3. Navigation Error (NE)

**Definition**: Mean distance between the agent's final position and the goal.

**Units**: meters

**Formula**:
```text
NE = Σ(Distance to goal at episode end) / (Total episodes)
```

Lower is better.

### 4. Collision Rate (CR)

**Definition**: Percentage of episodes with at least one counted human collision.

**Formula**:
```text
CR = (Episodes with >=1 collision) / (Total episodes) × 100%
```

Lower is better.

## Ranking Priority

When challenge results are compared, the intended priority is:

1. **SR**
2. **TCR**
3. **NE**

`CR` is mainly diagnostic.

## Human-Aware Interpretation

### What Counts as a Collision?

In HA-VLN, the human-aware metrics are meant to reflect interaction with dynamic humans rather than only static-scene collisions.

### Why TCR and CR Both Matter

- `TCR` measures how much human-collision behavior accumulates across episodes
- `CR` measures how often at least one collision happens

Together they help you distinguish frequency from severity.

## Practical Implications for Participants

When iterating on your own agent, use these metrics together rather than optimizing only one of them.

Useful questions to ask are:

- does the agent reach goals reliably?
- does the agent stay safe around dynamic humans?
- are failures caused more by navigation error or by human collisions?

## Notes

- exact final challenge ranking and reporting details may still be refined
- participant-facing docs should focus on how to interpret the metrics, not on overfitting to unpublished evaluation details

## Further Reading

- [Challenge Overview](../challenge/overview.md)
- [Agent Integration Guide](../challenge/integration_guide.md)
- [Collision Checks](collision_checks.md)
