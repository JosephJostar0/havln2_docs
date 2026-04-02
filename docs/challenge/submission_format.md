# HA-VLN Challenge: Submission Format Specification

This document specifies the exact format required for HA-VLN challenge submissions. All submissions must follow this specification to be accepted for evaluation.

## Overview

Participants must submit **action sequence** files in JSON format. The organizers will replay these action sequences in the private HA-VLN simulator to compute all evaluation metrics.

## File Format

- **Format**: JSON
- **Encoding**: UTF-8
- **File extension**: `.json`

## JSON Structure

### Complete Example

```json
{
  "episodes": [
    {
      "episode_id": "0",
      "trajectory_id": "5732",
      "scene_id": "mp3d/5ZKStnWn8Zo/5ZKStnWn8Zo.glb",
      "actions": [1, 1, 2, 1, 0]
    },
    {
      "episode_id": "1",
      "trajectory_id": "1234",
      "scene_id": "mp3d/GLvvgkT4dwJ/GLvvgkT4dwJ.glb",
      "actions": [3, 1, 0]
    }
  ],
  "metadata": {
    "agent_name": "MyAgent",
    "timestamp": "2026-04-01T12:00:00Z",
    "split": "test"
  }
}
```

### Minimal Example (Required Fields Only)

```json
{
  "episodes": [
    {
      "episode_id": "0",
      "trajectory_id": "5732",
      "scene_id": "mp3d/5ZKStnWn8Zo/5ZKStnWn8Zo.glb",
      "actions": [1, 1, 0]
    }
  ]
}
```

## Field Specifications

### Required Fields for Each Episode

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `episode_id` | string | Required | Unique episode identifier. Must match `episode_id` in `test.json.gz`. |
| `trajectory_id` | string | Required | Unique trajectory identifier. Must match `trajectory_id` in `test.json.gz`. |
| `scene_id` | string | Required | Scene identifier. Must match `scene_id` in `test.json.gz`. Format: `mp3d/{scan_id}/{scan_id}.glb`. |
| `actions` | array[int] | Required | Action sequence. Length must be 1-500 inclusive. |

### Action Codes

HA-VLN uses a discrete action space with the following codes:

| Code | Action Name | Description |
|------|-------------|-------------|
| 0 | STOP | Stop navigation and end the episode. |
| 1 | MOVE_FORWARD | Move forward 0.25 meters. |
| 2 | TURN_LEFT | Turn left 15 degrees (counter-clockwise). |
| 3 | TURN_RIGHT | Turn right 15 degrees (clockwise). |

### Metadata Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `agent_name` | string | Recommended | Your method/model name. |
| `timestamp` | string | Recommended | Generation time in ISO 8601 format (e.g., "2026-04-01T12:00:00Z"). |
| `split` | string | Recommended | Always "test" for challenge submissions. |

### Optional Trajectory Field

You may optionally include trajectory information for debugging and visualization:

```json
{
  "episode_id": "0",
  "trajectory_id": "5732",
  "scene_id": "mp3d/5ZKStnWn8Zo/5ZKStnWn8Zo.glb",
  "actions": [1, 1, 0],
  "trajectory": [
    {
      "position": [6.307, 0.121, 0.185],
      "heading": -0.259,
      "stop": false
    },
    {
      "position": [6.307, 0.121, 0.407],
      "heading": -0.259,
      "stop": false
    },
    {
      "position": [6.307, 0.121, 0.629],
      "heading": -0.259,
      "stop": true
    }
  ]
}
```

**Note**: Trajectory information is for visualization only and does not affect scoring.

## Validation Rules

### 1. Structural Validation
- ✓ File must be valid JSON
- ✓ Root element must contain `episodes` field (array)
- ✓ Each episode must contain all required fields

### 2. Field Validation
- ✓ `episode_id`, `trajectory_id`, `scene_id` must be strings
- ✓ `actions` must be an integer array
- ✓ Each action must be in {0, 1, 2, 3}
- ✓ Action sequence length: 1 ≤ length ≤ 500

### 3. Data Integrity
- ✓ All episodes from the test set must be included (3408 episodes)
- ✓ No duplicate `episode_id` values
- ✓ Episode IDs must match those in `Data/HA-R2R/test/test.json.gz`

## How to Generate Valid Submissions

### 1. Collect Actions During Inference

During inference on the test split, record the action sequence for each episode:

```python
import json

class SubmissionGenerator:
    def __init__(self):
        self.episodes = []
    
    def add_episode(self, episode_id, trajectory_id, scene_id, actions):
        episode = {
            "episode_id": str(episode_id),
            "trajectory_id": str(trajectory_id),
            "scene_id": scene_id,
            "actions": actions
        }
        self.episodes.append(episode)
    
    def save(self, output_path, agent_name="MyAgent"):
        submission = {
            "episodes": self.episodes,
            "metadata": {
                "agent_name": agent_name,
                "timestamp": datetime.now().isoformat(),
                "split": "test"
            }
        }
        
        with open(output_path, 'w') as f:
            json.dump(submission, f, indent=2)
```

### 2. Integration Example

```python
from datetime import datetime
import json

# Initialize generator
generator = SubmissionGenerator()

# In your evaluation loop
for episode in test_episodes:
    actions = []
    
    # Run episode
    env.reset()
    while not env.episode_over:
        # Get action from your agent
        action = agent.act(observations)
        actions.append(action)
        
        # Step environment
        observations = env.step(action)
    
    # Add to submission
    generator.add_episode(
        episode_id=episode.episode_id,
        trajectory_id=episode.trajectory_id,
        scene_id=episode.scene_id,
        actions=actions
    )

# Save submission
generator.save("my_submission.json", "YourAgentName")
```

### 3. Verify Your Submission

Before submitting, verify that your file:
1. Contains exactly 3408 episodes
2. All action sequences are valid
3. File size is reasonable (typically 1-10 MB)

## Common Issues and Solutions

### Issue 1: Missing STOP Action
**Problem**: Episode doesn't end with action code 0.
**Solution**: Ensure your agent outputs STOP (0) when the episode should end.

### Issue 2: Action Sequence Too Long
**Problem**: More than 500 actions in an episode.
**Solution**: Implement a step limit in your agent (e.g., force STOP after 500 steps).

### Issue 3: Invalid Action Codes
**Problem**: Action codes outside {0, 1, 2, 3}.
**Solution**: Map your agent's actions to the valid codes above.

### Issue 4: Missing Episodes
**Problem**: Not all 3408 test episodes are included.
**Solution**: Ensure your inference loop processes the entire test set.

## Submission Checklist

Before submitting, verify:

- [ ] File is valid JSON
- [ ] Contains `episodes` array
- [ ] Each episode has: `episode_id`, `trajectory_id`, `scene_id`, `actions`
- [ ] All actions are in {0, 1, 2, 3}
- [ ] Action sequences are 1-500 actions long
- [ ] Contains all 3408 test episodes
- [ ] No duplicate `episode_id` values
- [ ] (Optional) Metadata includes `agent_name`

## Test Data Reference

The test split is available at:
- `Data/HA-R2R/test/test.json.gz` - Contains episode IDs, scene IDs, and instructions
- `Data/HA-R2R/test/test_bertidx.json.gz` - BERT tokenized version (optional)

**Important**: The test split does not contain goal positions or reference paths. These are kept private for evaluation.

## Related Documentation

- [Agent Integration Guide](integration_guide.md) - How to integrate your agent with HA-VLN
- [HA-R2R Dataset](https://github.com/F1y1113/HA-VLN/tree/main/Data/HA-R2R) - Dataset details and examples
- [HA-VLN API Documentation](../api/) - Environment APIs and interfaces

## Support

If you encounter issues with submission format:
1. Review this specification carefully
2. Check the example submissions in `challenge/examples/`
3. Ensure your JSON is valid using a JSON validator
4. Compare your submission structure with the examples provided

Remember: Proper formatting is essential for your submission to be accepted and evaluated.