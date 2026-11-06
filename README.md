# MIMIC-III Sepsis Prediction

This repository contains a research workflow for early sepsis prediction using MIMIC-III patient data. It covers cohort preparation, exploratory analysis, time-series preprocessing, model training and evaluation, explainability, and result visualization.

## Workflow

1. **Cohort construction and label generation**  
   Construct the study cohort from MIMIC-III and identify Sepsis-3 cases using suspected infection together with an increase in SOFA score of at least 2. Prepare prediction tasks for 4-hour and 8-hour horizons using 12-hour observation windows. The 12-hour prediction task is excluded because the eligibility audit found no positive cases for that horizon.

2. **Exploratory data analysis**  
   Use `notebooks/MIMICIII_Sepsis3_EDA.ipynb` to inspect the cohort, outcome labels, feature distributions, and missing data.

3. **Time-series preprocessing and data splitting**  
   Prepare patient time-series windows, remove windows that are entirely missing, and impute and standardize features. Fit preprocessing steps on the training data only to avoid information leakage. Split the data into stratified development and held-out test partitions, and check that patients do not overlap between partitions.

4. **Model experiments**  
   Use `notebooks/MIMICIII_Sepsis3_Experiment_Kaggle.ipynb` to train and compare four sequence-model architectures:
   - Recurrent Neural Network (RNN)
   - Long Short-Term Memory (LSTM)
   - CNN-Transformer
   - LSTM-Transformer

   Experiments compare unweighted binary cross-entropy with class-weighted binary cross-entropy to address class imbalance.

5. **Validation and final evaluation**  
   The final experiment workflow uses stratified 5-fold cross-validation on the development partition and evaluates the selected models on a separate held-out test partition. Reported metrics include accuracy, precision, recall/sensitivity, specificity, F1-score, balanced accuracy, AUROC, area under the precision-recall curve (AUPR), and Brier score.

6. **Model explainability**  
   Where available, inspect model predictions using Local Kernel SHAP, Temporal SHAP, and Transformer attention. These analyses provide complementary views of feature contributions and temporal model behavior.

7. **Results visualization**  
   The Streamlit application in `app.py` presents exported metrics, predictions, and explainability results in an interactive research dashboard. See `STREAMLIT_DEPLOY.md` for deployment instructions.

## Repository structure

- `notebooks/` — exploratory analysis and model experiments.
- `scripts/` — supporting workflow scripts.
- `data/` — dataset-related files and processed data.
- `results/` — exported experiment results used for analysis and visualization.
- `app.py` — Streamlit research dashboard.
- `requirements.txt` — Python dependencies.
- `STREAMLIT_DEPLOY.md` — dashboard deployment instructions.

## Important notes

- MIMIC-III is a credentialed research dataset. Users must obtain the required access and comply with the dataset's data-use agreement and all applicable restrictions.
- The positive class is rare in the prepared cohorts (29 of 5,190 windows for the 4-hour task and 14 of 4,717 for the 8-hour task). Performance estimates should therefore be interpreted cautiously, especially for metrics sensitive to class imbalance.
- This repository is intended for research and experimentation. The dashboard and models are not validated clinical tools and must not be used to make patient-care decisions.
