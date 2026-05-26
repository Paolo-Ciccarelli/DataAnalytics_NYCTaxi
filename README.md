# COMP 333 Final Project - Phase 3

This repository contains our COMP 333 final project using the NYC TLC Yellow Taxi dataset (January 2024 to December 2025). The submission is implemented as a single end-to-end notebook that covers Phase 1 and Phase 2 in one reproducible pipeline: data retrieval, cleaning, exploratory data analysis, a baseline linear regression model, advanced supervised learning, feature selection, unsupervised learning, interpretation, and ethical considerations.


## Team Members

- **Arad Hajari (40242069)**
- **Noah Burns (40237138)** 
- **Paolo Ciccarelli (40286203)**
- **Sang Ho Lee (40229178)**
---

**Phase 1 includes:** 
- Programmatic retrieval of monthly NYC TLC Yellow Taxi parquet files
- Reproducible data cleaning and audit reporting
- Exploratory data analysis
- 2 research questions
- A simple linear regression baseline for fare prediction

**Phase 2 extends the pipeline with:** 
- Advanced supervised learning for multiclass fare-tier prediction
- A feature selection comparison using filter, wrapper, and embedded methods
- Unsupervised learning with PCA and clustering
- Model interpretation using feature importance and partial dependence plots
- Ethical Considerations

## Dataset

- Official dataset page: `https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page`
- Files used: monthly parquet files `yellow_tripdata_YYYY-MM.parquet`
- Data Range used: 2024-01 through 2025-12
- Why this dataset:
  - large enough (>= 1GB when multiple months are combined),
  - real-world and messy (missing values, invalid rows, outliers),
  - supports both supervised and unsupervised analysis.

## Main Implementation

The final submission is implemented as a single end-to-end notebook, not as separate Phase 1 and Phase 2 execution pipelines. The notebook performs the full workflow in order:

1. Configuration and imports  
2. Data retrieval  
3. Data cleaning  
4. Loading cleaned outputs  
5. Exploratory data analysis  
6. Baseline simple linear regression  
7. Feature engineering and target definition  
8. Sampling and train/test split  
9. Advanced supervised learning  
10. Feature selection comparison  
11. Unsupervised learning  
12. Interpretation  
13. Ethical considerations  
14. Division of labour

## Repository Layout

```text
.
├── README.md
├── requirements.txt
├── notebooks/
│   ├── phase1_comp333.ipynb         # Phase 1 revised
│   ├── phase2_comp333.ipynb         # Phase 2 notebook
|   ├── phase3_comp333.ipynb         # Phase 3 notebook
└── data/
    ├── raw/
    │   ├── retrieval_log.csv
    │   └── size_summary.txt
    └── processed/
        └── cleaning_audit.json
        └── phase1_cleaned.parquet

```
If your notebook filename differs, update the commands below accordingly.

## Setup

### Linux / macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Windows (PowerShell)

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## How to Run
Run the final notebook end-to-end. It includes retrieval, cleaning, modeling, clustering, interpretation, and output generation in one pipeline.

``bash
.venv/bin/jupyter nbconvert --to notebook --execute notebooks/comp333_final_pipeline.ipynb --output phase3_comp333.ipynb --output-dir notebooks

**Note:** If you see a "Notebook JSON is invalid" or "required property" error from nbconvert, the notebook may have outdated output metadata. Clear code cell outputs (e.g. in Jupyter: Cell → All Output → Clear) and run the command again.

## Output Files

Running the notebook generates or updates the following outputs:

- `data/raw/retrieval_log.csv` - retrieval log with URL, timestamp, size, checksum, status
- `data/raw/size_summary.txt` - total raw size and >=1GB check
- `data/processed/phase1_cleaned.parquet` - cleaned dataset for EDA/modeling
- `data/processed/cleaning_audit.json` - cleaning summary (missing rates, rows removed, etc.)

### Phase 1 Analysis
- Descriptive statistics, plots, and EDA figures
- Baseline linear regression metrics and residual analysis

### Phase 2 Analysis
- Classification metrics, confusion matrices, ROC-AUC curves, and cross-validation results
- Feature-selection comparison outputs
- PCA visualizations, clustering diagnostics, and cluster profiles
- Model interpretation figures, including feature importance and partial dependence plots

**Notes:**

- If a month is unavailable from the host (e.g., HTTP 403/404), it is logged as failed and execution continues.
- The current run still satisfies the size requirement (>1GB total raw data).

## Feature Engineering and Target

The notebook engineers several features before modeling, including:

- Time features: `pickup_hour`, `pickup_dayofweek`, `is_weekend`
- Distance transform: `log_distance`
- Domain flags: `is_overnight`, `is_airport_trip`

The multiclass supervised target is **`fare_tier`**, created from `fare_amount` using the following bins:

- **low:** `0 <= fare_amount < 15`
- **mid:** `15 <= fare_amount < 40`
- **high:** `fare_amount >= 40`

To avoid target leakage, `fare_amount` and `tip_amount` are excluded from the supervised feature set.

## Sampling Strategy

Because the cleaned dataset contains roughly 83 million rows, the notebook uses manageable samples for modeling:

- **Supervised learning:** `500,000` rows
- **Wrapper feature selection:** `100,000` rows
- **Unsupervised learning:** `100,000` rows

These values are configurable through the notebook constants.

### Advanced Supervised Learning

- **Target:** `fare_tier` (multiclass classification: low, mid, high)
- **Models implemented:**
  1. Random Forest Classifier
  2. Multilayer Perceptron (MLP)
  3. XGBoost Classifier

All models are evaluated using stratified cross-validation and held-out test performance. Reported metrics include accuracy, precision, recall, macro F1, ROC-AUC, classification reports, and confusion matrices.

In the final notebook results, **XGBoost** is selected as the best overall model by macro F1 and overall classification performance.

### Feature Selection Comparison

Three feature-selection approaches are compared:

1. **Filter method:** `SelectKBest` with mutual information, selecting the top 8 features
2. **Wrapper method:** forward sequential feature selection using a scaled logistic regression pipeline
3. **Embedded method:** Random Forest feature importance using a mean-importance threshold

### Unsupervised Learning

The unsupervised section uses trip-structure features only and does not use the supervised `fare_tier` target during clustering.

- **PCA:** Principal component analysis for dimensionality reduction
- **KMeans:** Clustering to discover natural groupings in the trip data

## Interpretation

The best supervised model, **XGBoost**, is interpreted using:

- Feature importance
- Partial Dependence Plots (PDPs)

The interpretation focuses on which engineered and structural trip features most strongly drive classification into `low`, `mid`, and `high` fare tiers.

## Notes

- If a monthly TLC file is unavailable from the host, the failure is logged and execution continues.
- The pipeline is designed to be reproducible from raw data download through final analysis.
- The current implementation is best described as a unified final pipeline rather than two separately executed notebooks.

## AI Usage Disclosure

AI assistance was used for structure, wording, code refinement, and documentation. All submitted code and outputs were reviewed and understood by the students.
