# Vacuole Segmentation with U-Net

Automatic segmentation of vacuoles in brightfield microscopy images using a U-Net trained using fluorescence-derived masks.

## Description

Vacuoles are small, circular structures visible inside cells under a brightfield microscope. Identifying their position and size manually is slow and inconsistent. This project automates the process using a deep learning model that learns to spot vacuoles in brightfield images. A mask is generated from fluorescence microscopy images of vacuoles, and is used as a ground truth to train the neural network.

## Content

The project is split in two notebooks:

**Notebook 1: Mask generation**
Processes the fluorescence images to produce binary segmentation masks. Each pixel is labelled as either vacuole or background. These masks are what the model learns to predict (ground truth).

**Notebook 2: U-Net training**
Trains a U-Net convolutional neural network to predict vacuole locations from brightfield images. 

## Data

| File type | Description |
|---|---|
| `.stk` brightfield stacks | 5-slice z-stacks; central slices are projected to 2D |
| `.tif` fluorescence masks | Binary masks generated in Notebook 1 |

For this model, I used five different images of yeast cells: 4 are used for training/validation, and one is held-out as a test set. The images are not included in this repository. If you want to use your own data, please set the paths in the configuration section of each notebook.

## Model Description

A **U-Net** with 3 encoder/decoder levels and skip connections.

- Input: 64×64 brightfield patches (normalised to [0, 1])
- Output: per-pixel probability of being a vacuole
- Loss: Dice loss + Binary Focal Cross-Entropy
- Training: 4-fold cross-validation, ~150 patches per image per fold

Patch-based training is used instead of full images because only 5 images are available. Extracting many small patches from each image multiplies the dataset size and also provides augmentation (as the patches are chosen randomly).

## Results

The model is evaluated on the held-out test image using the **Dice coefficient** as an accuracy metric. 

Output files are saved to `unet_results_nobs/`:

| File | Description |
|---|---|
| `best_model.keras` | Best model checkpoint across all folds |
| `training_curves.png` | Loss and Dice per fold |
| `evaluation_metrics.png` | Dice curve, ROC curve, overlay at best threshold |
| `all_predictions.png` | Side-by-side predictions for all 5 images |
| `test_pred_mask.tif` | Binary prediction mask for the test image |

## Setup

```bash
pip install tensorflow scikit-image tifffile scikit-learn matplotlib
```

Run the notebooks

```
01_mask_generation.ipynb
02_unet_patch_training.ipynb
```

## Project structure

```
├── images/                  # Brightfield .stk stacks (raw data not uploaded)
├── masks/                   # Fluorescence-derived binary masks (raw data not uploaded, generate using the first notebook)
├── unet_results_nobs/       # Model outputs and figures
├── 01_mask_generation.ipynb
└── 02_unet_patch_training.ipynb
```
