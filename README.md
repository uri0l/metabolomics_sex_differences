# Sex-associated Differences in Baseline Urinary Metabolites

> A computational metabolomics project developed for the Introduction to Statistical Learning course at the Universitat Politècnica de Barcelona (UPC), December 2025.

**Authors:** 
* Òscar Contreras
* [Eira Fontanals](https://github.com/eirafontanals)
* [Oriol Leal](https://github.com/uri0l)
* Marc J. Torres

[![R-Analysis](https://img.shields.io/badge/Language-R-blue.svg)](https://www.r-project.org/)
[![Bioinformatics](https://img.shields.io/badge/Field-Bioinformatics-green.svg)](https://en.wikipedia.org/wiki/Bioinformatics)
[![Machine Learning](https://img.shields.io/badge/Methods-PLS--DA%20%7C%20PCA-orange.svg)](https://en.wikipedia.org/wiki/Partial_least_squares_regression)

---

## Overview
 
This project reproduces and extends the analysis from [Fan et al. (2018)](https://doi.org/10.1038/s41598-018-29592-3), a *Scientific Reports* study that profiled urinary metabolites in 60 healthy males and 60 age-matched females using untargeted GC-TOF-MS. The original paper identified meaningful sex-associated differences in baseline urinary metabolism — findings with direct implications for biomarker discovery in bladder-related diseases such as interstitial cystitis (IC).
 
Using the supplementary data from that publication, we implemented a full machine learning pipeline in R to characterize the urinary metabolome by sex, identify the most discriminating metabolites, and evaluate the performance of a PLS-DA classifier with and without feature selection. This work was carried out as part of the *Introduction to Statistical Learning* course at Universitat Politècnica de Barcelona.
 
---
 
## Goals
 
### General Goal
Investigate whether biological sex influences baseline urinary metabolomic profiles in healthy adults, and assess the implications of these differences for urine-based biomarker discovery in bladder-related diseases.
 
### Specific Goals
- Characterize global urinary metabolomic patterns in healthy males and females using untargeted GC-TOF-MS data.
- Identify metabolites and metabolite classes that differ significantly between sexes using univariate and multivariate statistical methods.
- Evaluate whether sex-specific metabolic differences may act as confounding factors in disease biomarker studies.
- Provide a baseline metabolic reference to support sex-aware interpretation of urinary biomarkers, particularly for interstitial cystitis.
 
---
 
## Dataset
 
- **Source:** Supplementary materials from Fan et al. (2018), *Scientific Reports* 8, 11883.
- **Samples:** 121 total — 60 healthy females and 61 healthy males (age range: 45–65 years).
- **Features:** 414 urinary metabolites profiled via GC-TOF-MS.
- **Missing values:** None (pre-imputed in the original dataset using BinBase software).
 
All data files are located in the `data/` folder:
 
| File | Description |
|---|---|
| `table1.xlsx` | Supplementary Table 1 — raw metabolite intensity matrix |
| `table2.xlsx` | Supplementary Table 2 — sample metadata and annotations |
| `data.csv` | Merged and preprocessed table used as input for the analysis pipeline |
 
> The original supplementary tables and the merged dataset are derived from Fan et al. (2018) and are included here strictly for reproducibility purposes. Please refer to the original publication for full data provenance.
 
---
 
## Methods & Pipeline
 
### 1. Data Import & Preprocessing
- Transposition of the raw matrix (metabolites × samples → samples × metabolites).
- Extraction of the sex label from sample row names.
- Log₁₀ transformation (`log10(x + 1)`) to reduce right-skewness and improve normality.
 
### 2. Exploratory Data Analysis (EDA)
- Distribution plots before and after log transformation.
- Outlier detection using the IQR method (1.5 × IQR rule) across all metabolites.
- Principal Component Analysis (PCA) on scaled data to visualize global variance structure and assess sex separation in unsupervised space.
 
### 3. Supervised Classification: PLS-DA
- **Hold-out split:** 70% training / 30% test, stratified by sex.
- **Internal validation:** 5-fold cross-validation on the training set.
- **Univariate pre-filtering:** t-tests per metabolite with Benjamini–Hochberg FDR correction (α = 0.05) performed on training data only.
- **Model training:** Partial Least Squares Discriminant Analysis (PLS-DA) via the `mixOmics` package.
- **Hyperparameter optimization:** Optimal number of latent variables (components) selected using M-fold cross-validation (`perf()`) with BER and `max.dist` criteria.
 
### 4. Feature Selection
- **VIP scores:** Variable Importance in Projection scores computed per component and averaged; threshold VIP > 1.
- **Recursive Feature Elimination (RFE):** PLS-DA-based RFE iteratively removing the bottom 15% of features by average VIP score per iteration, evaluated via 10-fold CV. The feature subset yielding the best CV accuracy was selected.
- Final model retrained on RFE-selected features with re-optimized number of components.
 
### 5. Evaluation
- Confusion matrix, accuracy, balanced accuracy, sensitivity, and specificity on the held-out test set.
- AUROC computed using the `pROC` package.
- Head-to-head comparison of the full-feature model vs. the RFE-reduced model.
 
---
 
## Key Results
 
| Model | Features | Components | Test Accuracy | Balanced Accuracy | AUROC |
|---|---|---|---|---|---|
| Full feature model | 414 | 4 | 0.556 | 0.556 | 0.778 |
| RFE-selected model | ~60 | re-optimized | 0.667 | 0.667 | 0.762 |
 
- **PCA** showed no strong visual separation between sexes in the first two principal components, with ~40 components needed to explain 80% of the total variance — consistent with the high dimensionality and subtle sex signal in the data.
- **Univariate analysis** (after FDR correction) identified 4 significantly different metabolites between sexes: succinic acid, malic acid, alpha-ketoglutarate, and an unknown compound (X7402).
- **VIP analysis** from the full PLS-DA model flagged 149 features with VIP > 1, with succinic acid and malic acid consistently ranking among the top discriminators — in agreement with the original paper.
- **RFE reduced** the feature space by ~85% while improving test accuracy from 55.6% to 66.7%, demonstrating the value of feature selection in high-dimensional metabolomics.
- These findings support the original paper's conclusion that sex is a meaningful biological variable that should be accounted for in urinary biomarker studies.
 
---
 
## Repository Structure

> All figures generated by the pipeline are saved to the ```plots/``` folder and committed to the repository. If you encounter any issues running the code locally, you can browse the pre-generated outputs there directly.
 
```
.
├── metabolite_project_final.Rmd   # Full annotated R Markdown pipeline
├── data/                          # Input data files
│   ├── table1.xlsx                # Supplementary Table 1 from Fan et al. (2018)
│   ├── table2.xlsx                # Supplementary Table 2 from Fan et al. (2018)
│   └── data.csv                   # Merged dataset used as pipeline input
├── plots/                         # Output figures generated by the pipeline
│   ├── distribution_plots.png
│   ├── original_log10_transform.png
│   ├── pca_autoplot.png
│   ├── plsda_score_plot.png
│   ├── vip_scores.png
│   ├── rfe_results.png
│   ├── plsda_score_plot_final.png
│   ├── confusion_matrix_final.png
│   └── roc_curve_final.png
└── README.md
```
 
---
 
## Dependencies
 
This project was developed in R. The following packages are required:
 
```r
install.packages(c("ggplot2", "caret", "dplyr", "pROC", "class", "tidyr", "ggfortify"))
 
# mixOmics requires Bioconductor
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("mixOmics")
```
 
---
 
## How to Run
 
1. Clone this repository.
2. Ensure the `data/` folder contains `data.csv` (already included — see Dataset section).
3. Open `metabolite_project_final.Rmd` in RStudio.
4. Update the `read.csv()` path in the first chunk to `"data/data.csv"` if needed.
5. Knit to HTML or PDF, or run chunks interactively.
 
---
 
## Reference
 
Fan, S., Yeon, A., Shahid, M. et al. *Sex-associated differences in baseline urinary metabolites of healthy adults.* Sci Rep **8**, 11883 (2018). https://doi.org/10.1038/s41598-018-29592-3
