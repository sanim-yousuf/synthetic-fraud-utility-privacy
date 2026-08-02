# When Synthetic Data Remembers: A Utility–Privacy Trade-off Framework for Diffusion-Based Minority-Class Augmentation

[![License: MIT](https://img.shields.io/badge/Code%20License-MIT-yellow.svg)](LICENSE)
[![License: CC BY 4.0](https://img.shields.io/badge/Manuscript%20License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://www.python.org/)
[![Journal](https://img.shields.io/badge/submitted%20to-Mach.%20Learn.%20Knowl.%20Extr.%20(MDPI)-informational.svg)](https://www.mdpi.com/journal/make)

This repository contains the experimental pipeline, result artifacts, and manuscript source for a study evaluating the **utility and membership-inference privacy cost of diffusion-based synthetic minority-class augmentation**, using imbalanced financial fraud detection as a case study.

---

## Abstract

Deep generative models are increasingly used to augment the minority class in imbalanced classification problems, on the premise that they capture nonlinear structure that classical resamplers such as SMOTE cannot. Evaluations of this practice, however, have almost universally reported classification utility in isolation, leaving open whether any utility gain justifies training a reusable generative artifact on a small number of sensitive, real records, and whether the answer depends on how much data is available. This study proposes and applies a general utility–privacy auditing framework for generative minority-class augmentation. A tabular denoising diffusion model is trained on the minority class of two fraud datasets differing in scale by roughly an order of magnitude (344 and 4,000 minority examples) and benchmarked against four classical and adversarial baselines across a five-point augmentation-ratio sweep, four downstream classifiers, and ten random seeds. Diffusion augmentation achieved no statistically significant utility advantage over SMOTE, yet its membership-inference vulnerability was severe on the smaller dataset (AUC = 0.913) and collapsed to near-random on the larger one (AUC = 0.524). Differentially private training recovered near-random risk at a tight privacy budget (ε = 7.91) with modest utility cost.

## Key Findings

| Question | Finding |
|---|---|
| Does diffusion augmentation improve classification utility over classical resampling? | No statistically significant advantage over SMOTE (Wilcoxon signed-rank test, *p* = 1.00) |
| Is the diffusion generator vulnerable to membership inference? | Severe on the smaller dataset (AUC = 0.913, loss-based attack); near-random on the larger dataset (AUC = 0.524) |
| Does privacy risk depend on training-set size? | Yes — leakage scales inversely with the number of real minority-class examples available to the generator |
| Can differential privacy mitigate this? | Yes — DP-SGD recovers near-random empirical leakage at ε = 7.91 with modest utility cost |

---

## Results Highlights

### Utility: diffusion vs. classical baselines (ULB dataset, XGBoost, *n* = 10 seeds)

| Method | Best Ratio | F1 | AUPRC | MCC |
|---|---|---|---|---|
| No augmentation | — | 0.846 ± 0.011 | 0.834 | 0.851 |
| Random oversampling | 10× | 0.841 ± 0.007 | 0.838 | 0.842 |
| SMOTE | 0.5× | 0.844 ± 0.012 | 0.827 | 0.849 |
| ADASYN | 0.5× | 0.839 ± 0.007 | 0.829 | 0.844 |
| **Diffusion (this study)** | **0.5×** | **0.845 ± 0.008** | **0.832** | **0.849** |
| CTGAN | 5× | 0.866 ± 0.007 | 0.832 | 0.868 |

A paired Wilcoxon signed-rank test found no significant difference between diffusion and SMOTE (*W* = 27.0, *p* = 1.00). See `figures/fig04_utility_sweep_ulb.png` for the full ratio sweep across all four classifiers, and `figures/fig05_seed_distribution_boxplot_ulb.png` for the seed-level spread.

### Privacy audit: membership inference AUC (0.5 = no leakage, 1.0 = total leakage)

| Generator | Attack | ULB (*n* = 344) | IEEE-CIS (*n* = 4,000) |
|---|---|---|---|
| Diffusion | Loss-based (white-box) | **0.913** | **0.524** |
| Diffusion | Nearest-neighbor distance (black-box) | 0.548 | 0.502 |
| CTGAN | Nearest-neighbor distance (black-box) | 0.500 | 0.506 |

> **Note:** CTGAN was only evaluated against the black-box attack, since the white-box loss-based attack exploits a per-sample denoising signal specific to score-based models. CTGAN's strong showing here should be read as "passed the audit we were able to run," not as a clean bill of health under a comparably strong, GAN-tailored white-box attack.

The headline result: increasing the diffusion model's training-set size roughly eleven-fold
