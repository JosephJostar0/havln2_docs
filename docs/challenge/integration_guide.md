# HA-VLN Challenge: Agent Integration Guide

This guide explains how to integrate your Vision-and-Language Navigation (VLN) agent with the HA-VLN environment to participate in the challenge. The focus is on adapting your existing agent to work with dynamic human activities and generating valid submission files.

## Overview

To participate in the HA-VLN challenge, you need to:
1. Configure your agent to work with the HA-VLN dynamic environment
2. Collect action sequences during inference on the test split
3. Format the collected actions according to the submission specification
4. Validate and submit your results

## 1. Environment Configuration

### 1.1 Task Configuration
Update your agent's configuration to use the HA-VLN task configuration:

```yaml
# In your agent's configuration file
BASE_TASK_CONFIG_PATH: path/to/HASimulator/config/HAVLNCE_task.yaml
```

### 1.2 Key Configuration Parameters
Ensure the following parameters are set in `HAVLNCE_task.yaml`:

```yaml
SIMULATOR:
  ADD_HUMAN: True                    # Enable dynamic human rendering
  ALLOW_SLIDING: True                # Recommended to prevent getting stuck
  HUMAN_GLB_PATH: ../Data/HAPS2_0    # Path to human motion models
  HUMAN_INFO_PATH: ../Data/Multi-Human-Annotations/human_motion.json
```

### 1.3 Environment Wrapper
If your agent uses a custom environment wrapper, ensure it handles the dynamic human timeline:

```python
from habitat.core.env import Env
from HASimulator.environments import HAVLNCE

class HAVLNWrapper(Env):
    def __init__(self, config, dataset=None):
        super().__init__(config, dataset)
        self.use_dynamic_human = getattr(self._config.TASK_CONFIG.SIMULATOR, "ADD_HUMAN", False)
        
        if self.use_dynamic_human:
            self.havlnce_tool = HAVLNCE(self._config.TASK_CONFIG, self._sim)
            self.havlnce_tool._reset_signal_queue_and_counters()
    
    def step(self, action):
        if self.use_dynamic_human:
            # Synchronize with dynamic human timeline
            self.havlnce_tool._handle_signals()
        
        return super().step(action)
```

## 2. Collecting Action Sequences

During inference on the test split, you need to collect action sequences for each episode. Here's a template for integrating action collection into your evaluation loop:

### 2.1 Basic Action Collection

```python
import json
from typing import List, Dict, Any

class ActionCollector:
    def __init__(self):
        self.episodes_data = []
    
    def start_episode(self, episode_id: str, trajectory_id: str, scene_id: str):
        """Start recording a new episode"""
        self.current_episode = {
            "episode_id": episode_id,
            "trajectory_id": trajectory_id,
            "scene_id": scene_id,
            "actions": []
        }
    
    def record_action(self, action: int):
        """Record an action for the current episode"""
        self.current_episode["actions"].append(action)
    
    def end_episode(self):
        """Finish recording the current episode"""
        if self.current_episode:
            self.episodes_data.append(self.current_episode)
            self.current_episode = None
    
    def save_submission(self, output_path: str, agent_name: str):
        """Save collected actions as a submission file"""
        submission = {
            "episodes": self.episodes_data,
            "metadata": {
                "agent_name": agent_name,
                "timestamp": datetime.now().isoformat(),
                "split": "test"
            }
        }
        
        with open(output_path, 'w') as f:
            json.dump(submission, f, indent=2)
```

### 2.2 Integration with Evaluation Loop

```python
from challenge.integration_guide import ActionCollector

# Initialize collector
collector = ActionCollector()

# In your evaluation loop
for episode in test_episodes:
    # Start recording
    collector.start_episode(
        episode_id=episode.episode_id,
        trajectory_id=episode.trajectory_id,
        scene_id=episode.scene_id
    )
    
    # Reset environment
    observations = env.reset()
    
    # Run episode
    while not env.episode_over:
        # Get action from your agent
        action = your_agent.act(observations)
        
        # Record action
        collector.record_action(action)
        
        # Step environment
        observations, reward, done, info = env.step(action)
    
    # Finish recording
    collector.end_episode()

# Save submission
collector.save_submission("my_submission.json", "YourAgentName")
```

## 3. Action Space

HA-VLN uses the following action codes:

| Code | Action | Description |
|------|--------|-------------|
| 0 | STOP | End the episode |
| 1 | MOVE_FORWARD | Move forward 0.25 meters |
| 2 | TURN_LEFT | Turn left 15 degrees |
| 3 | TURN_RIGHT | Turn right 15 degrees |

**Important Notes:**
- Each episode must end with a STOP action (code 0)
- Action sequences must be 1-500 actions long
- Invalid actions will cause submission validation to fail

## 4. Testing Your Integration

### 4.1 Validation Split Testing
Before generating test submissions, test your integration on validation splits:

```python
# Test on val_unseen split
test_agent_on_split("val_unseen", collector)
collector.save_submission("val_unseen_test.json", "YourAgentName")
```

### 4.2 Action Sequence Validation
Check that your action sequences meet the requirements:

```python
def validate_episode_actions(actions: List[int]) -> bool:
    """Validate an episode's action sequence"""
    if not actions:
        return False
    
    if len(actions) > 500:
        return False
    
    if actions[-1] != 0:  # Must end with STOP
        return False
    
    valid_actions = {0, 1, 2, 3}
    for action in actions:
        if action not in valid_actions:
            return False
    
    return True
```

## 5. Generating Final Submission

### 5.1 Test Split Inference
Run inference on the test split (`Data/HA-R2R/test/test.json.gz`):

```bash
# Example command structure (adapt to your agent)
python your_agent_script.py \
    --test_split test \
    --output submission.json \
    --agent_name YourAgentName
```

### 5.2 Submission File Structure
Your submission file should have this structure:

```json
{
  "episodes": [
    {
      "episode_id": "0",
      "trajectory_id": "5732",
      "scene_id": "mp3d/5ZKStnWn8Zo/5ZKStnWn8Zo.glb",
      "actions": [1, 1, 2, 1, 0]
    },
    // ... more episodes
  ],
  "metadata": {
    "agent_name": "YourAgentName",
    "timestamp": "2026-04-01T12:00:00Z",
    "split": "test"
  }
}
```

## 6. Common Integration Issues

### 6.1 Vocabulary Expansion
The HA-R2R dataset provides pre-processed BERT tokenized versions (`*_bertidx.json.gz`) for all splits. If your agent uses BERT-based text encoding, you can use these pre-processed files directly.

### 6.2 Dynamic Human Synchronization
The HA-VLN environment uses a background thread that sends refresh signals every 0.1 seconds. The environment wrapper automatically handles synchronization, so your agent does not need to match this frequency explicitly.

### 6.3 Collision Handling
The HA-VLN environment includes dynamic human obstacles. Test your agent's collision avoidance in human-populated scenes.

## 7. Next Steps

1. **Test Integration**: Run your agent on validation splits to ensure proper integration
2. **Generate Submission**: Run inference on the test split and generate submission file
3. **Validate Format**: Use the provided validation tools to check your submission
4. **Submit**: Follow the challenge submission instructions

## 8. Optional: Baseline Models

If you want to run the provided baseline models for reference:

```bash
cd agent
# Run HA-VLN-CMA baseline inference
python run.py --exp-config config/cma_pm_da_aug_tune.yaml --run-type inference
```

**Note**: The challenge encourages developing your own agent. Baseline results are provided for reference only.

## Support

For integration issues:
- Review the [HA-VLN API documentation](../api/)
- Check the [submission format specification](submission_format.md)
- Refer to the [HA-R2R dataset documentation](https://github.com/F1y1113/HA-VLN/tree/main/Data/HA-R2R)

Remember to test your integration thoroughly before generating final submissions.