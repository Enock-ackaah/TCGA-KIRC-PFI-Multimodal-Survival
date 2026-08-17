\# TCGA-KIRC Clinical and RNA-seq Survival Prediction



This repository contains a reproducible survival-analysis workflow for progression-free interval (PFI) prediction in TCGA Kidney Renal Clear Cell Carcinoma (TCGA-KIRC).



The study compares clinical-only, RNA-seq-only, and integrated clinical + RNA-seq survival models, with emphasis on predictive performance, calibration, robustness, and RNA feature-selection stability.



\## Study Objective



The primary objective is to determine whether adding high-dimensional RNA-seq information improves PFI prediction beyond routinely available clinical predictors.



Three modeling strategies were evaluated:



1\. Clinical-only Cox proportional hazards model

2\. RNA-only penalized Cox model

3\. Clinical + RNA multimodal penalized Cox model



\## Cohort



\- Cancer type: TCGA Kidney Renal Clear Cell Carcinoma (TCGA-KIRC)

\- PFI-eligible patients: 529

\- PFI events: 159

\- Censored patients: 370

\- RNA-seq profiles aligned with clinical data: 529



Clinical predictors included:



\- Age at index

\- Tumor grade

\- AJCC stage



RNA-seq modeling used protein-coding genes after prevalence filtering and training-fold-specific variable-gene selection.



Raw TCGA sequencing files are not distributed in this repository.



\## Validation Strategy



The primary reliability analysis used repeated nested cross-validation:



\- 10 repeats

\- 5 outer folds per repeat

\- 50 held-out evaluations per model



RNA preprocessing and feature selection were performed using training data within each outer fold to reduce information leakage.



Hyperparameter selection for penalized Cox models was conducted using inner cross-validation.



\## Main Results



\### Repeated outer-fold C-index



| Model | Mean C-index |

|---|---:|

| Clinical | 0.8120 |

| RNA | 0.7173 |

| Clinical + RNA | 0.7989 |



The clinical-only model achieved the highest average discrimination.



The mean paired difference between the Clinical + RNA and Clinical models was:



`-0.0131`



with a repeat-level 95% bootstrap confidence interval of approximately:



`\[-0.0188, -0.0062]`



\## Time-Dependent AUC



| Model | 1 year | 3 years | 5 years |

|---|---:|---:|---:|

| Clinical | 0.8551 | 0.8216 | 0.8197 |

| RNA | 0.7283 | 0.7357 | 0.7562 |

| Clinical + RNA | 0.8344 | 0.8132 | 0.8063 |



Clinical predictors remained strongest across all three PFI horizons.



\## Prediction Error



Mean Brier scores and integrated Brier score:



| Model | BS 1 year | BS 3 years | BS 5 years | IBS 1–5 years |

|---|---:|---:|---:|---:|

| Clinical | 0.0936 | 0.1344 | 0.1574 | 0.1310 |

| RNA | 0.1261 | 0.1738 | 0.1908 | 0.1695 |

| Clinical + RNA | 0.1046 | 0.1433 | 0.1647 | 0.1399 |



Lower values indicate lower prediction error.



\## Risk Stratification



Median-risk Kaplan-Meier analyses produced significant high- versus low-risk separation in all 10 repeats for all three model classes.



Mean log-rank chi-square statistics:



| Model | Mean log-rank chi-square |

|---|---:|

| Clinical | 76.24 |

| RNA | 64.95 |

| Clinical + RNA | 97.29 |



Although the clinical-only model had better overall discrimination and prediction error, the Clinical + RNA model showed particularly strong risk-group separation.



\## RNA Feature Stability



Across 50 repeated multimodal fits:



\- 148 unique RNA genes were selected at least once

\- Mean pairwise Jaccard similarity: 0.3251

\- No gene was selected in all 50 models

\- 6 genes were selected in at least 40 of 50 models



Frequently selected genes included:



\- SLC10A2

\- CXCL5

\- CLTRN

\- SLC6A19

\- SCIN

\- NDNF

\- G6PC

\- FABP7

\- PRAME

\- CAPN6



This indicates that predictive performance can be comparatively stable even when the exact molecular feature set varies across resampling.



\## RNA Feature-Count Sensitivity



The multimodal model was also evaluated using different numbers of retained RNA features:



| Top RNA features | Mean C-index |

|---:|---:|

| 100 | 0.7984 |

| 250 | 0.7989 |

| 500 | 0.7978 |



Performance changed very little across these settings, indicating that the main conclusion was not driven by the choice of 250 retained genes.



\## Calibration



Calibration was evaluated at 1, 3, and 5 years using out-of-fold predicted survival probabilities and observed Kaplan-Meier survival estimates.



The clinical-only model showed the lowest mean absolute calibration error overall.



\## Repository Structure



```text

TCGA-KIRC-PFI-Multimodal-Survival/

│

├── README.md

├── requirements.txt

├── .gitignore

│

├── 02\_notebooks/

│   ├── 01\_audit\_TCGA\_KIRC\_RNAseq.ipynb

│   ├── 03\_build\_TCGA\_KIRC\_expression\_matrix.ipynb

│   ├── 04\_prepare\_PFI\_modeling\_cohort.ipynb

│   ├── 05\_build\_PFI\_multimodal\_model.ipynb

│   └── 06\_repeated\_nested\_CV\_reliability.ipynb

│

├── 03\_processed\_data/

│   └── analysis summaries and model outputs

│

└── 04\_publication\_outputs/

&#x20;   ├── publication figures

&#x20;   └── final summary tables

