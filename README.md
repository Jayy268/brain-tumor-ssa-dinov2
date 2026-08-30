# Toward Edge-Deployable Brain Tumour Detection in Sub-Saharan Africa

Code for the paper *"Toward Edge-Deployable Brain Tumour Detection in Sub-Saharan Africa: A Self-Supervised Vision Transformer with Dynamic Quantisation."*

This repository contains the full experimental pipeline: 2D slice extraction from the BraTS-Africa NIfTI volumes, multi-seed training of a frozen DINOv2 ViT-S/14 classifier, and the INT8 dynamic quantisation experiment.

## Overview

The work replaces the supervised MobileNetV2 backbone of a prior brain-tumour screening baseline with a frozen self-supervised DINOv2 ViT-S/14, evaluates it across four random seeds, and measures both sensitivity (on BraTS-Africa) and specificity (on the Nigerian Brain Dataset). INT8 dynamic quantisation is then applied to compress the model toward an edge-deployable footprint while preserving its clinical metrics.

## Repository structure

```
notebooks/
  00_brats_3d_to_2d_extraction.ipynb   Extract 2D axial T1c slices from BraTS-Africa NIfTI volumes
  01_training_multiseed.ipynb          Train the DINOv2 classifier (run once per seed)
  02_quantisation_experiment.ipynb     Apply and evaluate INT8 dynamic quantisation
```

## Data

The pipeline uses three sources, none of which are redistributed here:

- **Brain Tumor MRI Dataset** (western training data) — available on Kaggle.
- **BraTS-Africa** (external sensitivity) — available via The Cancer Imaging Archive (TCIA).
- **Nigerian Brain Dataset** (external specificity) — available on brainlife.io.

Download these separately and set the paths in each notebook's **Configuration** cell.

## Requirements

- Python 3.10+
- PyTorch, torchvision
- numpy, opencv-python, matplotlib
- nibabel (slice extraction only)

DINOv2 backbone weights are pulled via `torch.hub` on first run, or can be loaded from a local `.pth` as configured.

## Usage

1. **Extract slices** — run `00_brats_3d_to_2d_extraction.ipynb`, pointing `DATA_ROOT` at the raw BraTS-Africa NIfTI directory.
2. **Train** — run `01_training_multiseed.ipynb` once for each seed in `{12, 23, 42, 2026}`, editing `SEED` in the configuration cell each time.
3. **Quantise** — run `02_quantisation_experiment.ipynb` against the seed-2026 checkpoint.

Each notebook begins with a configuration cell; set the dataset and model paths there before running.

## Notes

- All benchmarking in the quantisation notebook runs on CPU (single-threaded) for parity between the FP32 and INT8 models, since PyTorch dynamic quantisation is CPU-only.
- The BraTS-Africa split is performed at the patient level to prevent leakage; the split is deterministic per seed.

## Citation

If you use this code, please cite the paper (details to be added on publication).

## License

Released under the MIT License. See `LICENSE`.
