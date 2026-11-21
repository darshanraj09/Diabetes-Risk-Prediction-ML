# Diabetes Readmission Analytics

An end-to-end machine-learning project for exploring **hospital readmission among patients with diabetes**. The project combines a Python model-training pipeline with a React dashboard and an Express API for dataset inspection, model evaluation, and predictions.

> **Important scope clarification:** The supplied dataset's target column is `readmitted`. This project therefore models a hospital-readmission outcome in a diabetes-related dataset; it does **not** diagnose diabetes or estimate whether a person will develop diabetes. The interface contains some diabetes-risk wording that should be aligned with the dataset before presenting this as a clinical tool.

## Project at a glance

- **Frontend:** React, TypeScript, Vite, Tailwind CSS, Recharts
- **Backend:** Node.js, Express, TypeScript
- **Machine learning:** Python, pandas, NumPy, scikit-learn
- **Models compared:** Logistic Regression, Random Forest, Support Vector Machine (SVM)
- **Evaluation:** 70/30 split, 80/20 split, 5-fold cross-validation, 10-fold cross-validation
- **Reported dataset size:** 81,410 rows × 30 columns before preprocessing

## Features

- Dataset upload and basic dataset profiling
- Missing-value and duplicate-row reporting
- Model training and comparison
- Accuracy, precision, recall, and F1-score reporting
- Cross-validation and stability analysis
- Feature-importance visualization
- Confusion-matrix visualization
- Prediction API and dashboard views

## Architecture

```text
React + TypeScript dashboard
          |
          | HTTP /api requests
          v
Express + TypeScript API
          |
          | invokes Python pipeline
          v
Python + pandas + scikit-learn
          |
          +-- ml/data/dataset.csv
          |
          +-- ml/results/*.json
          +-- trained model artifacts (generated locally)
```

## Repository structure

```text
.
├── artifacts/
│   ├── api-server/             # Express API
│   └── diabetes-predictor/     # React dashboard
├── lib/
│   ├── api-client-react/       # Generated API client
│   ├── api-spec/               # OpenAPI specification
│   ├── api-zod/                # Shared validation schemas
│   └── db/                     # Shared database package
├── ml/
│   ├── data/                     # Add an authorized dataset here
│   ├── results/
│   │   ├── dataset_info.json
│   │   ├── features.json
│   │   └── training_results.json
│   └── diabetes_pipeline.py
├── package.json
├── pnpm-workspace.yaml
└── requirements.txt
```

## Model evaluation

The repository includes the evaluation results produced by the supplied project. The table below reports the mean accuracy values recorded in its stability-analysis output.

| Model | Mean accuracy | Standard deviation |
|---|---:|---:|
| Logistic Regression | 61.83% | 0.00400 |
| Random Forest | 60.09% | 0.00557 |
| SVM | 61.75% | 0.00340 |

- **Highest mean accuracy in the saved results:** Logistic Regression
- **Lowest accuracy variation in the saved results:** SVM

These are the values stored in `ml/results/training_results.json`; they have not been independently reproduced as part of this repository cleanup. See [`docs/MODEL_EVALUATION.md`](docs/MODEL_EVALUATION.md) for the split-by-split metrics.

## Getting started

### Prerequisites

- Python 3.11 or later
- Node.js 20 or later
- pnpm
- Git

### 1. Install Python dependencies

```bash
python -m venv .venv
```

Activate the environment:

**Windows PowerShell**
```powershell
.\.venv\Scripts\Activate.ps1
```

**macOS / Linux**
```bash
source .venv/bin/activate
```

Then install dependencies:

```bash
pip install -r requirements.txt
```

### 2. Install JavaScript dependencies

From the repository root:

```bash
pnpm install
```

### 3. Check the dataset

Place an authorized CSV dataset at:

```text
ml/data/dataset.csv
```

The original archive contained a health-related dataset, but its source and redistribution licence were not documented. To avoid republishing data without confirming permission, the raw CSV is not included in this GitHub-ready package. The application also supports uploading a CSV through its dataset page.

The pipeline expects a CSV containing a target column named `readmitted` (or one of the alternative target names handled in `ml/diabetes_pipeline.py`).

### 4. Start the API

The Vite development server is configured to proxy `/api` requests to `http://localhost:8080`.

**Windows PowerShell**
```powershell
$env:PORT = "8080"
pnpm --filter @workspace/api-server dev
```

### 5. Start the dashboard

Open a second terminal at the repository root:

```bash
pnpm --filter @workspace/diabetes-predictor dev
```

Open the local URL printed by Vite (normally `http://localhost:5173`).

> The project originated in a Replit workspace. A clean local setup may need environment or script adjustments depending on your machine. The repository's original package scripts and application code are retained rather than silently rewritten.

## Generated model files

The supplied archive contained a large `trained_model.pkl` file (approximately 440 MB). It is intentionally **not** included in this GitHub-ready package because GitHub blocks ordinary files larger than 100 MB.

The training pipeline writes model artifacts under `ml/results/`. Run the training workflow from the dashboard after confirming the dataset and environment are configured. Generated `.pkl` files are ignored by Git.

## Methodology notes and limitations

- The target field is `readmitted`, so results should be interpreted as readmission classification, not diabetes diagnosis.
- The saved dataset summary reports 81,410 rows and 30 columns before preprocessing, 102 duplicate rows, and no missing values in the supplied dataset.
- The current Python pipeline scales the feature matrix before creating train/test splits and cross-validation folds. This can cause **data leakage** and make evaluation results optimistic. A stronger version should fit preprocessing only on each training fold, ideally with a scikit-learn `Pipeline`.
- The current project should be treated as an educational prototype, not a medical device or clinical decision-support system.
- The original archive does not document the dataset's source or redistribution licence. Verify the dataset's provenance and licence before making the repository public.

## Future improvements

- Move scaling and categorical preprocessing inside a scikit-learn `Pipeline` to prevent data leakage.
- Add stratified train/test splits and report class-wise metrics.
- Align the prediction form with the actual `readmitted` target and the dataset's 29 model features.
- Add automated tests for preprocessing, training, API endpoints, and prediction input validation.
- Record the dataset citation, source URL, and licence.
- Add a reproducible model-training command and document the exact environment used for the saved metrics.

## Disclaimer

This repository is for learning and demonstration. Its predictions are not medical advice and must not be used to make diagnosis or treatment decisions.
