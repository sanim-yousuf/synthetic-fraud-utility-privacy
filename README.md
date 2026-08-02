# Utility–Privacy Trade-offs in Diffusion-Based Synthetic Data Augmentation for Imbalanced Financial Fraud Detection

[![Research](https://img.shields.io/badge/Research-Synthetic%20Data-blue)](#) [![Domain](https://img.shields.io/badge/Domain-FinTech%20%7C%20Fraud%20Detection-teal)](#) [![Method](https://img.shields.io/badge/Method-Diffusion%20Models-purple)](#) [![Privacy](https://img.shields.io/badge/Focus-Utility%20%2B%20Privacy-indigo)](#)

## Abstract

Financial fraud detection is inherently affected by severe class imbalance, where fraudulent transactions represent only a small fraction of observed financial activity. Synthetic data augmentation provides a potential solution by increasing the representation of minority-class transactions; however, improvements in predictive utility do not necessarily imply that the generated data are privacy-safe.

This study presents an empirical evaluation of **diffusion-based synthetic data augmentation for imbalanced financial fraud detection**, with particular emphasis on the interaction between **downstream utility, synthetic-data fidelity, and privacy risk**.

Rather than evaluating synthetic data solely according to classification performance, the study investigates whether increasing the amount of synthetic minority data produces consistent benefits and whether those benefits are accompanied by increased membership-inference risk.

The experimental framework combines diffusion-based generation with controlled augmentation-ratio experiments, downstream fraud-detection evaluation, fidelity analysis, membership-inference auditing, differential-privacy experiments, statistical testing, and cross-dataset validation.

The central research question is:

> **How much synthetic data is sufficient to improve fraud-detection utility, and what privacy risks emerge as synthetic-data generation becomes more aggressive?**

---

## Research Objectives

The study is designed around four primary objectives:

1. **Evaluate downstream utility** of diffusion-generated synthetic minority transactions for imbalanced fraud detection.
2. **Determine the effect of synthetic-data quantity** by systematically varying the synthetic augmentation ratio.
3. **Assess privacy risk** using membership-inference attacks and differential-privacy experiments.
4. **Examine generalizability** through controlled evaluation on an additional financial fraud dataset.

The study therefore treats synthetic-data generation as a **utility–privacy evaluation problem**, rather than assuming that synthetic data are automatically beneficial or privacy-preserving.

---

## Methodological Overview

```text
Real Financial Transaction Data
            │
            ▼
     Data Preprocessing
            │
            ├──────────────► Held-Out Test Set
            │                    │
            ▼                    │
   Training Data Only            │
            │                    │
            ▼                    │
 ┌─────────────────────────┐     │
 │ Synthetic Data Generation│     │
 │                         │     │
 │ • Diffusion Model       │     │
 │ • CTGAN                 │     │
 │ • Real-data Baseline    │     │
 └────────────┬────────────┘     │
              │                  │
              ▼                  │
      Synthetic Minority Data    │
              │                  │
              ▼                  │
    Controlled Augmentation      │
              │                  │
              ▼                  │
      Downstream Classifiers ◄───┘
              │
              ▼
       Utility Evaluation
              │
              ├──────────────► Statistical Testing
              │
              └──────────────► SHAP Sanity Check

Synthetic Data
      │
      ├──────────────► Fidelity Analysis
      │
      └──────────────► Privacy Audit
                         │
                         ├── Membership Inference
                         │
                         └── Differential Privacy
                                  │
                                  ▼
                         Architecture-Matched
                         Controlled Confirmation
```

---

## Core Methodology

### 1. Data Preparation

The experiments use financial transaction data characterized by substantial class imbalance.

The preprocessing pipeline is designed to prevent information leakage:

* preprocessing parameters are fitted using the training data;
* the held-out test set remains isolated;
* synthetic generators are trained using training data only;
* the test set is not used for synthetic-data generation or model selection.

This separation is maintained throughout the experimental workflow.

---

### 2. Synthetic Data Generation

The primary method investigated in this study is a **denoising diffusion probabilistic model (DDPM)** for generating minority-class financial transactions.

The diffusion generator employs a residual MLP-based denoising architecture with timestep information.

Conceptually:

```text
Minority Training Samples
          │
          ▼
   Forward Diffusion
          │
          ▼
 Progressive Gaussian
      Noise Addition
          │
          ▼
     Noisy Sample xt
          │
          ▼
 Residual MLP Denoiser
          │
          ▼
   Noise Prediction
          │
          ▼
   Reverse Diffusion
          │
          ▼
Synthetic Minority Sample
```

The repository also includes comparative experiments involving **CTGAN** and a real-data baseline.

---

### 3. Controlled Synthetic Augmentation

Rather than evaluating only one arbitrary synthetic-data quantity, the study systematically varies the amount of synthetic minority data introduced into the training set.

The augmentation experiments investigate multiple synthetic-data ratios, allowing the relationship between **synthetic-data quantity and downstream classification utility** to be examined empirically.

This design directly addresses:

> **Is more synthetic data necessarily better?**

---

### 4. Downstream Fraud Detection

Synthetic data are evaluated through their effect on fraud-detection models.

The experimental framework evaluates downstream classifiers using metrics appropriate for imbalanced classification:

* **F1-score**
* **PR-AUC**
* **ROC-AUC**
* **Accuracy**

Multiple random seeds are used to assess the stability of observed results.

Statistical comparisons are performed using the **Wilcoxon signed-rank test** where appropriate.

---

### 5. Synthetic-Data Fidelity

Synthetic samples are examined before drawing conclusions from downstream classification performance.

The fidelity analysis investigates how closely generated transactions resemble the underlying real minority-class distribution.

This is important because a synthetic dataset may produce useful classification results without necessarily reproducing realistic characteristics of the original financial data.

---

### 6. Privacy Risk Assessment

A central component of the study is the evaluation of whether synthetic-data generation introduces privacy risks.

#### Membership Inference

The study evaluates membership-inference risk using:

* **Black-box attacks**
* **White-box attacks**

These analyses examine whether an attacker can distinguish training members from non-members using information associated with the synthetic-data generation process.

---

### 7. Differential Privacy

Differential privacy is investigated as a mechanism for controlling privacy leakage.

The experiments investigate a range of privacy/noise settings rather than relying on a single arbitrary configuration.

Importantly, the study includes an **architecture-matched controlled confirmation experiment** to distinguish privacy effects from changes caused by using a different model architecture.

The controlled experiment is treated as the primary evidence when interpreting the utility–privacy relationship.

---

## Results Highlights

### 1. Synthetic augmentation does not imply monotonic utility gains

The controlled augmentation experiments indicate that increasing the amount of synthetic data should not automatically be interpreted as improving fraud-detection performance.

The study therefore supports a **quantity-sensitive evaluation framework** rather than the assumption that more synthetic minority samples are always beneficial.

---

### 2. Privacy risk can remain substantial despite strong downstream utility

The privacy analysis demonstrates an important distinction between:

**predictive utility** and **privacy protection**.

A synthetic-data generator can support competitive downstream fraud detection while still exhibiting measurable membership-inference vulnerability.

This motivates evaluating synthetic financial data along both dimensions rather than using classification performance as the sole criterion.

---

### 3. Architecture control materially affects the DP interpretation

The controlled same-architecture differential-privacy experiment provides an important methodological check.

At:

**ε = 7.91**

the architecture-matched experiment produced:

* **F1 = 0.7893**
* **Membership-Inference AUC = 0.5395**

For comparison, the corresponding undefended same-architecture setting produced:

* **F1 = 0.8452**
* **Membership-Inference AUC = 0.9133**

These results illustrate the central utility–privacy trade-off: stronger privacy protection can reduce downstream utility while substantially reducing membership-inference discrimination.

The manuscript therefore distinguishes this controlled result from the exploratory lightweight DP sweep.

---

### 4. Exploratory DP results provide additional evidence

The lightweight DP configuration at:

**ε = 7.91**

yielded:

* **F1 = 0.8401**
* **Membership-Inference AUC = 0.5227**

Because this configuration uses a different architecture, it is treated as an **exploratory result**, rather than as the primary architecture-controlled comparison.

This distinction reduces the risk of attributing architecture-related utility differences solely to differential privacy.

---

### 5. Utility, fidelity, and privacy should be evaluated jointly

The overall experimental results support a broader methodological conclusion:

> **Synthetic financial data should not be evaluated solely by how well they improve downstream fraud detection.**

A meaningful assessment should jointly consider:

* downstream predictive utility;
* synthetic-data fidelity;
* membership-inference vulnerability;
* privacy-preserving mechanisms;
* and the amount of synthetic data introduced into training.

---

## Experimental Design

| Component                          | Experimental Role                    |
| ---------------------------------- | ------------------------------------ |
| Real training data                 | Reference training distribution      |
| Held-out test set                  | Final downstream evaluation          |
| Diffusion model                    | Primary synthetic-data generator     |
| CTGAN                              | Comparative synthetic-data baseline  |
| Synthetic augmentation ratios      | Utility sensitivity analysis         |
| Multiple downstream classifiers    | Model-dependent utility assessment   |
| Fidelity analysis                  | Synthetic-data quality assessment    |
| Membership inference               | Privacy-risk assessment              |
| Differential privacy               | Privacy mitigation analysis          |
| Architecture-matched DP experiment | Controlled privacy confirmation      |
| SHAP                               | Post-hoc model-behavior sanity check |
| IEEE-CIS                           | Cross-dataset validation             |
| Multiple random seeds              | Experimental stability               |
| Wilcoxon signed-rank test          | Statistical comparison               |

---

## Research Perspective

A central premise of this study is that **synthetic-data quality should not be judged by predictive utility alone**.

A synthetic dataset can potentially:

* improve minority-class representation;
* produce competitive downstream performance;
* reproduce useful distributional characteristics;

while simultaneously creating privacy risks if generated samples retain information about original training records.

Therefore, the study evaluates synthetic financial data along multiple dimensions:

```text
                 Synthetic Data
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
      Utility        Fidelity       Privacy
        │              │              │
        ▼              ▼              ▼
   Fraud Detection  Distribution   Membership
     Performance     Similarity     Inference
                                      │
                                      ▼
                              Differential Privacy
```

This multidimensional perspective forms the central methodological motivation of the study.

---

## Cross-Dataset Validation

The experimental framework is additionally evaluated using the **IEEE-CIS Fraud Detection dataset**.

Because of computational considerations, the cross-dataset experiment uses a controlled working subset rather than reproducing the full-scale primary experiment.

The purpose is to examine whether the observed utility–privacy behavior extends beyond the primary experimental setting.

The cross-dataset analysis is therefore interpreted as **supporting external validation**, rather than as evidence that the observed relationships universally generalize across financial institutions or transaction systems.

---

## Explainability

SHAP-based analysis is included as a **post-hoc sanity check** of model behavior.

It is not treated as a primary contribution.

The purpose is to inspect whether downstream models rely on meaningful feature patterns after synthetic-data augmentation.

---

## Research Contributions

This study contributes:

1. **A controlled evaluation framework** for diffusion-based synthetic-data augmentation under severe class imbalance.
2. **A systematic analysis of synthetic-data quantity** and its relationship with downstream fraud-detection utility.
3. **A multidimensional synthetic-data assessment** incorporating utility, fidelity, and privacy.
4. **Membership-inference auditing** of synthetic financial data.
5. **Differential-privacy experiments** examining the utility–privacy relationship.
6. **Architecture-matched controlled analysis** to reduce confounding in the privacy evaluation.
7. **Cross-dataset validation** using an additional financial fraud dataset.
8. **A reproducible experimental protocol** documented through the accompanying research notebook.

---

## Reproducibility

The experimental notebook serves as the primary reference for reproducing the reported experiments.

For reproducibility, the experimental configuration should preserve:

* preprocessing configuration;
* model configuration;
* diffusion hyperparameters;
* random seeds;
* augmentation ratios;
* downstream classifier settings;
* privacy-attack configuration;
* differential-privacy settings;
* evaluation metrics;
* statistical-testing configuration.

Dataset files should not be redistributed when their licenses or access conditions prohibit redistribution.

---

## Data Availability

The repository does not redistribute restricted financial transaction datasets.

Researchers should obtain the respective datasets through their official access mechanisms and comply with all applicable licenses, terms of use, and privacy requirements.

The experimental code and configuration can be used with appropriately licensed versions of the datasets.

---

## Scientific Scope and Limitations

This repository supports an empirical investigation of diffusion-based synthetic-data augmentation for financial fraud detection.

The findings should not be interpreted as evidence that:

* synthetic data are inherently privacy-preserving;
* diffusion models universally outperform alternative generators;
* one privacy parameter provides a universally acceptable privacy guarantee;
* downstream predictive improvement necessarily implies better synthetic-data fidelity;
* results obtained on the evaluated datasets automatically generalize to all financial institutions or transaction systems.

The study instead emphasizes the importance of **jointly evaluating utility and privacy** when synthetic data are considered for financial machine-learning applications.

---

## License

This repository is released under the terms specified in the accompanying `LICENSE` file.

Dataset licenses and access restrictions remain separate from the software license of this repository.

---

## Disclaimer

This repository is intended for **academic and research purposes**.

The experimental results should not be interpreted as a production-ready financial fraud-detection system or as a guarantee of privacy for synthetic financial data.

Researchers adapting the methodology to real financial environments should conduct independent validation, privacy assessment, security review, and regulatory compliance analysis before deployment.

---

## Research Keywords

`Synthetic Data` · `Diffusion Models` · `Financial Fraud Detection` · `Imbalanced Learning` · `Data Augmentation` · `Privacy-Preserving Machine Learning` · `Membership Inference` · `Differential Privacy` · `Generative Models` · `FinTech` · `Trustworthy AI` · `Financial Machine Learning`
