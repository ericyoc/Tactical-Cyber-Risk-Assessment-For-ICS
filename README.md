Here is the **GitHub README.md** file for your ICS Cyber Risk Assessment project:

```markdown
# ICS Tactical Cyber Risk Assessment

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![IEEE](https://img.shields.io/badge/IEEE-Paper-orange.svg)](https://ieeexplore.ieee.org)

A data-driven tactical cybersecurity risk assessment framework for Industrial Control Systems (ICS) integrating Random Forest ensemble machine learning with the Joint Risk Analysis Methodology (JRAM).

## Overview

This repository contains the complete implementation of the methodology presented in *"Tactical Cyber Risk Assessment for Industrial Control Systems"* (Quemada, Yocam, & Vaidyan). The framework fuses three open-source datasets to automate risk assessment and produce quantifiable, actionable risk levels for tactical decision-making.

### Key Features

- **Multi-modal data fusion** combining ICS telemetry, vulnerability data, and dark web threat intelligence
- **Random Forest classifier** (50 trees, max depth 10) with baseline comparisons (Decision Tree, Linear SVM)
- **JRAM integration** mapping probabilities to likelihood categories and consequence levels
- **Feature importance ranking** identifying critical ICS sensors for prioritization
- **Publication-ready visualizations** (confusion matrix, ROC curves, risk contour matrix)

## Datasets

The framework uses three datasets:

| Dataset | Description | Records |
|---------|-------------|---------|
| **HAI 22.04** | ICS testbed telemetry (steam-turbine power generation) | 1.36M rows, 88 sensors |
| **NVD Vulnerabilities** | ICS-specific CVEs (Siemens, Rockwell, Schneider, ABB, etc.) | 140 CVEs |
| **Dark Web Threat Intel** | Onion domains from Dizzy dataset | 32,555 domains |

## Results

| Model | Accuracy | Precision | Recall | F1 |
|-------|----------|-----------|--------|-----|
| Random Forest | 0.9931 | 1.0000 | 0.2219 | 0.3633 |
| Decision Tree | 0.9930 | 1.0000 | 0.2016 | 0.3355 |
| SVM (Linear) | 0.9922 | 1.0000 | 0.1155 | 0.2072 |

### Top 10 Most Important Features

| Feature | Importance |
|---------|------------|
| P3_FIT01 (flow transmitter) | 24.4% |
| P4_ST_PT01 (pressure) | 12.0% |
| P2_SCO (solenoid control) | 11.3% |
| P1_FT01Z | 8.6% |
| P1_LIT01 | 6.2% |
| P2_SIT01 | 5.5% |
| P3_PIT01 | 4.0% |
| P3_LCP01D | 2.6% |
| P3_LCV01D | 2.6% |
| P3_LIT01 | 2.6% |

## Repository Structure

```
ics-cyber-risk-assessment/
├── README.md
├── requirements.txt
├── ics_risk_assessment.py          # Main implementation
├── figures/                         # Place figures here manually
│   ├── feature_importance_hai_22_04.png
│   ├── confusion_matrix_hai_22_04.png
│   ├── roc_curves_hai_22_04.png
│   └── risk_contour.png
├── results/                         # Generated outputs
│   ├── model_results.csv
│   ├── feature_importance.csv
│   └── random_forest_model.pkl
└── datasets/                        # Data directory (not included in repo)
    ├── hai-22.04/
    ├── vulnerability_data/
    └── darkweb_data/
```

## Installation

### Prerequisites

- Python 3.10 or higher
- Google Colab (recommended) or local environment with 8GB+ RAM

### Setup

1. Clone the repository:

```bash
git clone https://github.com/ericyoc/ics-cyber-risk-assessment.git
cd ics-cyber-risk-assessment
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

### Google Colab Setup (Recommended)

1. Upload `ics_risk_assessment.py` to Google Colab
2. Mount Google Drive:
```python
from google.colab import drive
drive.mount('/content/drive')
```

3. Create a Kaggle API token:
   - Go to [kaggle.com/settings](https://www.kaggle.com/settings) → Account → API → Create New Token
   - Upload `kaggle.json` to Colab (or add to Secrets as `KAGGLE_KEY`)

## Usage

### Complete Pipeline

Run the main script:

```python
python ics_risk_assessment.py
```

### In Google Colab

```python
# Run the complete pipeline
!python ics_risk_assessment.py

# Or import the module
from ics_risk_assessment import ICSRiskAssessor

# Initialize with path to your datasets
assessor = ICSRiskAssessor(data_path='/content/drive/MyDrive/ics_cyber_risk_assessment/datasets')

# Run full pipeline
assessor.load_data()
assessor.train_models()
assessor.evaluate()
assessor.generate_figures()
assessor.jram_assessment()
```

## Outputs

The script generates the following outputs in your Google Drive:

| Output | Format | Description |
|--------|--------|-------------|
| `model_results.csv` | CSV | Accuracy, precision, recall, F1 for all models |
| `feature_importance.csv` | CSV | Top 10 features with importance percentages |
| `confusion_matrix.pdf` | PDF | Random Forest confusion matrix |
| `roc_curves.pdf` | PDF | ROC curves for all models |
| `feature_importance.pdf` | PDF | Bar chart of top features |
| `risk_contour.png` | PNG | JRAM risk assessment matrix |
| `random_forest_model.pkl` | Pickle | Trained model for reuse |

## Acknowledgments

- HAI dataset: Shin et al. (2021) - National Security Research Institute, South Korea
- NVD vulnerability data: National Institute of Standards and Technology
- Dizzy dark web dataset: cibr-qcri/dizzy-datasets

**Note:** The `datasets/` directory and actual data files are **not** included in the GitHub repository due to size and licensing. Users must download the HAI dataset from Kaggle as described in the README.
