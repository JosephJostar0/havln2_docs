# HA-VLN Challenge: Getting Started

This guide provides a step-by-step process for participating in the HA-VLN challenge. Follow these steps to set up your environment, integrate your agent, and generate valid submissions.

## Quick Overview

1. **Set up environment** - Install dependencies and configure HA-VLN
2. **Download data** - Get the HA-R2R dataset and human motion models
3. **Integrate your agent** - Adapt your VLN agent to work with HA-VLN
4. **Test on validation splits** - Verify your integration works correctly
5. **Generate test submission** - Run inference on the test split
6. **Validate and submit** - Check your submission format and submit

## Step 1: Environment Setup

### 1.1 System Requirements
- Linux system
- Conda package manager
- (Optional) NVIDIA GPU for accelerated rendering

### 1.2 Install Dependencies
Follow the [Quick Start: Dependencies](../quick_start/dependencies.md) guide to install system packages and create the Conda environment.

### 1.3 Install HA-VLN
Follow the [Quick Start: Installation](../quick_start/installation.md) guide to install:
- Habitat-Sim and Habitat-Lab
- GroundingDINO (for human counting, optional)
- Agent requirements

## Step 2: Data Download

### 2.1 Download Datasets
```bash
# Download HA-R2R and HAPS 2.0 datasets
bash scripts/download_data.sh

# Download Matterport3D scenes (requires access)
# Follow instructions in record_env.md
```

### 2.2 Dataset Structure
After downloading, you should have:
- `Data/HA-R2R/train/` - Training split
- `Data/HA-R2R/val_seen/` - Validation seen split
- `Data/HA-R2R/val_unseen/` - Validation unseen split
- `Data/HA-R2R/test/` - Test split (for inference)
- `Data/HAPS2_0/` - Human motion models

### 2.3 Verify Data
Check that the test split is available:
```bash
ls Data/HA-R2R/test/
# Should show: test.json.gz and test_bertidx.json.gz
```

## Step 3: Agent Integration

### 3.1 Review Integration Guide
Read the [Agent Integration Guide](integration_guide.md) to understand:
- How to configure your agent for HA-VLN
- How to handle dynamic human activities
- How to collect action sequences

### 3.2 Configure Your Agent
Update your agent's configuration to use the HA-VLN environment:

```yaml
# Example configuration update
BASE_TASK_CONFIG_PATH: HASimulator/config/HAVLNCE_task.yaml
```

### 3.3 Implement Action Collection
Add action collection to your evaluation loop:

```python
# Pseudocode for action collection
actions = []
while not episode_over:
    action = agent.act(observation)
    actions.append(action)
    observation = env.step(action)
    
# Save actions for this episode
save_episode_actions(episode_id, actions)
```

## Step 4: Testing on Validation Splits

### 4.1 Run Validation
Before generating test submissions, test your agent on validation splits:

```bash
# Example command structure
python your_agent.py \
    --split val_unseen \
    --output val_unseen_results.json
```

### 4.2 Check Metrics
Verify that your agent produces reasonable metrics on validation splits. This helps catch integration issues early.

### 4.3 Validate Action Sequences
Ensure your action sequences meet the requirements:
- All actions are in {0, 1, 2, 3}
- Each episode ends with STOP (0)
- Sequences are 1-500 actions long

## Step 5: Generate Test Submission

### 5.1 Run Inference on Test Split
```bash
# Run inference on the test split
python your_agent.py \
    --split test \
    --output my_submission.json \
    --agent_name "YourAgentName"
```

### 5.2 Submission Format
Your submission must follow the [Submission Format Specification](submission_format.md):
- JSON format with `episodes` array
- Each episode must have: `episode_id`, `trajectory_id`, `scene_id`, `actions`
- Include all 3408 test episodes

### 5.3 Example Submission Structure
```json
{
  "episodes": [
    {
      "episode_id": "0",
      "trajectory_id": "5732",
      "scene_id": "mp3d/5ZKStnWn8Zo/5ZKStnWn8Zo.glb",
      "actions": [1, 1, 2, 1, 0]
    }
    // ... 3407 more episodes
  ],
  "metadata": {
    "agent_name": "YourAgentName",
    "split": "test"
  }
}
```

## Step 6: Validate and Submit

### 6.1 Check Submission Completeness
Verify your submission:
- Contains exactly 3408 episodes
- All required fields are present
- Action sequences are valid
- File is valid JSON

### 6.2 File Size Check
A typical submission file should be:
- **Expected size**: 1-10 MB
- **Too small (< 100KB)**: Likely missing episodes
- **Too large (> 100MB)**: May include unnecessary data

### 6.3 Submit Your Results
Follow the challenge submission instructions (to be announced) to submit your `my_submission.json` file.

## Common Pitfalls and Solutions

### Pitfall 1: Missing Dependencies
**Symptom**: Import errors for habitat_sim or HASimulator.
**Solution**: Follow the installation guides carefully. Check Python version compatibility if using newer Python versions.

### Pitfall 2: Human Models Not Loading
**Symptom**: No humans appear in the environment.
**Solution**: Check that `ADD_HUMAN: True` is set in your configuration and the human model paths are correct.

### Pitfall 3: Action Collection Issues
**Symptom**: Submission has wrong number of episodes or invalid actions.
**Solution**: Use the ActionCollector class from the integration guide and test on validation splits first.

### Pitfall 4: Performance Issues
**Symptom**: Inference is very slow.
**Solution**: 
- Use `ALLOW_SLIDING: True` to prevent getting stuck
- Consider disabling human counting if not needed
- Use appropriate batch sizes for your hardware

## Testing Checklist

Before submitting, complete this checklist:

### Environment
- [ ] Conda environment created and activated
- [ ] Habitat-Sim and Habitat-Lab installed
- [ ] HA-VLN dependencies installed
- [ ] Data downloaded and accessible

### Agent Integration
- [ ] Agent configured to use HA-VLN task
- [ ] Action collection implemented
- [ ] Tested on val_unseen split
- [ ] Action sequences validated

### Submission
- [ ] Inference run on test split
- [ ] Submission file contains 3408 episodes
- [ ] All actions are valid (0, 1, 2, 3)
- [ ] Each episode ends with STOP
- [ ] JSON file is valid

## Next Steps

### After Submission
1. **Wait for evaluation** - Organizers will run your submission through the private evaluator
2. **Receive results** - You'll get metrics (SR, TCR, NE, CR) for your submission
3. **Leaderboard ranking** - Compare your results with other participants

### Improving Your Agent
- Analyze failure cases from validation splits
- Consider human-aware navigation strategies
- Optimize for both success rate and collision avoidance
- Test different hyperparameters and architectures

## Support Resources

### Documentation
- [Agent Integration Guide](integration_guide.md) - Detailed integration instructions
- [Submission Format](submission_format.md) - Complete format specification
- [HA-VLN API](../api/) - Environment APIs and interfaces
- [Quick Start](../quick_start/) - Installation and setup guides

### Code Examples
- Example submissions in `challenge/examples/`
- Baseline agent code in `agent/`
- Integration examples in this guide

### Community
- GitHub repository issues
- Challenge announcement channels
- Related research papers and benchmarks

## Important Notes

### Development vs Submission
- **Development**: Use train/val splits for training and validation
- **Submission**: Only use test split for final inference
- **No test tuning**: Do not tune hyperparameters on test results

### Fair Competition
- Develop your own agent
- Do not share test predictions
- Follow the challenge rules and timeline

### Timeline
- Check challenge announcements for deadlines
- Submit early to avoid last-minute issues
- Allow time for validation and testing

Good luck with the challenge! Remember to test thoroughly and submit valid, complete submissions.