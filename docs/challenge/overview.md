# HA-VLN Challenge: Overview

The Human-Aware Vision-and-Language Navigation (HA-VLN) Challenge evaluates agents' ability to navigate in dynamic human-populated environments while following natural language instructions.

## Challenge Objective

The goal is to develop agents that can:
1. **Navigate** from a starting position to a goal location in indoor environments
2. **Follow** natural language instructions that describe both the path and human activities
3. **Avoid** collisions with dynamic human obstacles
4. **Demonstrate** socially-aware navigation behaviors

## Key Features

### 1. Dynamic Human Activities
- Real-time rendering of human motions and interactions
- Humans perform various activities (walking, talking, reading, etc.)
- Human positions and activities change over time

### 2. Complex Instructions
- Natural language descriptions incorporating human behaviors
- Examples: "pass by a person talking on the phone", "avoid disturbing the couple having dinner"
- Instructions reference both static landmarks and dynamic human activities

### 3. Human-Aware Evaluation
- Metrics that account for collision avoidance
- Success requires both reaching the goal and avoiding human collisions
- Emphasis on socially appropriate navigation

## Challenge Structure

### 1. Development Phase
- **Training data**: `Data/HA-R2R/train/` (with human activity descriptions)
- **Validation data**: `Data/HA-R2R/val_seen/` and `Data/HA-R2R/val_unseen/`
- **Purpose**: Train and validate your agent models

### 2. Submission Phase
- **Test data**: `Data/HA-R2R/test/` (instructions only, no goals)
- **Task**: Generate action sequences for all test episodes
- **Output**: JSON file with action sequences (see [Submission Format](submission_format.md))

### 3. Evaluation Phase
- **Private evaluation**: Organizers replay actions in private simulator
- **Metrics**: Compute SR, TCR, NE, CR (see Evaluation Metrics section)
- **Leaderboard**: Rank based on evaluation results

## Evaluation Metrics

The challenge uses four core metrics:

### 1. Success Rate (SR)
- **Definition**: Percentage of episodes completed successfully
- **Success criteria**: Agent reaches goal AND avoids all human collisions
- **Importance**: Primary ranking metric

### 2. Trajectory Collision Rate (TCR)
- **Definition**: Average collisions in human-occupied zones
- **Calculation**: Counts collisions with dynamic humans
- **Importance**: Secondary ranking metric (lower is better)

### 3. Navigation Error (NE)
- **Definition**: Mean distance between agent's final position and goal
- **Units**: Meters
- **Importance**: Measures navigation accuracy

### 4. Collision Rate (CR)
- **Definition**: Percentage of episodes with at least one collision
- **Calculation**: Binary indicator per episode
- **Importance**: Measures collision frequency

**Ranking Priority**: SR (primary) → TCR (secondary) → NE (tertiary)

## Dataset: HA-R2R

The Human-Aware Room-to-Room (HA-R2R) dataset extends the classic R2R dataset with:

### Key Enhancements
1. **Human activity descriptions** in navigation instructions
2. **Dynamic human models** in the environment
3. **Social interaction scenarios** (individuals, couples, groups)
4. **Agent-human interaction** considerations

### Example Instruction
> "Exit the library and turn left. As you proceed straight ahead, you will enter the bedroom, **where you can observe a person actively searching for a lost item**. Continue moving forward, **ensuring you do not disturb his search**."

### Data Splits
- **Train**: 14,039 episodes for training
- **Val Seen**: 1,021 episodes (seen environments)
- **Val Unseen**: 2,349 episodes (unseen environments)
- **Test**: 3,408 episodes (for challenge evaluation)

For detailed dataset information, see the [HA-R2R documentation](https://github.com/F1y1113/HA-VLN/tree/main/Data/HA-R2R).

## Participation Requirements

### 1. Agent Development
- Develop your own navigation agent
- Can be based on existing VLN architectures
- **World model based approaches are encouraged** - these can better handle dynamic human activities and predict future states
- Must handle dynamic human activities
- Must output valid action sequences

### 2. Submission Requirements
- JSON file following exact format specification
- Action sequences for all 3,408 test episodes
- Valid action codes: {0: STOP, 1: MOVE_FORWARD, 2: TURN_LEFT, 3: TURN_RIGHT}
- Each episode: 1-500 actions, must end with STOP

### 3. Technical Requirements
- Must run in HA-VLN environment
- Must use provided test split (no ground truth access)
- Must generate reproducible results

## Getting Started

### Quick Start Path
1. **Setup**: Follow [Installation Guide](../quick_start/installation.md)
2. **Data**: Download [HA-R2R dataset](../quick_start/data.md)
3. **Integration**: Read [Agent Integration Guide](integration_guide.md)
4. **Development**: Train on train split, validate on val splits
5. **Submission**: Generate test predictions using [Getting Started Guide](getting_started.md)

### Recommended Approach
1. Start with a working VLN agent
2. Adapt it to HA-VLN environment (dynamic humans)
3. Test on validation splits
4. Optimize for both navigation success and collision avoidance
5. Generate final submission

## Challenge Timeline

1. **Announcement**: Challenge details and timeline announced
2. **Development Period**: Participants develop and train agents
3. **Submission Window**: Open for test predictions
4. **Evaluation Period**: Organizers evaluate submissions
5. **Results Announcement**: Leaderboard published

Check challenge announcements for specific dates and deadlines.

## Resources

### Documentation
- [Agent Integration Guide](integration_guide.md) - How to integrate your agent
- [Submission Format](submission_format.md) - Detailed format specification
- [Getting Started](getting_started.md) - Step-by-step participation guide
- [HA-VLN API](../api/) - Environment APIs and interfaces

### Code and Data
- [HA-VLN GitHub Repository](https://github.com/F1y1113/HA-VLN)
- [HA-R2R Dataset](https://github.com/F1y1113/HA-VLN/tree/main/Data/HA-R2R)
- [Example Submissions](https://github.com/F1y1113/HA-VLN/tree/main/challenge/examples)
- [Baseline Agents](https://github.com/F1y1113/HA-VLN/tree/main/agent)

### Research Background
- [HA-VLN 2.0 Paper](https://arxiv.org/abs/2503.14229)
- [Project Website](https://ha-vln-project.vercel.app/)
- [Related VLN Challenges](https://github.com/F1y1113/HA-VLN#related-work)

## Support and Contact

For challenge-related questions:
- Check the [Common Issues and Solutions](submission_format.md#common-issues-and-solutions)
- Review existing documentation
- Open an issue on the GitHub repository
- Contact challenge organizers through official channels

## Important Notes

### Fair Competition
- All participants use the same test data
- No access to test ground truth
- Independent development encouraged
- Code release encouraged for top submissions

### Reproducibility
- Document your approach
- Provide training details
- Ensure results are reproducible
- Consider open-sourcing your code

### Ethical Considerations
- Focus on safe navigation around humans
- Consider social navigation aspects
- Develop agents that respect personal space
- Avoid aggressive or intrusive behaviors

## Why Participate?

### Research Value
- Advance human-aware navigation research
- Contribute to socially intelligent AI
- Address real-world navigation challenges
- Benchmark against state-of-the-art methods

### Practical Applications
- Service robots in human environments
- Assistive navigation systems
- Human-robot collaboration
- Socially-aware autonomous systems

### Community Impact
- Join a growing research community
- Share insights and techniques
- Collaborate on challenging problems
- Help shape future benchmarks

Good luck with the challenge! We look forward to seeing innovative approaches to human-aware navigation.