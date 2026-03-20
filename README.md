# Sex-associated Differences in Baseline Urinary Metabolites

[![R-Analysis](https://img.shields.io/badge/Language-R-blue.svg)](https://www.r-project.org/)
[![Bioinformatics](https://img.shields.io/badge/Field-Bioinformatics-green.svg)](https://en.wikipedia.org/wiki/Bioinformatics)
[![Machine Learning](https://img.shields.io/badge/Methods-PLS--DA%20%7C%20PCA-orange.svg)](https://en.wikipedia.org/wiki/Partial_least_squares_regression)

## 📌 Project Overview
This project explores the biological basis for gender variability in metabolomic profiles. Using a dataset of healthy individuals, we implemented a computational pipeline to identify whether urinary metabolic signatures are globally distinct between males and females. Establishing these baseline differences is crucial for advancing personalized medicine and improving disease risk factor assessments.

The study is based on the work by **Fan et al. (2018)**: *"Sex-associated differences in baseline urinary metabolites of healthy adults"*.

## 🧬 Biological Context
Metabolomics provides a functional readout of cellular activity. This analysis focuses on primary metabolism (TCA cycle, fatty acids, and amino acids) to identify sex-specific biomarkers. Key findings include:
* **Significant Upregulation in Males:** $\alpha$-ketoglutarate (2.3-fold) and 4-hydroxybutyric acid (4.41-fold).
* **Pathway Involvement:** Saturated fatty acids, TCA cycle components, and butyrates.

## 🛠️ Technical Pipeline
The analysis was performed in **R** using the following workflow:

1.  **Preprocessing:** Data transposition, log10 transformation to correct skewness, and IQR-based outlier detection.
2.  **Exploratory Data Analysis (EDA):** Principal Component Analysis (PCA) to visualize global variance.
3.  **Supervised Learning:** * Model: **Partial Least Squares Discriminant Analysis (PLS-DA)**.
    * Validation: 5-fold internal cross-validation and external hold-out validation.
    * Optimization: Latent variable (LV) selection based on error rate minimization.
4.  **Feature Selection:** * Calculated **VIP (Variable Importance in Projection)** scores.
    * Implemented **Recursive Feature Elimination (RFE)** to identify the most predictive metabolite subset.

## 📊 Key Results
* **Classification Accuracy:** The optimized PLS-DA model achieved a cross-validation accuracy of **~76%**.
* **Biomarker Discovery:** Identified a subset of **21 high-importance metabolites** (VIP > 1) that drive the separation between male and female metabolic profiles.
* **Separation:** PCA and PLS-DA score plots confirm distinct clustering based on biological sex.

## 📁 Repository Structure

```text
.
├── data/
│   └── data.csv                # Raw metabolomics dataset
├── scripts/
│   ├── metabolite_project.Rmd  # Main analysis pipeline (R Markdown)
│   └── functions.R             # (Optional) Helper functions
├── plots/
│   ├── pca_score_plot.png      # Global variance visualization
│   ├── plsda_vip_ranking.png   # Top 21 metabolite biomarkers
│   └── final_model_roc.png     # Model performance curve
├── docs/
│   └── project_report.pdf      # Detailed final report
├── .gitignore                  # Files to exclude (e.g., .Rhistory)
└── README.md                   # Project documentation and summary
```

* `data/`: Contains the metabolomics dataset (samples vs metabolites).
* `scripts/`: `metabolite_project_final.Rmd` - Full analysis code.
* `plots/`: Exported visualizations (PCA, PLS-DA scores, VIP rankings).
* `README.md`: Project documentation.

## 🚀 Getting Started
### Prerequisites
You will need R installed with the following packages:
```r
install.packages(c("ggplot2", "caret", "dplyr", "pROC", "tidyr", "ggfortify"))
if (!require("BiocManager", quietly = TRUE)) install.packages("BiocManager")
BiocManager::install("mixOmics")
