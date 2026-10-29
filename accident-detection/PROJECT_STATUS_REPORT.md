# Accident Detection Project Status Report

**Report date:** 2026-10-29

## Project overview

This project classifies images as `Accident` or `Non Accident`. It includes a labeled image dataset, trained MobileNetV2 and ResNet50 CNNs, classical classifiers trained on features extracted from those saved CNNs, experiment notebooks, evaluation outputs, and cleaned hospital and police-station reference datasets.

## Work completed

- Created the initial project structure and organized labeled images into training, validation, and test splits.
- Added cleaned hospital and police-station reference CSV files.
- Created notebooks for dataset validation, MobileNetV2, ResNet50, classical ML experiments, and model comparison.
- Trained and saved the accident-detection MobileNetV2 and ResNet50 models.
- Added six classical ML experiments to `notebooks/04_ml_models.ipynb`: SVM, Random Forest, and Logistic Regression on features from each of the saved CNNs.
- Used the saved CNNs as feature extractors, taking their pooled intermediate feature vectors before the final sigmoid classification layer. No ImageNet-only replacement model was used.
- Matched each CNN's 224×224 RGB input dimensions and architecture-specific Keras preprocessing. The classical experiments use the explicit mapping `Accident = 0`, `Non Accident = 1`, with Accident treated as positive.
- Fit classifiers on the training features, selected the configuration by validation Accident F1, and evaluated the experiments on the held-out test split.
- Added deterministic feature extraction, cache validation/reuse, classifier serialization, test predictions, classification reports, confusion matrices, and comparison CSV/plot outputs.
- Preserved the saved CNN models, their notebooks and result files, and the repository-level `results/metrics.csv`.

The Git history records these earlier repository milestones:

- `22b6a7a` — Initial folder structure
- `96b557c` — Image dataset added
- `2118131` — Dataset fully added
- `a377ad7` — ResNet50 model and training results added
- `414d8d3` — MobileNet added

## Dataset summary

There are **973 JPG images** in total:

| Split | Accident | Non Accident | Total |
|---|---:|---:|---:|
| Train | 369 | 422 | 791 |
| Validation | 46 | 52 | 98 |
| Test | 31 | 53 | 84 |
| **Total** | **446** | **527** | **973** |

## Classical ML experiment performance

Each test metric below is calculated on the same held-out set of 84 images, with **Accident (label 0) as the positive class**. The best configuration was selected using validation Accident F1; test metrics were not used for selection. Fit and prediction times are seconds from the run environment and may vary on other hardware.

| Experiment | Validation Accident F1 | Test accuracy | Test precision | Test recall | Test F1 | Test ROC-AUC | Fit time (s) | Test prediction (s) |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| MobileNetV2 + SVM | 0.9247 | 0.9405 | 0.9333 | 0.9032 | 0.9180 | 0.9738 | 0.46 | 0.07 |
| MobileNetV2 + Random Forest | 0.9111 | 0.9524 | 0.9655 | 0.9032 | 0.9333 | 0.9842 | 2.08 | 0.16 |
| MobileNetV2 + Logistic Regression | 0.8913 | 0.9524 | 0.9091 | 0.9677 | 0.9375 | 0.9878 | 0.23 | 0.003 |
| ResNet50 + SVM | 0.9149 | 0.9048 | 0.8966 | 0.8387 | 0.8667 | 0.9684 | 0.90 | 0.12 |
| **ResNet50 + Random Forest** | **0.9348** | **0.9762** | **0.9677** | **0.9677** | **0.9677** | **0.9890** | **2.34** | **0.13** |
| ResNet50 + Logistic Regression | 0.8913 | 0.9405 | 0.9333 | 0.9032 | 0.9180 | 0.9568 | 0.45 | 0.008 |

**Best validation-selected experiment:** ResNet50 + Random Forest (validation Accident F1 **0.9348**). On the held-out test set it achieved **97.62% accuracy**, **96.77% precision**, **96.77% recall**, **96.77% F1**, and **0.9890 ROC-AUC** for Accident.

## Existing direct-CNN test results

These are the pre-existing results recorded in the CNN-specific metrics files on the same 84 test images. The CNN notebooks use the default positive label **class 1 (`Non Accident`)**, unlike the classical results above, which explicitly use **Accident (label 0)** as positive. Therefore, their precision, recall, and F1 are **not directly comparable** to the classical Accident-positive metrics without recalculating predictions under a consistent positive-class convention.

| Direct CNN | Accuracy | Precision (Non Accident positive) | Recall (Non Accident positive) | F1 (Non Accident positive) | ROC-AUC* | Training time |
|---|---:|---:|---:|---:|---:|---:|
| MobileNetV2 | 0.9048 | 0.8947 | 0.9623 | 0.9273 | 0.9343 | 187.37 s |
| ResNet50 | 0.8333 | 0.7179 | 0.9032 | 0.8000 | 0.9464 | 577.72 s |

*The saved CNN metric files calculate ROC-AUC from the CNN positive-class score for class 1. The classical ROC-AUC values in the previous table are for Accident as the positive class. ROC-AUC is invariant to switching both labels and score orientation, but the positive class is documented to make the conventions explicit.

## Folder structure

```text
accident-detection/
├── README.md
├── PROJECT_STATUS_REPORT.md
├── datasets/
│   ├── data/
│   │   ├── train/
│   │   │   ├── Accident/
│   │   │   └── Non Accident/
│   │   ├── val/
│   │   │   ├── Accident/
│   │   │   └── Non Accident/
│   │   └── test/
│   │       ├── Accident/
│   │       └── Non Accident/
│   ├── hospitals/
│   │   └── india_hospitals_master_cleaned.csv
│   └── police/
│       └── india_police_stations_master_cleaned.csv
├── models/
│   ├── mobilenetv2_best.keras
│   └── resnet50_best.keras
├── notebooks/
│   ├── 01_data_validation.ipynb
│   ├── mobilenetv2.ipynb
│   ├── resnet50.ipynb
│   ├── 04_ml_models.ipynb
│   └── 05_model_comparison.ipynb
├── results/
│   ├── classical_ml/
│   │   ├── features/                  # Cached CNN feature arrays
│   │   ├── models/                    # Six serialized classical classifiers
│   │   ├── confusion_matrices/
│   │   ├── reports/                   # Per-experiment classification reports
│   │   ├── test_predictions/          # Per-image held-out predictions
│   │   ├── classical_ml_metrics.csv
│   │   ├── classical_ml_comparison.csv
│   │   ├── cnn_and_classical_ml_comparison.csv
│   │   └── classical_ml_comparison.png
│   ├── confusion_matrices/
│   ├── plots/
│   ├── mobilenetv2_confusion_matrix.png
│   ├── mobilenetv2_history.csv
│   ├── mobilenetv2_metrics.csv
│   ├── mobilenetv2_test_predictions.npz
│   ├── mobilenetv2_training_curves.png
│   ├── resnet50_confusion_matrix.png
│   ├── resnet50_history.csv
│   ├── resnet50_metrics.csv
│   ├── resnet50_test_predictions.npz
│   ├── resnet50_training_curves.png
│   └── metrics.csv
└── src/
```

## Reproducibility and validation

- The notebook seeds Python, NumPy, and TensorFlow random-number generators.
- Dataset file order is deterministic, and cached features are checked against model hashes, image file metadata, preprocessing names, feature layers, and class mapping before reuse.
- Scalers are contained within the SVM and Logistic Regression pipelines and are fit on training data only.
- Notebook code cells were executed successfully. The six saved classifiers, cached feature dimensions and finite values, prediction CSVs, reports, confusion-matrix PNGs, and reported metric values were verified.
- The ResNet50 archive contains inert Keras configuration fields unsupported by some runtimes. In the tested environment it was loaded from a temporary compatibility copy that removed only disabled/empty metadata fields; the original saved model and learned weights were not modified.
- TensorFlow emitted an OpenCV/NumPy ABI warning (`_ARRAY_API not found`) in the local environment, but the experiments completed successfully.

## Current gaps and notes

- `src/` currently has no reusable implementation files; the visible project work is primarily in notebooks and saved artifacts.
- The classical experiment metrics are saved under `results/classical_ml/`. The repository-level `results/metrics.csv` is unchanged and still contains only its existing header; it is not the classical experiment comparison file.
- The README describes an earlier scaffold: its documented image paths and notebook filenames do not match the current repository, and it says trained models are not included even though both model files are present.
- A `Pictures - Shortcut.lnk` file is present under the test `Non Accident` dataset folder; it is a shortcut, not a JPG image counted in the dataset totals above.
