# Diabetes 30-Day Readmission Prediction

A machine learning project using the Diabetes 130-US hospitals dataset to explore whether a hospital encounter is followed by a readmission within 30 days.

# Project overview

The original `readmitted` field has three values:

- `NO` — no readmission.
- `>30` — readmission after 30 days.
- `<30` — readmission within 30 days.

For the binary prediction task, the target is defined as:

- `readmitted_30d = 1` when `readmitted == '<30'`.
- `readmitted_30d = 0` when `readmitted` is `NO` or `>30`.

The project includes a data audit and a baseline modelling notebook. The baseline uses a majority-class dummy classifier for comparison and Logistic Regression as the initial machine-learning model.

# Repository structure

```text
ML/
├── .venv/                         # Local virtual environment (not committed)
├── data/
│   ├── diabetic_data.csv
│   └── IDS_mapping.csv
├── notebooks/
│   ├── data_audit.ipynb
│   └── baseline_model.ipynb
├── reports/
│   ├── data_audit_summary.md
│   ├── baseline_results.md
│   ├── train_test_split.md
│   └── assistance_declaration...  # If required by the project
├── .gitignore
├── README.md
└── requirements.txt
```

The `.venv/` directory is local to your computer and should not be uploaded to GitHub.

# Requirements

- Python 3.10 or later recommended.
- Visual Studio Code.
- VS Code Python and Jupyter extensions.
- The two dataset files placed in the `data/` directory.

# Setup (Windows / PowerShell)

Open the project root folder in VS Code. Open **Terminal → New Terminal** and confirm that the terminal is in the project root (the folder containing `requirements.txt`).

# 1. Create a virtual environment

```powershell
python -m venv .venv
```

# 2. Activate the environment

```powershell
.\.venv\Scripts\Activate.ps1
```

If PowerShell blocks activation, you can use Command Prompt instead:

```cmd
.venv\Scripts\activate.bat
```

# 3. Install dependencies

```powershell
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

# 4. Register the Jupyter kernel

```powershell
python -m ipykernel install --user --name diabetes-env --display-name "Python (Diabetes)"
```

# 5. Select the kernel in VS Code

1. Open `notebooks/data_audit.ipynb`.
2. Click **Select Kernel** in the notebook toolbar.
3. Choose **Python (Diabetes)** (or the project's `.venv` Python interpreter).
4. Repeat for `notebooks/baseline_model.ipynb`.

# Dataset placement

Download or obtain the dataset files separately and place them here:

```text
data/
├── diabetic_data.csv
└── IDS_mapping.csv
```

The notebooks expect the files to be available locally. Do not commit private, restricted, or large dataset files to a public repository unless you have confirmed that redistribution is permitted. If the dataset is not included in the repository, explain in your project submission how to obtain it.

# How to run

Run the notebooks in this order:

# 1. Data audit

Open `notebooks/data_audit.ipynb` and run all cells from top to bottom.

The audit examines:

- Dataset dimensions and variable types.
- Missing values.
- Duplicate rows and encounter identifiers.
- Original and binary target distributions.
- Potential numerical outliers using the IQR method.

### 2. Baseline model

Open `notebooks/baseline_model.ipynb` and run all cells from top to bottom.

The baseline workflow:

- Creates the binary 30-day readmission target.
- Splits data by patient so the same patient does not appear in both training and test sets.
- Imputes missing values and scales numerical features.
- Encodes categorical features.
- Compares a majority-class dummy classifier with Logistic Regression.
- Evaluates performance using PR-AUC, ROC-AUC, and recall at a precision threshold of 0.60.

**Important:** Because the notebooks are stored in `notebooks/` while the CSV files are stored in the project-root `data/` directory, check the data-loading cell. If the notebook uses a relative path such as `Path("data")`, change it to:

```python
from pathlib import Path

DATA_DIR = Path("../data")
```

Likewise, if a notebook saves reports to a `reports/` directory and you want them in the project-root `reports/` folder, use:

```python
REPORTS_DIR = Path("../reports")
REPORTS_DIR.mkdir(parents=True, exist_ok=True)
```

Run the notebook from its first cell after selecting the correct kernel.

# Reproducibility

Install the dependencies using `requirements.txt` and run the notebooks in the order above. The notebooks contain the analysis and modelling workflow. For reproducible results, keep the random seeds and model settings in the notebooks unchanged unless you document the changes.

