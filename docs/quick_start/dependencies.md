## Dependencies

Official HA-VLN repository: https://github.com/F1y1113/HA-VLN

This page mainly helps users on newer Linux distributions (for example Ubuntu 22.04/24.04 and WSL) adapt HA-VLN dependencies in a stable and reproducible way.

If your Linux environment is already close to the original supported stack (older distro / toolchain), installing dependencies directly from the official repository instructions is also fine.

### System Packages

Install the common HA-VLN system dependencies first (graphics, build tools, and crypto-related libraries):

```bash
sudo apt-get update
sudo apt-get install -y --no-install-recommends \
	libjpeg-dev libglm-dev libgl1 libegl1-mesa-dev mesa-utils \
	xorg-dev freeglut3-dev libcrypt-dev
```

### Python Environment

Use conda to create and manage the environment:

```bash
conda create -n havlnce python=3.7 gcc_linux-64=9 gxx_linux-64=9 cudatoolkit=11.1 -c conda-forge -y
conda activate havlnce
```

### Core Python Packages (Training Runtime)

The following dependencies are required for HA-VLN training/evaluation:

```bash
# Full CUDA toolkit (for nvcc)
conda install -c conda-forge cudatoolkit-dev=11.1.1 -y
export CUDA_HOME=$CONDA_PREFIX

# PyTorch (CUDA 11.1)
pip install torch==1.9.1+cu111 torchvision==0.10.1+cu111 \
	-f https://download.pytorch.org/whl/torch_stable.html
```
