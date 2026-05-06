# Data Download

Official HA-VLN repository: https://github.com/F1y1113/HA-VLN

This page explains where to download the required data, which parts require separate access, and how to place the downloaded files into the repository `Data/` directory.

## What You Need to Download

For a usable HA-VLN setup, you typically need four data groups:

1. Matterport3D scene assets used by Habitat and the simulator
2. the public HA-R2R dataset
3. the public HAPS 2.0 human model dataset
4. optional DD-PPO baseline weights

## 1. Matterport3D Scene Assets

### Access Requirement

Matterport3D is not distributed directly by this repository. You must first obtain access from the official Matterport3D dataset source.

Reference page:

- https://niessner.github.io/Matterport/

The repository includes `download_mp.py`, but that script is only useful after you have obtained the required access and credentials according to the Matterport3D procedure mentioned in the original repository README.

### Download Commands Mentioned by the Repository

The original repository materials mention two common ways to use `download_mp.py`.

README example:

```bash
python2 download_mp.py -o Data/scene_datasets --type matterport_mesh house_segmentations region_segmentations poisson_meshes
```

Habitat task-data example:

```bash
python3 download_mp.py -o Data/scene_datasets --task habitat
unzip Data/scene_datasets/v1/tasks/mp3d_habitat.zip -d Data/scene_datasets/
```

### Recommended Guidance

For the current public workflow, use the Habitat task-data path:

```bash
python3 download_mp.py -o Data/scene_datasets --task habitat
unzip Data/scene_datasets/v1/tasks/mp3d_habitat.zip -d Data/scene_datasets/
```

This prepares Habitat-compatible scene assets under `Data/scene_datasets/`.

## 2. HA-R2R and HAPS 2.0

The public HA-VLN datasets are distributed separately from Matterport3D.

The original README points to these public dataset entries:

- Hugging Face dataset: `https://huggingface.co/datasets/fly1113/HA-VLN`
- Google Drive dataset folder: `https://drive.google.com/drive/folders/1WrdsRSPp-xJkImZ3CnI7Ho90lnhzp5GR?usp=sharing`

The repository also provides a helper script that downloads the public HA-R2R and HAPS 2.0 archives from Google Drive and extracts them into the expected layout.

### Recommended Command

```bash
bash scripts/download_data.sh
```

### What the Script Does

`scripts/download_data.sh` will:

- install `gdown` if needed
- download the public HAPS 2.0 archive into `Data/HAPS2_0.zip`
- extract it into `Data/HAPS2_0/`
- flatten the nested `human_motion_glbs_v3/` directory so the final human model folders sit directly under `Data/HAPS2_0/`
- download the public HA-R2R archive into `Data/HAR2R-CE.zip`
- extract it into `Data/HA-R2R/`

This means you normally do not need to manually reorganize those two datasets if you use the provided script.

## 3. Optional DD-PPO Baseline Weights

If you want to run the baseline depth encoder weights referenced by the original repository, download the DD-PPO models from:

- `https://dl.fbaipublicfiles.com/habitat/data/baselines/v1/ddppo/ddppo-models.zip`

Then extract them carefully to avoid an extra nested directory level:

```bash
wget --no-check-certificate https://dl.fbaipublicfiles.com/habitat/data/baselines/v1/ddppo/ddppo-models.zip
unzip -j ddppo-models.zip "data/ddppo-models/*" -d Data/ddppo-models/
rm ddppo-models.zip
```

## Recommended Final Layout

After the required public data is prepared, a practical layout is:

```text
Data/
  HA-R2R/
    train/
    val_seen/
    val_unseen/
  HAPS2_0/
  Multi-Human-Annotations/
    human_motion.json
  scene_datasets/
  ddppo-models/
  recompute_navmesh/
```

`Data/Multi-Human-Annotations/human_motion.json` is referenced directly by the HA-VLN task configs, and the runtime may create cached navmesh files under `Data/recompute_navmesh`.

## Important Placement Notes

- keep the Matterport3D Habitat assets under `Data/scene_datasets/`
- keep HA-R2R under `Data/HA-R2R/`
- keep HAPS 2.0 human assets under `Data/HAPS2_0/`
- if you use DD-PPO baseline weights, place them under `Data/ddppo-models/`
- keep `Data/Multi-Human-Annotations/human_motion.json` available because the HA-VLN task configs reference it directly
- allow `Data/recompute_navmesh/` to be created or updated during runtime
- do not leave HAPS 2.0 nested under `Data/HAPS2_0/human_motion_glbs_v3/`; the provided script already flattens this for you

## Provided Text Resources

The public HA-R2R data already provides text resources that many agents can use directly.

### 1. Built-in Instruction Vocabulary

The main HA-R2R dataset files such as `Data/HA-R2R/train/train.json.gz` include an `instruction_vocab.word_list` field.

This means classic word-vocabulary pipelines do not need to rebuild a vocabulary from scratch before getting started.

### 2. Pre-tokenized BERT Inputs

The public splits also include pre-tokenized BERT-format files:

- `Data/HA-R2R/train/train_bertidx.json.gz`
- `Data/HA-R2R/val_seen/val_seen_bertidx.json.gz`
- `Data/HA-R2R/val_unseen/val_unseen_bertidx.json.gz`

If your method uses a BERT-style text encoder, these files are the first thing to check before doing any custom preprocessing.

## Recommended Guidance

- if your method uses a classic word-level vocabulary, first try the vocabulary already bundled in the HA-R2R dataset files
- if your method uses BERT-style text encoding, first try the provided `*_bertidx.json.gz` files
- only build or extend your own vocabulary if your method truly requires a different text pipeline

## GroundingDINO Weights

If `HUMAN_COUNTING` is enabled, download the detector model weights:

```bash
cd HASimulator/GroundingDINO
mkdir -p weights
cd weights
wget -q https://github.com/IDEA-Research/GroundingDINO/releases/download/v0.1.0-alpha/groundingdino_swint_ogc.pth
```

Then configure the absolute path to this weight file in `detector.py`.
