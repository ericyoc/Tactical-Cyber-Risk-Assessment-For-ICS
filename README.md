# Tactical Cyber Risk Assessment for Industrial Control Systems

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ericyoc/Tactical-Cyber-Risk-Assessment-For-ICS/blob/main/Tactical_Cyber_Risk_Assessment_For_ICS.ipynb)
[![Python](https://img.shields.io/badge/Python-3.13-blue.svg)](https://www.python.org/)

Analysis code for *Tactical Cyber Risk Assessment for Industrial Control Systems* (Sarah Quemada, Eric Yocam, Varghese Vaidyan), submitted to *Digital Engineering* (Elsevier).

## Overview

The framework couples unsupervised anomaly detection on ICS process telemetry with the Joint Risk Analysis Methodology (JRAM):

- **Detection.** An autoencoder and a gated recurrent unit (GRU) forecaster are trained on normal operation only. For each held-out fold, the detector score, its calibration (isotonic regression), and its alarm threshold (1% false-positive budget) are chosen on a separate validation file.
- **Likelihood.** The calibrated probability of each 60-s window is binned into the JRAM likelihood categories.
- **Consequence.** Each window is attributed to a physical process (P1 boiler, P2 turbine, P3 water treatment, P4 HIL simulation). A planner-set base consequence level is raised by one when an alarm occurs on a process whose controller products have a Critical CVE (CVSS ≥ 9.0) in the NVD.
- **Risk.** Likelihood and consequence are combined in the JRAM risk matrix (Low, Moderate, Significant, High, Extreme).
- **Evaluation.** Detection and risk are evaluated on held-out attack files, with block-bootstrap confidence intervals and an ablation of each data stream.

## Data

| Source | Use | Access |
|---|---|---|
| HAI 22.04 | Primary evaluation: 6 normal training files, 4 test files, 58 attack events, 68 non-constant sensors | https://github.com/icsdataset/hai (also on Kaggle) |
| HAI 23.05 | Replication: 4 normal training files, 2 test files, 52 attack events | https://github.com/icsdataset/hai |
| NVD CVE API 2.0 | Testbed controller CVEs; 140-CVE ICS keyword sample | https://nvd.nist.gov/developers/vulnerabilities |
| MITRE ATT&CK for ICS | Technique coverage per asset class (ablation) | https://github.com/mitre-attack/attack-stix-data |
| Dizzy | Dark-web onion-service records (ablation) | https://github.com/cibr-qcri/dizzy-datasets |

Datasets are not included in this repository. The notebook stores them under `MyDrive/ics_cyber_risk_assessment/datasets/` in Google Drive.

## Notebook

`Tactical_Cyber_Risk_Assessment_For_ICS.ipynb` runs on a Google Colab CPU runtime (seed 42). Its sections map to the manuscript as follows:

| Section | Produces |
|---|---|
| 1. Data acquisition | HAI datasets in Google Drive |
| 2. ICS vulnerability sample | 140-CVE severity statistics (Section 5.7) |
| 3. Dizzy dark-web dataset | Dark-web records (Section 4.3) |
| 4. Analysis v1 | Dizzy ICS-term check (Section 5.10); NVD cache used by v2 |
| 5. Analysis v2 | Leakage (Table 9), baselines and DeLong tests (Table 10), assessment time (Section 5.13), ATT&CK coverage (Section 5.10) |
| 6. v2 follow-up | ATT&CK ablation for the autoencoder (Section 5.10) |
| 7. Analysis v3 | Selected detector (Table 11, Figure 3) |
| 8. Corrected CVE layer | Testbed CVEs (Table 14), JRAM risk (Table 16, Figure 5) |
| 9. Analysis v4 (standalone) | Reproduction of Table 11, sensor importance (Table 13, Figure 4), HAI 23.05 replication (Table 17, Figure 6) |

Sections 5–8 run in one session, in order. Section 9 is standalone and takes about 1.5–2 hours on 8 vCPU.

## Key results

| Result | HAI 22.04 | HAI 23.05 |
|---|---|---|
| Random Forest ROC-AUC, random row split vs. held-out files | 0.965 vs. 0.497 | — |
| Selected detector ROC-AUC (held-out files) | 0.852 ± 0.061 | 0.824 ± 0.189 |
| Attack events detected | 43 of 58 | 33 of 52 |
| Median detection latency | 34 s | 23 s |
| False alarms per hour | 1.41 | 4.01 |
| JRAM risk ROC-AUC, detection + CVE (95% CI) | 0.765 (0.690–0.839) | 0.760 (0.667–0.845) |
| Vulnerability-layer effect (95% CI) | +0.005 (+0.001 to +0.011) | +0.006 (+0.001 to +0.013) |

ATT&CK for ICS coverage and the Dizzy dark-web data add no discriminative information. The sensors that matter most to the selected detector respond 16–81 s after attack onset, so they act as consequence indicators rather than manipulated points.

## Outputs

Results are written to `MyDrive/ics_cyber_risk_assessment/results/` and figures to `MyDrive/ics_cyber_risk_assessment/figures/`. The manuscript figures are:

| Figure | File |
|---|---|
| Figure 3 | `figures/revision_v3/roc_pr_selected.png` |
| Figure 4 | `figures/revision_v4/feature_importance_selected.png` |
| Figure 5 | `figures/revision_v3/jram_matrix_v3_fixed.png` |
| Figure 6 | `figures/revision_v4/roc_pr_selected_hai2305.png` |

## Environment

Python 3.13.18, NumPy 2.4.6, pandas 2.2.3, scikit-learn 1.6.1, SciPy 1.16.3, XGBoost 3.4.1, PyTorch 2.14.1 (CPU), Matplotlib. Reported runs used AMD EPYC 7B12, 8 vCPU, 53.5 GB RAM, no GPU.

## Acknowledgments

- HAI datasets: Shin et al., National Security Research Institute, South Korea (CC BY-SA 4.0)
- NVD: National Institute of Standards and Technology
- MITRE ATT&CK for ICS: The MITRE Corporation
- Dizzy: Boshmaf et al., Qatar Computing Research Institute
