# Evaluation Metrics

This document explains the evaluation metrics used in the HA-VLN Challenge. Understanding these metrics will help you optimize your agent for the challenge.

## Overview

HA-VLN uses four core metrics to evaluate agent performance:

1. **Success Rate (SR)** - Primary ranking metric
2. **Trajectory Collision Rate (TCR)** - Secondary ranking metric
3. **Navigation Error (NE)** - Tertiary ranking metric
4. **Collision Rate (CR)** - Additional diagnostic metric

## Metric Definitions

### 1. Success Rate (SR)

**Definition**: Percentage of episodes completed successfully.

**Success Criteria**:
1. Agent reaches the goal position (within 3.0 meters)
2. Agent avoids all collisions with dynamic humans
3. Episode ends with a valid STOP action

**Formula**:
```
SR = (Number of successful episodes) / (Total episodes) × 100%
```

**Importance**: Primary ranking metric. Higher is better.

**Optimization Strategy**:
- Focus on accurate navigation to goals
- Implement effective human collision avoidance
- Ensure proper episode termination

### 2. Trajectory Collision Rate (TCR)

**Definition**: Average number of collisions with dynamic humans per episode.

**Calculation**:
- Counts collisions within 1.0 meter of human models
- Excludes unavoidable collisions (pre-computed baseline)
- Averages across all episodes

**Formula**:
```
TCR = Σ(Collisions with humans) / (Total episodes)
```

**Importance**: Secondary ranking metric. Lower is better.

**Interpretation**:
- **TCR = 0**: Perfect human avoidance
- **TCR > 0**: Some human collisions occurred
- Lower TCR indicates better social navigation

**Optimization Strategy**:
- Implement human detection and tracking
- Maintain safe distances from humans
- Predict human movement patterns

### 3. Navigation Error (NE)

**Definition**: Mean distance between agent's final position and goal.

**Units**: Meters

**Formula**:
```
NE = Σ(Distance to goal at episode end) / (Total episodes)
```

**Importance**: Tertiary ranking metric. Lower is better.

**Interpretation**:
- **NE = 0**: Perfect navigation (reached exact goal)
- **NE < 3.0**: Successful navigation (within success threshold)
- **NE > 3.0**: Failed to reach goal vicinity

**Optimization Strategy**:
- Improve path planning accuracy
- Enhance visual localization
- Reduce cumulative navigation errors

### 4. Collision Rate (CR)

**Definition**: Percentage of episodes with at least one collision.

**Calculation**:
- Binary indicator per episode (0 or 1)
- 1 if any collision with dynamic humans occurred
- 0 if no human collisions

**Formula**:
```
CR = (Episodes with ≥1 collision) / (Total episodes) × 100%
```

**Importance**: Diagnostic metric. Lower is better.

**Interpretation**:
- Measures collision frequency
- Complements TCR (which measures collision severity)
- Helps identify systematic collision issues

## Ranking Priority

When comparing agents on the leaderboard:

1. **Primary**: Success Rate (SR) - Higher is better
2. **Secondary**: Trajectory Collision Rate (TCR) - Lower is better
3. **Tertiary**: Navigation Error (NE) - Lower is better
4. **Diagnostic**: Collision Rate (CR) - For analysis only

**Tie-breaking**:
- If SR is equal, compare TCR
- If TCR is equal, compare NE
- CR is used for analysis but not ranking

## Metric Relationships

### Trade-offs
- **Navigation vs Safety**: Aggressive navigation may improve SR but increase TCR
- **Speed vs Accuracy**: Faster movement may reduce NE but increase collisions
- **Conservative vs Aggressive**: Conservative agents may have lower TCR but also lower SR

### Ideal Agent Profile
- **High SR**: Successfully reaches goals
- **Low TCR**: Minimizes human collisions
- **Low NE**: Accurate navigation
- **Low CR**: Consistent collision avoidance

## Human-Aware Considerations

### What Counts as a Collision?
- Physical contact with human 3D models
- Within 1.0 meter proximity threshold
- Dynamic humans only (not static objects)
- Excludes unavoidable collisions (baseline subtracted)

### Human Activity Impact
- Moving humans are harder to avoid
- Groups increase collision risk
- Predictable paths are easier to navigate around
- Unpredictable movements challenge collision avoidance

## Practical Implications

### For Agent Design
1. **Balance Objectives**: Optimize for both SR and TCR
2. **Human Modeling**: Consider predicting human movements
3. **Safety Margins**: Maintain distance from humans
4. **Adaptive Behavior**: Adjust based on human density

### For Training
1. **Reward Design**: Include both goal-reaching and collision penalties
2. **Curriculum**: Start with simple scenes, progress to crowded ones
3. **Augmentation**: Vary human positions and activities
4. **Validation**: Monitor all metrics during development

### For Evaluation
1. **Comprehensive**: Consider all metrics together
2. **Contextual**: Interpret metrics relative to scene difficulty
3. **Comparative**: Compare with baseline performance
4. **Diagnostic**: Use metrics to identify weaknesses

## Baseline Performance

Reference performance levels (approximate):

| Metric | Random Agent | Forward-Only | HA-VLN-CMA (Baseline) |
|--------|--------------|--------------|----------------------|
| SR | ~5% | ~10% | ~40% |
| TCR | ~2.5 | ~1.8 | ~0.8 |
| NE | ~8-10m | ~6-8m | ~4-5m |
| CR | ~80% | ~60% | ~30% |

**Note**: Actual values depend on specific implementation and random seeds.

## Optimization Tips

### For SR
- Improve instruction understanding
- Enhance visual grounding
- Better path planning
- More accurate goal localization

### For TCR
- Implement human detection
- Predict human trajectories
- Maintain safe distances
- Slow down near humans

### For NE
- Reduce cumulative errors
- Improve pose estimation
- Better mapping and localization
- More precise movement control

### For All Metrics
- Test on validation splits
- Analyze failure cases
- Iterate based on metrics
- Balance trade-offs consciously

## Common Pitfalls

### Over-optimizing Single Metric
- Maximizing SR at expense of high TCR
- Minimizing TCR but failing to reach goals
- Focusing on NE while ignoring collisions

### Ignoring Human Factors
- Treating humans as static obstacles
- Not considering human movement patterns
- Underestimating social navigation complexity

### Validation-Test Mismatch
- Overfitting to validation splits
- Not testing diverse human scenarios
- Ignoring metric correlations

## Further Reading

- [HA-VLN Paper](https://arxiv.org/abs/2503.14229) - Detailed metric definitions
- [Challenge Overview](../challenge/overview.md) - Challenge context
- [Agent Integration Guide](../challenge/integration_guide.md) - Implementation guidance
- [HA-R2R Dataset](https://github.com/F1y1113/HA-VLN/tree/main/Data/HA-R2R) - Data characteristics

## Summary

Successful HA-VLN agents must:
1. **Navigate accurately** to reach goals (high SR, low NE)
2. **Avoid collisions** with dynamic humans (low TCR, low CR)
3. **Balance trade-offs** between navigation and safety
4. **Adapt to diverse** human activities and scenarios

Use these metrics to guide your agent development and optimization. Good luck!