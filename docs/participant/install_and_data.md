# Install HASimulator and Get Public Data

This page is for participants who are setting up HA-VLN for the first time.

Your goal in this step is simple:

- install the HA-VLN runtime and `HASimulator`
- obtain the public data needed for development
- verify that the environment can run before you start writing your own agent

## What You Need

To develop an HA-VLN agent, you need:

- the HA-VLN repository
- the simulator stack used by HA-VLN
- the public dataset under `Data/`
- an available Python environment

For installation details, use these reference pages:

- [Dependencies](../quick_start/dependencies.md)
- [Installation Steps](../quick_start/installation.md)
- [Data Download](../quick_start/data.md)

These pages already use the Python 3.8 based participant setup path.

## Recommended Setup Order

### 1. Clone the repository

```bash
git clone https://github.com/F1y1113/HA-VLN.git
cd HA-VLN
```

### 2. Prepare the Python environment

Follow [Dependencies](../quick_start/dependencies.md) and [Installation Steps](../quick_start/installation.md) to install the required system and Python packages.

### 3. Install the simulator stack

The installation flow includes the HA-VLN runtime together with Habitat-Sim and Habitat-Lab. Follow:

- [Installation Steps](../quick_start/installation.md)

### 4. Prepare the public data under `Data/`

Participants should place the public HA-VLN data under the repository `Data/` directory. The full download sources, Matterport3D access note, extraction commands, and final layout are described in:

- [Data Download](../quick_start/data.md)

At minimum, your local setup should include the required Matterport3D Habitat assets, the public HA-R2R data, and the HAPS 2.0 human assets.

### 5. Verify the environment before agent development

Before writing or adapting your own agent, confirm that the environment imports correctly and that the required data paths exist.

Minimal checks:

```bash
python -c "import torch; print(torch.__version__)"
python -c "import habitat_sim; print('habitat-sim OK')"
```

## What You Should Have at the End of This Step

When this step is complete, you should have:

- a working HA-VLN repository checkout
- a runnable simulator environment
- the public dataset prepared under `Data/`
- enough runtime support to begin developing your own agent

## Next Step

Once installation and data preparation are finished, continue to:

- [Develop Your Agent](develop_agent.md)
