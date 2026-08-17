# TCGA-KIRC Clinical and RNA-seq Survival Prediction

This repository contains a reproducible survival-analysis workflow for progression-free interval (PFI) prediction in **TCGA Kidney Renal Clear Cell Carcinoma (TCGA-KIRC)**.

The project compares **clinical-only**, **RNA-seq-only**, and **integrated clinical + RNA-seq** survival models, with particular emphasis on predictive discrimination, calibration, risk stratification, robustness, and RNA feature-selection stability.

## Study Objective

The primary objective is to determine whether adding high-dimensional RNA-seq information improves PFI prediction beyond routinely available clinical predictors.

Three modeling strategies were evaluated:

1. Clinical-only Cox proportional hazards model
2. RNA-only penalized Cox model
3. Clinical + RNA multimodal penalized Cox model

## Cohort

- Cancer type: TCGA Kidney Renal Clear Cell Carcinoma (TCGA-KIRC)
- PFI-eligible patients: **529**
- PFI events: **159**
- Censored patients: **370**
- RNA-seq profiles aligned with clinical data: **529**

Clinical predictors included:

- Age at index
- Tumor grade
- AJCC stage

RNA-seq modeling used protein-coding genes after prevalence filtering and training-fold-specific variable-gene selection.

Raw TCGA sequencing files are not distributed in this repository.

## Validation Strategy

The primary reliability analysis used **repeated nested cross-validation**:

- 10 repeats
- 5 outer folds per repeat
- 50 held-out evaluations per model

RNA preprocessing and feature selection were performed within each outer-training fold to reduce information leakage.

Hyperparameter selection for penalized Cox models was conducted using inner cross-validation.

## Main Results

### Repeated Outer-Fold C-index

| Model | Mean C-index |
|---|---:|
| Clinical | **0.8120** |
| RNA | 0.7173 |
| Clinical + RNA | 0.7989 |

The clinical-only model achieved the highest average discrimination.

The mean paired difference between the Clinical + RNA and Clinical models was:

`-0.0131`

with an approximate repeat-level 95% bootstrap confidence interval of:

`[-0.0188, -0.0062]`

These results indicate that adding RNA-seq features did not improve overall discrimination beyond the clinical-only model.

## Time-Dependent AUC

| Model | 1 year | 3 years | 5 years |
|---|---:|---:|---:|
| Clinical | **0.8551** | **0.8216** | **0.8197** |
| RNA | 0.7283 | 0.7357 | 0.7562 |
| Clinical + RNA | 0.8344 | 0.8132 | 0.8063 |

Clinical predictors remained strongest across all three PFI horizons.

## Prediction Error

Mean Brier scores and integrated Brier score:

| Model | BS 1 year | BS 3 years | BS 5 years | IBS 1–5 years |
|---|---:|---:|---:|---:|
| Clinical | **0.0936** | **0.1344** | **0.1574** | **0.1310** |
| RNA | 0.1261 | 0.1738 | 0.1908 | 0.1695 |
| Clinical + RNA | 0.1046 | 0.1433 | 0.1647 | 0.1399 |

Lower values indicate better predictive accuracy.

The clinical-only model consistently showed the lowest prediction error.

## Risk Stratification

Median-risk Kaplan-Meier analyses showed statistically significant separation between high- and low-risk groups in all 10 repeats for all three model classes.

Mean log-rank chi-square statistics were:

| Model | Mean log-rank chi-square |
|---|---:|
| Clinical | 76.24 |
| RNA | 64.95 |
| Clinical + RNA | **97.29** |

Although the clinical-only model achieved better overall discrimination and lower prediction error, the Clinical + RNA model produced the strongest high- versus low-risk separation.

## RNA Feature Stability

Across 50 repeated multimodal fits:

- **148** unique RNA genes were selected at least once
- Mean pairwise Jaccard similarity: **0.3251**
- No gene was selected in all 50 models
- **6 genes** were selected in at least 40 of 50 models

Frequently selected genes included:

- SLC10A2
- CXCL5
- CLTRN
- SLC6A19
- SCIN
- NDNF
- G6PC
- FABP7
- PRAME
- CAPN6

These results show that predictive performance can remain comparatively stable even when the exact molecular feature set varies across resampling.

## RNA Feature-Count Sensitivity

The multimodal model was evaluated using different numbers of retained RNA features:

| Top RNA features | Mean C-index |
|---:|---:|
| 100 | 0.7984 |
| 250 | 0.7989 |
| 500 | 0.7978 |

Performance changed very little across these settings, indicating that the main conclusion was not driven by the choice of 250 retained RNA features.

## Calibration

Calibration was evaluated at 1, 3, and 5 years using out-of-fold predicted survival probabilities and observed Kaplan-Meier PFI estimates.

The clinical-only model showed the lowest mean absolute calibration error overall.

## Key Conclusion

The principal finding of this project is that **adding high-dimensional RNA-seq information did not improve overall PFI prediction beyond a parsimonious clinical model**, despite the multimodal model producing strong risk-group separation.

The results also highlight an important reliability issue in high-dimensional biomedical modeling: **predictive performance may appear stable while the selected molecular features remain only moderately stable across repeated resampling**.

## Repository Structure

```text
TCGA-KIRC-PFI-Multimodal-Survival/
│
├── README.md
├── requirements.txt
├── CITATION.cff
├── LICENSE
├── .gitignore
│
├── 02_notebooks/
│   ├── 01_audit_TCGA_KIRC_RNAseq.ipynb
│   ├── 03_build_TCGA_KIRC_expression_matrix.ipynb
│   ├── 04_prepare_PFI_modeling_cohort.ipynb
│   ├── 05_build_PFI_multimodal_model.ipynb
│   └── 06_repeated_nested_CV_reliability.ipynb
│
├── 03_processed_data/
│   └── analysis summaries and model outputs
│
└── 04_publication_outputs/
    ├── publication figures
    └── final summary tables
