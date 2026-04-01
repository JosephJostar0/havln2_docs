## Installation Steps

Official HA-VLN repository: https://github.com/F1y1113/HA-VLN

The following installation flow is ordered for practical HA-VLN runtime setup.

### 1. Clone Repository

```bash
git clone https://github.com/F1y1113/HA-VLN.git
cd HA-VLN
```

### 2. Create Legacy-Compatible Runtime

```bash
conda create -n havlnce python=3.7 gcc_linux-64=9 gxx_linux-64=9 cudatoolkit=11.1 -c conda-forge -y
conda activate havlnce
```

### 3. Install Habitat-Sim and Habitat-Lab

```bash
conda install -c aihabitat -c conda-forge habitat-sim=0.1.7 headless -y

git clone --branch v0.1.7 https://github.com/facebookresearch/habitat-lab.git
cd habitat-lab
pip install msgpack
pip install -r requirements.txt
pip install -r habitat_baselines/rl/requirements.txt
python setup.py develop --all
cd ..
```

### 4. Install GroundingDINO

```bash
cd HASimulator
git clone https://github.com/IDEA-Research/GroundingDINO.git
cd GroundingDINO

pip install "safetensors==0.3.1" "huggingface-hub==0.16.4" \
	"tokenizers==0.13.3" "transformers==4.30.2" "timm==0.9.2"

sed -i 's/supervision.*/supervision==0.11.1/g' requirements.txt

SYSROOT_TYPES="$CONDA_PREFIX/x86_64-conda-linux-gnu/sysroot/usr/include/linux/types.h"
chmod +w "$SYSROOT_TYPES"
sed -i 's/typedef __signed__ __int128/\/\/ typedef __signed__ __int128/g' "$SYSROOT_TYPES"
sed -i 's/typedef unsigned __int128/\/\/ typedef unsigned __int128/g' "$SYSROOT_TYPES"

pip install ninja
pip install -e .
cd ../..
```

### 5. Verify Runtime

At minimum, verify the following two checks:

```bash
python -c "import torch; print(torch.__version__)"
python -c "import habitat_sim; print('habitat-sim OK')"
```
