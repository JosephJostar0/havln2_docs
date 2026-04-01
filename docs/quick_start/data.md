## Data Download

Official HA-VLN repository: https://github.com/F1y1113/HA-VLN

This page explains common HA-VLN data asset preparation: datasets, model weights, and vocabulary expansion.

### 1. Dataset Layout

It is recommended to keep datasets under the `Data` directory with the following layout:

```text
Data/
  HA-R2R/
		train/train.json.gz
		val_seen/val_seen.json.gz
		val_unseen/val_unseen.json.gz
```

### 2. GroundingDINO Weights

If `HUMAN_COUNTING` is enabled, download the detector model weights:

```bash
cd HASimulator/GroundingDINO
mkdir -p weights
cd weights
wget -q https://github.com/IDEA-Research/GroundingDINO/releases/download/v0.1.0-alpha/groundingdino_swint_ogc.pth
```

Then configure the absolute path to this weight file in `detector.py`.

### 3. Vocabulary Expansion (for static vocab agents)

If your agent uses a static vocabulary (for example a txt file), expand it from HA-R2R corpora to avoid OOV issues:

```python
import json
import gzip
import re
from pathlib import Path

def clean_text(text: str) -> list:
    text = text.lower()
    text = re.sub(r'([.?!,;:/\\()\[\]"\'\-])', r' \1 ', text)
    text = re.sub(r'\s+', ' ', text).strip()
    return text.split()

def update_vocabulary(ha_r2r_dir: str, existing_vocab_path: str, output_vocab_path: str):
    ha_r2r_path = Path(ha_r2r_dir)

    with open(existing_vocab_path, 'r', encoding='utf-8') as f:
        existing_words = [line.strip() for line in f.readlines()]

    vocab_set = set(existing_words)
    new_words = []

    for split in ['train', 'val_seen', 'val_unseen']:
        json_file = ha_r2r_path / split / f"{split}.json.gz"
        if not json_file.exists():
            continue

        with gzip.open(json_file, 'rt', encoding='utf-8') as f:
            data = json.load(f)

        for item in data:
            for instruction in item.get('instructions', []):
                tokens = clean_text(instruction)
                for token in tokens:
                    if token not in vocab_set:
                        vocab_set.add(token)
                        new_words.append(token)

    with open(output_vocab_path, 'w', encoding='utf-8') as f:
        for word in existing_words + new_words:
            f.write(f"{word}\n")
```
