# Install HASimulator and Get Public Data

This page is for researchers and developers who are setting up HA-VLN for the first time.

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

These pages use the Python 3.8 based setup path.

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

Place the public HA-VLN data under the repository `Data/` directory. The full download sources, Matterport3D access note, extraction commands, and final layout are described in:

- [Data Download](../quick_start/data.md)

At minimum, your local setup should include the required Matterport3D Habitat assets, the public HA-R2R data, and the HAPS 2.0 human assets.

### 5. Verify the environment before agent development

Before writing or adapting your own agent, confirm both of the following:

- the Python and simulator environment imports correctly
- the HA-VLN runtime and data paths are available from the repository checkout

#### Basic environment checks

These checks only confirm that the base Python environment is usable:

```bash
python -c "import torch; print(torch.__version__)"
python -c "import habitat_sim; print('habitat-sim OK')"
```

#### HA-VLN runtime checks

Use a slightly stronger check from the repository root to confirm that the HA-VLN-specific packages and config path can be resolved through the local import-path setup used by the evaluation workflow:

```bash
python -c "import os, sys; repo = os.getcwd(); sys.path.insert(0, repo); sys.path.insert(0, os.path.join(repo, 'agent')); sys.path.insert(0, os.path.join(repo, 'agent', 'VLN-CE')); import HASimulator, habitat_extensions, vlnce_baselines; print('HA-VLN runtime OK')"
```

These checks are still lightweight. They do not prove that a full evaluation run will succeed, but they are much closer to the actual HA-VLN workflow than import-only checks for `torch` or `habitat_sim`.

#### Data-path sanity checks

Before moving on, make sure the required public paths are actually present under `Data/`.

For example, confirm that your checkout contains the expected public assets described in [Data Download](../quick_start/data.md), including:

- the HA-R2R dataset files
- the HAPS 2.0 human assets
- the required Matterport3D Habitat scene assets

## What You Should Have at the End of This Step

When this step is complete, you should have:

- a working HA-VLN repository checkout
- a runnable simulator environment
- the public dataset prepared under `Data/`
- enough runtime support to begin developing your own agent

## Next Step

Once installation and data preparation are finished, continue to:

- [Develop Your Agent](develop_agent.md)
