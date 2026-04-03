# Quick Start

The original repositories require Python 3.7 and CUDA 11.1. The following steps address compatibility issues when running on modern Linux distributions like Ubuntu 22.04 or 24.04.

## 1. Install System Dependencies

Install the required graphics and encryption libraries. This replaces the deprecated `libgl1-mesa-glx` with `libgl1` and adds `libcrypt-dev` for `lmdb` compilation.

```bash
sudo apt-get update
sudo apt-get install -y --no-install-recommends libjpeg-dev libglm-dev libgl1 libegl1-mesa-dev mesa-utils xorg-dev freeglut3-dev libcrypt-dev unzip
```

## 2. Clone the Repository & Create the Environment

Clone the HA-VLN repository and create a Conda environment. This setup includes GCC 9 to ensure compatibility with the project's requirements.

```bash
git clone https://github.com/F1y1113/HA-VLN.git
cd HA-VLN

# Create environment with specific compiler and Python versions
conda create -n havlnce python=3.7 gcc_linux-64=9 gxx_linux-64=9 cudatoolkit=11.1 -c conda-forge -y
conda activate havlnce
```

## 3. Setup PyTorch & CUDA Toolkit

Install the full CUDA development toolkit to obtain the `nvcc` compiler required for custom extensions.

```bash
# Install CUDA toolkit development tools
conda install -c conda-forge cudatoolkit-dev=11.1.1 -y

# Set CUDA_HOME to the conda environment path
export CUDA_HOME=$CONDA_PREFIX

# Install PyTorch
pip install torch==1.9.1+cu111 torchvision==0.10.1+cu111 -f https://download.pytorch.org/whl/torch_stable.html
```

## 4. Install Habitat-Sim and Habitat-Lab

Install the pre-compiled binary for `habitat-sim`. For `habitat-lab`, `msgpack` is installed first to avoid build errors.

```bash
# 1. Install Habitat-Sim (Headless)
conda install -c aihabitat -c conda-forge habitat-sim=0.1.7 headless -y

# 2. Clone and Install Habitat-Lab
git clone --branch v0.1.7 https://github.com/facebookresearch/habitat-lab.git
cd habitat-lab
pip install msgpack
pip install -r requirements.txt
pip install -r habitat_baselines/rl/requirements.txt
conda install -c conda-forge libxcrypt -y
python setup.py develop --all
cd ..
```

## 5. Install GroundingDINO

This step involves compiling older CUDA code. It requires specific versions of Python packages and a modification to a Conda system header to prevent compilation errors.

```bash
cd HASimulator
git clone https://github.com/IDEA-Research/GroundingDINO.git
cd GroundingDINO/

# 1. Install specific dependency versions
pip install "safetensors==0.3.1" "huggingface-hub==0.16.4" "tokenizers==0.13.3" "transformers==4.30.2" "timm==0.9.2"

# 2. Update supervision version
sed -i 's/supervision.*/supervision==0.11.1/g' requirements.txt

# 3. Patch types.h in the conda sysroot to resolve __int128 errors
SYSROOT_TYPES="$CONDA_PREFIX/x86_64-conda-linux-gnu/sysroot/usr/include/linux/types.h"
chmod +w $SYSROOT_TYPES
sed -i 's/typedef __signed__ __int128/\/\/ typedef __signed__ __int128/g' $SYSROOT_TYPES
sed -i 's/typedef unsigned __int128/\/\/ typedef unsigned __int128/g' $SYSROOT_TYPES

# 4. Install ninja and compile GroundingDINO
pip install ninja
pip install -e .

# 5. Download weights
mkdir weights
cd weights
wget -q https://github.com/IDEA-Research/GroundingDINO/releases/download/v0.1.0-alpha/groundingdino_swint_ogc.pth
cd ../../../
```

## 6. Install Agent Requirements

```bash
pip install -r agent/VLN-CE/requirements.txt
pip install setuptools==59.5.0
pip install webdataset==0.1.40
```

## 7. Download Dataset

```bash
# 1. Download and extract Matterport3D Dataset 
# Requires download_mp.py from official Matterport3D sources
python3 download_mp.py -o Data/scene_datasets --task habitat
unzip Data/scene_datasets/v1/tasks/mp3d_habitat.zip -d Data/scene_datasets/

# 2. Download and extract HA-R2R and HAPS 2.0 datasets
bash scripts/download_data.sh

# 3. Download and extract pre-trained DD-PPO model weights
wget https://dl.fbaipublicfiles.com/habitat/data/baselines/v1/ddppo/ddppo-models.zip
unzip ddppo-models.zip -d Data/ddppo-models/
```

## 8. Human-Scene Fusion

Run the following script to generate multi-view human annotation videos. The output path can be modified in `scripts/human_scene_fusion.py`.

```bash
cd scripts
python3 human_scene_fusion.py
```

## 9. Manual Navigation

Navigate through a scene using the keyboard. Update the `DATA_PATH` in `scripts/demo.py` (Line 15) to your local project path before running.

```bash
cd scripts
python demo.py --scan 1LXtFkjw3qL
```

## 10. Real-time Human Rendering

Human rendering is implemented in the `HAVLNCE` class within `HASimulator/enviorments.py`. It uses a background thread for timing and the main thread for managing human models and navmesh updates.

The navmesh is calculated during the first use and saved for subsequent sessions. To enable this, update the HAVLN-CE task configuration:

```yaml
SIMULATOR:
  ADD_HUMAN: True
  HUMAN_GLB_PATH: ../Data/HAPS2_0
  HUMAN_INFO_PATH: ../Data/Multi-Human-Annotations/human_motion.json
  RECOMPUTE_NAVMESH_PATH: ../Data/recompute_navmesh
```

## 11. Training, Evaluation and Inference

Use the following commands to run the HA-VLN-CMA agent:

```bash
cd agent
# Training
python run.py --exp-config config/cma_pm_da_aug_tune.yaml --run-type train

# Evaluation
python run.py --exp-config config/cma_pm_da_aug_tune.yaml --run-type eval

# Inference
python run.py --exp-config config/cma_pm_da_aug_tune.yaml --run-type inference
```
