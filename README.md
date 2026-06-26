<div align="center">

# ⚡ Short-Term Load Forecasting
### Hybrid CNN–XGBoost Model with Advanced Hyperparameter Optimization

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)](https://www.tensorflow.org/)
[![XGBoost](https://img.shields.io/badge/XGBoost-Gradient%20Boosting-green?style=for-the-badge)](https://xgboost.readthedocs.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebooks-F37626?style=for-the-badge&logo=jupyter&logoColor=white)](https://jupyter.org/)

*A hybrid deep learning pipeline for short-term electrical load forecasting, combining CNN-based feature extraction with XGBoost regression and multiple hyperparameter tuning strategies.*

</div>

---

## 📌 Overview

Short-Term Load Forecasting (STLF) is critical for grid operators, energy planners, and power utilities to maintain the balance between electricity generation and consumption. This project implements a **Hybrid CNN–XGBoost** architecture — using a 1D Convolutional Neural Network as a spatial feature extractor, whose learned representations feed into an XGBoost regressor for final load prediction.

Three hyperparameter optimization strategies are benchmarked against the same hybrid backbone:

| Strategy | Notebook |
|---|---|
| 🔀 Random Search (baseline tuning) | `Hybrid_XG_CNN.ipynb` |
| 🧬 Evolutionary / Genetic Algorithm | `Evolutionary (Genetic) Algorithm.ipynb` |
| 🎯 Bayesian Optimization (best model) | `Bayesian -Best Model.ipynb` |
| 📈 Standalone XGBoost (ablation) | `Xgboost model.ipynb` |
| 🔁 CNN-only baseline | `stlf (1).ipynb` |
| ⚙️ Hybrid with manual tuning | `hybrid_tuning.ipynb` |

---

## 🏗️ Architecture

```
Raw Load Data (Excel)
        │
        ▼
 Feature Engineering
 (temporal features, lag variables, date/time encodings)
        │
        ▼
  MinMax / Standard Scaling
        │
        ▼
  ┌─────────────────────┐
  │   CNN Feature       │   Conv1D → MaxPool → Flatten → Dense
  │   Extractor         │   (Trained on 70% time-series split)
  └─────────┬───────────┘
            │  learned feature embeddings
            ▼
  ┌─────────────────────┐
  │   XGBoost           │   Gradient boosted trees
  │   Regressor         │   tuned via Bayesian / GA / Random Search
  └─────────┬───────────┘
            │
            ▼
   Load Forecast (L8 target)
   Evaluated on Jan / Feb / March 2025 holdout sets
```

---

## 📁 Repository Structure

```
Short-term-load-forcasting/
│
├── 📓 Bayesian -Best Model.ipynb         # Best performing model (Bayesian-tuned hybrid)
├── 📓 Evolutionary (Genetic) Algorithm.ipynb  # GA-based hyperparameter tuning
├── 📓 Hybrid_XG_CNN.ipynb               # Baseline hybrid CNN + XGBoost
├── 📓 hybrid_tuning.ipynb               # Manual/random search tuning variant
├── 📓 Xgboost model.ipynb              # Standalone XGBoost with RandomizedSearchCV
├── 📓 stlf (1).ipynb                   # CNN-only baseline model
│
├── 📊 Feature_engineering_DS.xlsx       # Main training dataset (engineered features)
├── 📊 COMPLETE DATASET( GEN N LOAD).xlsx # Raw generation and load dataset
├── 📊 2025_testing.xlsx                 # Holdout test set
│
└── 📄 README.md
```

---

## 🔬 Models & Methods

### 1. CNN Feature Extractor
- **Architecture:** `Conv1D (64 filters) → MaxPooling1D → Flatten → Dense(100) → Output`
- **Framework:** TensorFlow / Keras
- **Purpose:** Captures local temporal patterns and cross-feature interactions from the scaled input matrix

### 2. XGBoost Regressor
- **Objective:** `reg:squarederror`
- **Input:** Feature embeddings from the frozen CNN extractor
- **Tunable hyperparameters:** `n_estimators`, `learning_rate`, `max_depth`, `min_child_weight`, `subsample`, `colsample_bytree`, `gamma`, `reg_alpha`, `reg_lambda`

### 3. Hyperparameter Optimization Strategies

| Method | Library | Description |
|---|---|---|
| **Random Search** | `sklearn.model_selection.RandomizedSearchCV` | Samples from log-uniform and uniform distributions over 10 iterations |
| **Genetic Algorithm** | `sklearn_genetic.GASearchCV` | Evolves hyperparameter populations via selection, crossover, and mutation |
| **Bayesian Optimization** | `skopt.BayesSearchCV` | Builds a probabilistic surrogate model (Gaussian Process) to guide search |

---

## 📊 Datasets

| File | Description |
|---|---|
| `Feature_engineering_DS.xlsx` | Primary training dataset with engineered temporal and lag features; target column: `L8` |
| `COMPLETE DATASET( GEN N LOAD).xlsx` | Raw dataset containing generation and load records |
| `2025_testing.xlsx` | 2025 holdout test data for final evaluation |

**Data split:** 70% train / 15% validation / 15% test (time-ordered, no random shuffle to prevent leakage)

**Target variable:** `L8` — short-term electrical load (next time step)

**Missing values:** Filled using linear regression interpolation

---

## 📈 Evaluation Metrics

All models are evaluated using:

- **MAPE** — Mean Absolute Percentage Error
- **RMSE** — Root Mean Squared Error
- **MAE** — Mean Absolute Error
- **R²** — Coefficient of Determination

---

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- Jupyter Notebook or JupyterLab

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/AshishRanjanGope/Short-term-load-forcasting.git
cd Short-term-load-forcasting

# 2. Create and activate a virtual environment (recommended)
python -m venv venv
source venv/bin/activate        # On Windows: venv\Scripts\activate

# 3. Install dependencies
pip install numpy pandas matplotlib scikit-learn tensorflow xgboost scikit-optimize scikit-genetic openpyxl joblib
```

### Running the Notebooks

Open any notebook in order of increasing complexity:

```bash
jupyter notebook
```

**Recommended order:**

1. `stlf (1).ipynb` — CNN baseline
2. `Xgboost model.ipynb` — XGBoost ablation
3. `Hybrid_XG_CNN.ipynb` — Hybrid baseline
4. `hybrid_tuning.ipynb` — Tuned hybrid
5. `Evolutionary (Genetic) Algorithm.ipynb` — GA-optimized
6. `Bayesian -Best Model.ipynb` ⭐ — Best results

> **Note:** Ensure `Feature_engineering_DS.xlsx` and the test Excel files are in the same directory as the notebooks before running.

---

## 🔄 Workflow

```
1. Load Feature_engineering_DS.xlsx
        ↓
2. Sort by date, split 70/15/15 (time-ordered)
        ↓
3. Scale features (MinMaxScaler / StandardScaler)
        ↓
4. Reshape for CNN input: (samples, 1, features)
        ↓
5. Train CNN feature extractor
        ↓
6. Extract CNN embeddings → feed into XGBoost
        ↓
7. Tune XGBoost via Random / GA / Bayesian search
        ↓
8. Save models: cnn_feature_extractor.h5, xgb_model.pkl, scalers
        ↓
9. Evaluate on Jan / Feb / March 2025 holdout sets
        ↓
10. Plot predicted vs. actual load curves
```

---

## 🤝 Contributing

Contributions are welcome! If you'd like to add new model architectures, improve feature engineering, or benchmark additional tuning strategies:

1. Fork the repository
2. Create a feature branch
   ```bash
   git checkout -b feature/YourFeature
   ```
3. Commit your changes
   ```bash
   git commit -m 'Add YourFeature'
   ```
4. Push to the branch
   ```bash
   git push origin feature/YourFeature
   ```
5. Open a Pull Request

---

## 📄 License

Distributed under the **MIT License**. See [`LICENSE`](LICENSE) for details.

---

## 📚 References

- Ran, P., Dong, K., Liu, X., & Wang, J. (2022). *Short-Term Load Forecasting Based on CEEMDAN and Transformer.* Electric Power Systems Research.
- Chen et al. (2022). *Short-Term Load Forecasting Based on CEEMDAN-SE-BPNN-Transformer.* Energies, 15(10), 3659.

---

<div align="center">

Made with ⚡ for smarter energy systems

⭐ **Star this repo if it helped your research!**

</div>
