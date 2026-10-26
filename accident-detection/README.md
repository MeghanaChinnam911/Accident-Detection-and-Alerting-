# Accident Detection

A workspace for validating accident-image data, training image classifiers, and comparing model performance.

## Project layout

- `datasets/accident_images/accident/` and `datasets/accident_images/non_accident/`: place labeled images in these folders.
- `datasets/hospitals/india_hospitals_master_cleaned.csv`: hospital reference data (add the source file here).
- `datasets/police/india_police_stations_master_cleaned.csv`: police-station reference data (add the source file here).
- `notebooks/`: sequential data-validation, model-training, and comparison notebooks.
- `models/`: saved model artifacts.
- `results/metrics.csv`, `results/confusion_matrices/`, and `results/plots/`: evaluation outputs.
- `src/`: reusable project code.

## Notebook sequence

1. `01_data_validation.ipynb` checks the expected dataset paths and image class folders.
2. `02_mobilenetv2.ipynb` is the MobileNetV2 experiment workspace.
3. `03_resnet50.ipynb` is the ResNet50 experiment workspace.
4. `04_ml_models.ipynb` is the classical machine-learning experiment workspace.
5. `05_model_comparison.ipynb` reads recorded metrics for comparison.

The model notebooks are starting points; training settings and preprocessing should be chosen to match the actual dataset. No data or trained models are included in this scaffold.
