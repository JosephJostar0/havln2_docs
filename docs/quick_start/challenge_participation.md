# Challenge Participation

This guide provides a quick overview of how to participate in the HA-VLN Challenge. For detailed instructions, see the [Challenge Overview](../challenge/overview.md).

## Quick Start Checklist

### 1. Environment Setup
- [ ] Install system dependencies
- [ ] Create Conda environment with Python
- [ ] Install Habitat-Sim and Habitat-Lab
- [ ] Install HA-VLN dependencies

### 2. Data Preparation
- [ ] Download HA-R2R dataset
- [ ] Download HAPS 2.0 human motion models
- [ ] Download Matterport3D scenes (requires access)
- [ ] Verify data structure

### 3. Agent Integration
- [ ] Configure your agent for HA-VLN environment
- [ ] Implement action collection during inference
- [ ] Test on validation splits
- [ ] Validate action sequences

### 4. Submission Generation
- [ ] Run inference on test split
- [ ] Generate submission JSON file
- [ ] Verify submission format
- [ ] Submit through official channel

## Essential Documentation

### Core Challenge Docs
- **[Challenge Overview](../challenge/overview.md)** - Challenge objectives and structure
- **[Getting Started](../challenge/getting_started.md)** - Step-by-step participation guide
- **[Agent Integration](../challenge/integration_guide.md)** - How to adapt your agent
- **[Submission Format](../challenge/submission_format.md)** - Detailed format specification

### Technical Reference
- **[API Documentation](../api/)** - Environment interfaces and APIs
- **[Evaluation Metrics](../api/evaluation_metrics.md)** - Challenge metrics explained
- **[HA-R2R Dataset](https://github.com/F1y1113/HA-VLN/tree/main/Data/HA-R2R)** - Dataset details

## Common Tasks

### Task 1: Configure Your Agent
```yaml
# Update your agent's configuration
BASE_TASK_CONFIG_PATH: HASimulator/config/HAVLNCE_task.yaml
```

### Task 2: Collect Actions
```python
# During inference, collect action sequences
actions = []
while not episode_over:
    action = agent.act(observation)
    actions.append(action)  # Save for submission
    observation = env.step(action)
```

### Task 3: Generate Submission
```python
# Format actions as JSON
submission = {
    "episodes": [{
        "episode_id": "0",
        "trajectory_id": "5732",
        "scene_id": "mp3d/...",
        "actions": [1, 1, 2, 0]  # Your collected actions
    }],
    "metadata": {
        "agent_name": "YourAgent",
        "split": "test"
    }
}
```

## Action Codes

| Code | Action | Description |
|------|--------|-------------|
| 0 | STOP | End episode |
| 1 | MOVE_FORWARD | Move forward 0.25m |
| 2 | TURN_LEFT | Turn left 15° |
| 3 | TURN_RIGHT | Turn right 15° |

**Important**: Each episode must end with STOP (0).

## Validation Checklist

Before submitting, verify:

- [ ] Submission is valid JSON
- [ ] Contains all 3408 test episodes
- [ ] Each episode has required fields
- [ ] All actions are valid (0, 1, 2, 3)
- [ ] Action sequences are 1-500 actions long
- [ ] Each episode ends with STOP

## Testing Strategy

### Phase 1: Development
- Use `train/` split for training
- Use `val_seen/` and `val_unseen/` for validation
- Test agent integration and action collection

### Phase 2: Validation
- Run full inference on validation splits
- Verify action sequences meet requirements
- Check for common issues (missing STOP, invalid actions)

### Phase 3: Submission
- Run inference on `test/` split only once
- Generate final submission file
- Validate format before submitting

## Troubleshooting

### Issue: Human Models Not Loading
**Solution**: Check `ADD_HUMAN: True` in configuration and verify human model paths.

### Issue: Invalid Action Codes
**Solution**: Ensure your agent outputs only {0, 1, 2, 3}.

### Issue: Missing Episodes
**Solution**: Verify your inference loop processes all 3408 test episodes.

### Issue: Submission Too Large/Small
**Solution**: 
- **Too small (< 100KB)**: Likely missing episodes
- **Too large (> 100MB)**: May include unnecessary data
- **Expected**: 1-10 MB for 3408 episodes

## Next Steps

1. **Read Detailed Guides**: Start with [Getting Started](../challenge/getting_started.md)
2. **Integrate Your Agent**: Follow [Agent Integration Guide](../challenge/integration_guide.md)
3. **Test Thoroughly**: Validate on splits before test inference
4. **Generate Submission**: Use [Submission Format](../challenge/submission_format.md) as reference
5. **Submit**: Follow official submission instructions

## Support

- **Documentation**: All challenge docs are in the [Challenge](../challenge/) section
- **Examples**: See `challenge/examples/` for sample submissions
- **Code**: Reference implementations in `agent/` directory
- **Issues**: Open GitHub issues for technical problems

## Important Notes

- **No Test Tuning**: Do not tune hyperparameters on test results
- **Independent Development**: Develop your own agent
- **Fair Competition**: All participants use same test data
- **Timeline**: Check announcements for deadlines

Good luck with the challenge!