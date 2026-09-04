# Gold Recovery Prediction

## Project overview

This project develops a machine-learning prototype for predicting gold recovery from ore using parameters recorded during flotation and purification. The solution estimates recovery at two stages:

- rougher concentrate recovery;
- final concentrate recovery.

The project is framed as a predictive prototype for industrial decision support. It estimates recovery under observed process conditions but does not establish which settings cause recovery to change.

## Main result

Random Forest and CatBoost models were evaluated with expanding-window time-series cross-validation. CatBoost produced the best result for both targets.

| Metric | Chronological CV | Untouched test period |
| --- | ---: | ---: |
| Rougher recovery sMAPE | 6.655% | **4.166%** |
| Final recovery sMAPE | 8.923% | **7.515%** |
| Weighted final sMAPE | 8.356% | **6.678%** |

The training-median baseline achieved a weighted test sMAPE of **8.295%**. The selected CatBoost models reduced weighted test error by **19.5%** relative to this baseline.

## Workflow

1. Load and validate the training, test, and complete source datasets.
2. Verify the supplied recovery calculations.
3. Examine missing values, distributions, chronological coverage, and feature availability.
4. Analyse outliers and associations between process parameters and recovery.
5. Compare metal concentrations and feed-particle-size distributions across processing stages and datasets.
6. Build separate feature matrices for rougher and final recovery.
7. Evaluate RandomForestRegressor and CatBoostRegressor with expanding-window cross-validation.
8. Test the selected models on the untouched later period and compare them with constant baselines.

## Repository structure

```text
gold-recovery-prediction/
├── data/
│   └── README.md
├── .gitignore
├── README.md
├── requirements.txt
└── gold_recovery_prediction.ipynb
```

## Data

The project uses three datasets:

| File | Purpose | Rows |
| --- | --- | ---: |
| `gold_industry_train.csv` | Model development period | 14,579 |
| `gold_industry_test.csv` | Later test period | 4,860 |
| `gold_industry_full.csv` | Complete source data | 19,439 |

The training and test sets represent consecutive chronological periods. The test feature schema contains **52 input and state variables** available before the outcomes are known. Target values for the test timestamps are matched from the complete dataset and used exclusively for final evaluation.

The notebook looks for the files in `/datasets`, `data/`, and the repository root. If they are unavailable locally, it downloads them from the public source URLs used in the original project environment.

## Data preparation

- Missing values are filled separately within the chronological training and test periods using forward fill.
- A group of 696 isolated training rows with near-zero output concentrations is removed from model development.
- The complete set of 4,860 test observations remains untouched.
- The rougher model uses 22 rougher-stage input and state features.
- The final model uses all 52 features available in the original test schema.
- Targets, output parameters, calculated values unavailable at prediction time, and diagnostic features are excluded from the model matrices.

## Model selection

Random Forest and CatBoost configurations are compared using five-fold `TimeSeriesSplit`. The selected CatBoost configuration for both targets uses:

- 100 iterations;
- tree depth of 4;
- learning rate of 0.03;
- L2 regularization of 3.

The project metric is weighted sMAPE:

- 25% rougher recovery sMAPE;
- 75% final recovery sMAPE.

## How to run the project

Python 3.10 or newer is recommended. The notebook metadata uses Python 3.12.

```bash
git clone <repository-url>
cd gold-recovery-prediction

python -m venv .venv
```

Activate the virtual environment:

```bash
# Windows PowerShell
.venv\Scripts\Activate.ps1

# macOS or Linux
source .venv/bin/activate
```

Install the dependencies and start JupyterLab:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
jupyter lab
```

Open `gold_recovery_prediction.ipynb` and run the cells in order. The complete time-series cross-validation search may take several minutes depending on the computer.

## Technologies

- Python
- pandas and NumPy
- Matplotlib and seaborn
- PhiK
- scikit-learn
- CatBoost
- JupyterLab

## Limitations and possible improvements

The result is based on one historical test period and should be validated on additional future periods before operational use. A production implementation would also require process-expert review, data-drift monitoring, input validation, and a defined retraining policy.
