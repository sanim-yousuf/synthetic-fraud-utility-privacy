# Revision results (full_paper)

## hyperparameters.csv

| Component           | Item                  | Setting                                                                                         |
|:--------------------|:----------------------|:------------------------------------------------------------------------------------------------|
| Diffusion           | Noise schedule        | linear, T = 200, β from 0.0001 to 0.02                                                          |
| Diffusion           | Denoiser              | residual MLP, width 256, 4 blocks, time embedding 128, LayerNorm, SiLU                          |
| Diffusion           | Optimiser             | Adam, learning rate 0.001, batch size 64, EMA decay 0.995                                       |
| Diffusion           | Stopping rule (Eq. 7) | window w = max(20, cap/20), tolerance 0.02, floor 800 (ULB) / 500 (IEEE-CIS), cap 3000 / 1500   |
| Diffusion           | Loss attack           | 20 (t, ε) draws per record                                                                      |
| CTGAN               | Architecture          | embedding 128, generator (256, 256), discriminator (256, 256), pac 10                           |
| CTGAN               | Training              | 100 epochs, batch 500, Adam lr 0.0002 (both), weight decay 1e-06                                |
| SMOTE / ADASYN      | Neighbours            | k = 5, real majority reference of 20,000 records                                                |
| Logistic regression | Settings              | L2, C = 1.0, lbfgs, max_iter 2000                                                               |
| Random forest       | Settings              | 100 trees, max depth 8, min samples per leaf 5                                                  |
| XGBoost             | Settings              | 100 trees, depth 6, learning rate 0.1, subsample 0.8, column subsample 0.8                      |
| MLP                 | Settings              | hidden (64, 32), ReLU, Adam lr 0.001, 30 epochs, batch 2048, pos_weight = n_neg/n_pos           |
| All classifiers     | Decision threshold    | 0.5 (fixed) and the validation-tuned threshold                                                  |
| DP-SGD              | Settings              | clip C = 1.0, δ = 1e-05, σ ∈ [0.5, 1.0, 2.0, 4.0], 250 epochs, Poisson sampling, RDP accountant |
| Protocol            | Seeds                 | ULB: 3 selection + 10 confirmation; IEEE-CIS: 2 + 5; DP: 5; scale study: 3                      |

## ieee_paired_tests.csv

| Metric   | Comparison                   | Mean difference [95% CI]   |   W |     p |   p (Holm) |   n |
|:---------|:-----------------------------|:---------------------------|----:|------:|-----------:|----:|
| F1       | Diffusion vs No augmentation | -0.013 [-0.015, -0.009]    |   0 | 0.062 |      0.312 |   5 |
| F1       | Diffusion vs ROS             | -0.054 [-0.056, -0.052]    |   0 | 0.062 |      0.312 |   5 |
| F1       | Diffusion vs SMOTE           | -0.030 [-0.032, -0.029]    |   0 | 0.062 |      0.312 |   5 |
| F1       | Diffusion vs ADASYN          | -0.023 [-0.027, -0.018]    |   0 | 0.062 |      0.312 |   5 |
| F1       | Diffusion vs CTGAN           | +0.002 [-0.000, +0.004]    |   3 | 0.312 |      0.312 |   5 |
| F1       | CTGAN vs No augmentation     | -0.014 [-0.017, -0.012]    |   0 | 0.062 |      0.25  |   5 |
| F1       | CTGAN vs SMOTE               | -0.032 [-0.035, -0.028]    |   0 | 0.062 |      0.25  |   5 |
| F1       | CTGAN vs ROS                 | -0.056 [-0.059, -0.053]    |   0 | 0.062 |      0.25  |   5 |
| F1       | CTGAN vs ADASYN              | -0.025 [-0.030, -0.019]    |   0 | 0.062 |      0.25  |   5 |
| AUPRC    | Diffusion vs No augmentation | -0.014 [-0.016, -0.013]    |   0 | 0.062 |      0.312 |   5 |
| AUPRC    | Diffusion vs ROS             | -0.012 [-0.015, -0.010]    |   0 | 0.062 |      0.312 |   5 |
| AUPRC    | Diffusion vs SMOTE           | +0.014 [+0.012, +0.016]    |   0 | 0.062 |      0.312 |   5 |
| AUPRC    | Diffusion vs ADASYN          | +0.021 [+0.019, +0.024]    |   0 | 0.062 |      0.312 |   5 |
| AUPRC    | Diffusion vs CTGAN           | -0.000 [-0.002, +0.002]    |   6 | 0.812 |      0.812 |   5 |

## ieee_privacy.csv

| Method           | Released artefact              | Attack                        | AUC mean ± SD   | 95% CI      | Exact copies of members   |   Seeds |
|:-----------------|:-------------------------------|:------------------------------|:----------------|:------------|:--------------------------|--------:|
| Diffusion (DDPM) | Generator (white-box)          | Loss                          | 0.521 ± 0.001   | 0.507–0.536 | nan                       |       5 |
| ROS              | Synthetic set (8,000 records)  | Nearest neighbour (k=1)       | 0.933 ± 0.003   | 0.930–0.936 | 83.2%                     |       5 |
| SMOTE            | Synthetic set (20,000 records) | Nearest neighbour (k=1)       | 0.871 ± 0.004   | 0.863–0.879 | 0.0%                      |       5 |
| ADASYN           | Synthetic set (20,000 records) | Nearest neighbour (k=1)       | 0.795 ± 0.002   | 0.786–0.805 | 0.0%                      |       5 |
| CTGAN            | Synthetic set (4,000 records)  | Nearest neighbour (k=1)       | 0.506 ± 0.001   | 0.492–0.520 | 0.0%                      |       5 |
| Diffusion (DDPM) | Synthetic set (4,000 records)  | Nearest neighbour (k=1)       | 0.503 ± 0.001   | 0.489–0.518 | 0.0%                      |       5 |
| No augmentation  | Classifier (XGBoost)           | Confidence (classifier-level) | 0.557 ± 0.002   | 0.542–0.572 | nan                       |       5 |
| ROS              | Classifier (XGBoost)           | Confidence (classifier-level) | 0.558 ± 0.002   | 0.543–0.573 | nan                       |       5 |
| SMOTE            | Classifier (XGBoost)           | Confidence (classifier-level) | 0.538 ± 0.000   | 0.523–0.553 | nan                       |       5 |
| ADASYN           | Classifier (XGBoost)           | Confidence (classifier-level) | 0.539 ± 0.001   | 0.524–0.554 | nan                       |       5 |
| CTGAN            | Classifier (XGBoost)           | Confidence (classifier-level) | 0.543 ± 0.001   | 0.528–0.557 | nan                       |       5 |
| Diffusion (DDPM) | Classifier (XGBoost)           | Confidence (classifier-level) | 0.542 ± 0.001   | 0.527–0.556 | nan                       |       5 |

## ieee_utility_all_classifiers.csv

| classifier         | method    |   ratio |       f1 |       f1_sd |    auprc |    auprc_sd |      mcc |      mcc_sd |
|:-------------------|:----------|--------:|---------:|------------:|---------:|------------:|---------:|------------:|
| LogisticRegression | adasyn    |     2   | 0.49376  | 0.000740638 | 0.514496 | 0.000432149 | 0.433749 | 0.000635097 |
| LogisticRegression | ctgan     |     5   | 0.385413 | 0.00825058  | 0.439715 | 0.0120241   | 0.355889 | 0.0124552   |
| LogisticRegression | diffusion |     2   | 0.450569 | 0.00982104  | 0.457471 | 0.00839956  | 0.400142 | 0.00704941  |
| LogisticRegression | none      |     0   | 0.388547 | 0           | 0.523251 | 0           | 0.404932 | 0           |
| LogisticRegression | ros       |     2   | 0.477689 | 0.00164804  | 0.513668 | 0.000916592 | 0.418585 | 0.00242143  |
| LogisticRegression | smote     |     2   | 0.481873 | 0.00234098  | 0.517498 | 0.0011709   | 0.423196 | 0.00289946  |
| MLP                | adasyn    |     0.5 | 0.479872 | 0.00977836  | 0.610238 | 0.00433401  | 0.420371 | 0.00906067  |
| MLP                | ctgan     |     1   | 0.549805 | 0.0097127   | 0.606546 | 0.00495812  | 0.486789 | 0.0113991   |
| MLP                | diffusion |     2   | 0.501567 | 0.0261793   | 0.56943  | 0.0208834   | 0.431764 | 0.0283428   |
| MLP                | none      |     0   | 0.497746 | 0.0209038   | 0.615403 | 0.00297487  | 0.437642 | 0.0180178   |
| MLP                | ros       |     1   | 0.502551 | 0.0255114   | 0.615956 | 0.00445259  | 0.441219 | 0.0240883   |
| MLP                | smote     |     2   | 0.507612 | 0.0219145   | 0.612835 | 0.00384844  | 0.443754 | 0.0206252   |
| RandomForest       | adasyn    |     5   | 0.554318 | 0.00589573  | 0.628439 | 0.0019139   | 0.491603 | 0.00656006  |
| RandomForest       | ctgan     |     0.5 | 0.435625 | 0.00620403  | 0.609396 | 0.00251601  | 0.479236 | 0.00467857  |
| RandomForest       | diffusion |     0.5 | 0.421248 | 0.00655155  | 0.600677 | 0.00248583  | 0.469104 | 0.00492116  |
| RandomForest       | none      |     0   | 0.468704 | 0.00357315  | 0.633754 | 0.000877682 | 0.50382  | 0.0044424   |
| RandomForest       | ros       |     2   | 0.578562 | 0.00316744  | 0.638576 | 0.00230557  | 0.534891 | 0.00398537  |
| RandomForest       | smote     |     5   | 0.554467 | 0.00127154  | 0.631128 | 0.00198269  | 0.491625 | 0.00157668  |
| XGBoost            | adasyn    |     5   | 0.630199 | 0.0031983   | 0.69731  | 0.00177817  | 0.596237 | 0.00319265  |
| XGBoost            | ctgan     |     1   | 0.605685 | 0.0041384   | 0.71874  | 0.00303128  | 0.60076  | 0.00435889  |
| XGBoost            | diffusion |     1   | 0.607273 | 0.00303283  | 0.718624 | 0.00191871  | 0.603627 | 0.00289328  |
| XGBoost            | none      |     0   | 0.619818 | 0.00268514  | 0.732787 | 0.00146126  | 0.613415 | 0.00254247  |
| XGBoost            | ros       |     2   | 0.661353 | 0.0031976   | 0.730725 | 0.00316139  | 0.619744 | 0.00344498  |
| XGBoost            | smote     |     5   | 0.6375   | 0.00404887  | 0.704802 | 0.00215144  | 0.604592 | 0.00374225  |

## ieee_utility_primary.csv

| Method           | Selected ratio r*   | F1 (0.5)      | F1 (tuned)    | AUPRC         | MCC (0.5)     | ROC-AUC       |   Seeds |
|:-----------------|:--------------------|:--------------|:--------------|:--------------|:--------------|:--------------|--------:|
| No augmentation  | none                | 0.620 ± 0.003 | 0.667 ± 0.002 | 0.733 ± 0.001 | 0.613 ± 0.003 | 0.904 ± 0.001 |       5 |
| ROS              | 2×                  | 0.661 ± 0.003 | 0.659 ± 0.005 | 0.731 ± 0.003 | 0.620 ± 0.003 | 0.905 ± 0.001 |       5 |
| SMOTE            | 5×                  | 0.638 ± 0.004 | 0.642 ± 0.003 | 0.705 ± 0.002 | 0.605 ± 0.004 | 0.893 ± 0.001 |       5 |
| ADASYN           | 5×                  | 0.630 ± 0.003 | 0.634 ± 0.004 | 0.697 ± 0.002 | 0.596 ± 0.003 | 0.891 ± 0.001 |       5 |
| CTGAN            | 1×                  | 0.606 ± 0.004 | 0.652 ± 0.005 | 0.719 ± 0.003 | 0.601 ± 0.004 | 0.898 ± 0.001 |       5 |
| Diffusion (DDPM) | 1×                  | 0.607 ± 0.003 | 0.651 ± 0.005 | 0.719 ± 0.002 | 0.604 ± 0.003 | 0.898 ± 0.001 |       5 |

## scale_results.csv

| Dataset   |   Training frauds n |   Non-members | Training-loss rule: AUC   | Training-loss rule: epoch   | Held-out-loss early stopping: AUC   | Held-out-loss early stopping: epoch   | Fixed 2250 epochs: AUC   |   Seeds |
|:----------|--------------------:|--------------:|:--------------------------|:----------------------------|:------------------------------------|:--------------------------------------|:-------------------------|--------:|
| IEEE-CIS  |                 344 |          2400 | 0.739 ± 0.010             | 2100 ± 212                  | 0.630 ± 0.039                       | 392 ± 236                             | 0.737 ± 0.011            |       3 |
| IEEE-CIS  |                1000 |          2400 | 0.613 ± 0.004             | 1900 ± 87                   | 0.605 ± 0.003                       | 1333 ± 123                            | 0.621 ± 0.001            |       3 |
| IEEE-CIS  |                2000 |          2400 | 0.556 ± 0.008             | 1750 ± 87                   | 0.556 ± 0.014                       | 1717 ± 772                            | 0.560 ± 0.008            |       3 |
| IEEE-CIS  |                4000 |          2400 | 0.526 ± 0.001             | 1450 ± 87                   | 0.527 ± 0.001                       | 1908 ± 311                            | 0.528 ± 0.001            |       3 |
| ULB       |                 275 |           148 | 0.927 ± 0.006             | 1750 ± 229                  | 0.754 ± 0.024                       | 183 ± 38                              | 0.933 ± 0.001            |       3 |

## temporal_paired_tests.csv

| Metric   | Comparison                   | Mean difference [95% CI]   |   W |     p |   p (Holm) |   n |
|:---------|:-----------------------------|:---------------------------|----:|------:|-----------:|----:|
| F1       | Diffusion vs No augmentation | +0.040 [+0.025, +0.059]    |   0 | 0.062 |      0.312 |   5 |
| F1       | Diffusion vs ROS             | +0.013 [-0.008, +0.034]    |   5 | 0.625 |      0.625 |   5 |
| F1       | Diffusion vs SMOTE           | +0.021 [+0.001, +0.035]    |   1 | 0.125 |      0.375 |   5 |
| F1       | Diffusion vs ADASYN          | +0.023 [+0.012, +0.033]    |   0 | 0.062 |      0.312 |   5 |
| F1       | Diffusion vs CTGAN           | +0.010 [-0.002, +0.022]    |   3 | 0.312 |      0.625 |   5 |
| F1       | CTGAN vs No augmentation     | +0.030 [+0.015, +0.045]    |   0 | 0.062 |      0.25  |   5 |
| F1       | CTGAN vs SMOTE               | +0.011 [-0.002, +0.027]    |   3 | 0.312 |      0.625 |   5 |
| F1       | CTGAN vs ROS                 | +0.003 [-0.022, +0.030]    |   6 | 0.812 |      0.812 |   5 |
| F1       | CTGAN vs ADASYN              | +0.013 [-0.000, +0.028]    |   2 | 0.188 |      0.562 |   5 |
| AUPRC    | Diffusion vs No augmentation | +0.045 [+0.036, +0.058]    |   0 | 0.062 |      0.312 |   5 |
| AUPRC    | Diffusion vs ROS             | +0.007 [+0.002, +0.013]    |   1 | 0.125 |      0.375 |   5 |
| AUPRC    | Diffusion vs SMOTE           | +0.003 [-0.004, +0.010]    |   5 | 0.625 |      1     |   5 |
| AUPRC    | Diffusion vs ADASYN          | +0.006 [+0.002, +0.010]    |   0 | 0.062 |      0.312 |   5 |
| AUPRC    | Diffusion vs CTGAN           | +0.001 [-0.004, +0.007]    |   7 | 1     |      1     |   5 |

## temporal_privacy.csv

| Method           | Released artefact             | Attack                        | AUC mean ± SD   | 95% CI      | Exact copies of members   |   Seeds |
|:-----------------|:------------------------------|:------------------------------|:----------------|:------------|:--------------------------|--------:|
| Diffusion (DDPM) | Generator (white-box)         | Loss                          | 1.000 ± 0.000   | 0.999–1.000 | nan                       |       5 |
| ROS              | Synthetic set (384 records)   | Nearest neighbour (k=1)       | 0.906 ± 0.016   | 0.888–0.924 | 65.6%                     |       5 |
| SMOTE            | Synthetic set (3,840 records) | Nearest neighbour (k=1)       | 0.997 ± 0.002   | 0.995–0.999 | 7.8%                      |       5 |
| ADASYN           | Synthetic set (192 records)   | Nearest neighbour (k=1)       | 0.485 ± 0.010   | 0.438–0.536 | 0.0%                      |       5 |
| CTGAN            | Synthetic set (3,840 records) | Nearest neighbour (k=1)       | 0.386 ± 0.042   | 0.346–0.428 | 0.0%                      |       5 |
| Diffusion (DDPM) | Synthetic set (192 records)   | Nearest neighbour (k=1)       | 0.384 ± 0.009   | 0.343–0.434 | 0.0%                      |       5 |
| No augmentation  | Classifier (XGBoost)          | Confidence (classifier-level) | 0.651 ± 0.008   | 0.593–0.708 | nan                       |       5 |
| ROS              | Classifier (XGBoost)          | Confidence (classifier-level) | 0.570 ± 0.011   | 0.493–0.647 | nan                       |       5 |
| SMOTE            | Classifier (XGBoost)          | Confidence (classifier-level) | 0.803 ± 0.024   | 0.763–0.841 | nan                       |       5 |
| ADASYN           | Classifier (XGBoost)          | Confidence (classifier-level) | 0.713 ± 0.011   | 0.661–0.767 | nan                       |       5 |
| CTGAN            | Classifier (XGBoost)          | Confidence (classifier-level) | 0.726 ± 0.036   | 0.680–0.770 | nan                       |       5 |
| Diffusion (DDPM) | Classifier (XGBoost)          | Confidence (classifier-level) | 0.728 ± 0.015   | 0.679–0.778 | nan                       |       5 |

## temporal_utility_all_classifiers.csv

| classifier   | method    |   ratio |       f1 |      f1_sd |    auprc |   auprc_sd |      mcc |      mcc_sd |
|:-------------|:----------|--------:|---------:|-----------:|---------:|-----------:|---------:|------------:|
| XGBoost      | adasyn    |     0.5 | 0.793883 | 0.00333471 | 0.7941   | 0.00420083 | 0.805766 | 0.000635655 |
| XGBoost      | ctgan     |    10   | 0.806947 | 0.0145949  | 0.798816 | 0.00649017 | 0.811151 | 0.0152737   |
| XGBoost      | diffusion |     0.5 | 0.816953 | 0.0130972  | 0.800156 | 0.00175185 | 0.826975 | 0.0108264   |
| XGBoost      | none      |     0   | 0.777155 | 0.0244839  | 0.754961 | 0.0150394  | 0.791525 | 0.0213864   |
| XGBoost      | ros       |     1   | 0.803869 | 0.0190885  | 0.79285  | 0.00675913 | 0.807735 | 0.020458    |
| XGBoost      | smote     |    10   | 0.79549  | 0.0126316  | 0.797194 | 0.0100088  | 0.799873 | 0.0126974   |

## temporal_utility_primary.csv

| Method           | Selected ratio r*   | F1 (0.5)      | F1 (tuned)    | AUPRC         | MCC (0.5)     | ROC-AUC       |   Seeds |
|:-----------------|:--------------------|:--------------|:--------------|:--------------|:--------------|:--------------|--------:|
| No augmentation  | none                | 0.777 ± 0.024 | 0.760 ± 0.018 | 0.755 ± 0.015 | 0.792 ± 0.021 | 0.978 ± 0.002 |       5 |
| ROS              | 1×                  | 0.804 ± 0.019 | 0.809 ± 0.019 | 0.793 ± 0.007 | 0.808 ± 0.020 | 0.981 ± 0.004 |       5 |
| SMOTE            | 10×                 | 0.795 ± 0.013 | 0.809 ± 0.032 | 0.797 ± 0.010 | 0.800 ± 0.013 | 0.982 ± 0.002 |       5 |
| ADASYN           | 0.5×                | 0.794 ± 0.003 | 0.790 ± 0.010 | 0.794 ± 0.004 | 0.806 ± 0.001 | 0.984 ± 0.002 |       5 |
| CTGAN            | 10×                 | 0.807 ± 0.015 | 0.766 ± 0.020 | 0.799 ± 0.006 | 0.811 ± 0.015 | 0.982 ± 0.004 |       5 |
| Diffusion (DDPM) | 0.5×                | 0.817 ± 0.013 | 0.800 ± 0.020 | 0.800 ± 0.002 | 0.827 ± 0.011 | 0.982 ± 0.001 |       5 |

## ulb_dp.csv

| Denoiser                        | Noise multiplier σ   | ε (δ=1e-5)   | F1            | AUPRC         | Loss-attack AUC   | NN-attack AUC   |   Seeds |
|:--------------------------------|:---------------------|:-------------|:--------------|:--------------|:------------------|:----------------|--------:|
| Primary (LayerNorm, 4 blocks)   | no DP                | ∞            | 0.840 ± 0.011 | 0.830 ± 0.007 | 0.795 ± 0.007     | 0.501 ± 0.004   |       5 |
| Primary (LayerNorm, 4 blocks)   | 0.5                  | 311.72       | 0.843 ± 0.008 | 0.833 ± 0.001 | 0.567 ± 0.007     | 0.491 ± 0.005   |       5 |
| Primary (LayerNorm, 4 blocks)   | 1.0                  | 63.96        | 0.846 ± 0.009 | 0.830 ± 0.006 | 0.554 ± 0.008     | 0.491 ± 0.006   |       5 |
| Primary (LayerNorm, 4 blocks)   | 2.0                  | 19.99        | 0.852 ± 0.007 | 0.832 ± 0.007 | 0.546 ± 0.013     | 0.490 ± 0.009   |       5 |
| Primary (LayerNorm, 4 blocks)   | 4.0                  | 7.91         | 0.844 ± 0.011 | 0.833 ± 0.003 | 0.546 ± 0.018     | 0.484 ± 0.011   |       5 |
| Lightweight (no norm, 2 blocks) | no DP                | ∞            | 0.844 ± 0.006 | 0.828 ± 0.005 | 0.594 ± 0.002     | 0.491 ± 0.003   |       5 |
| Lightweight (no norm, 2 blocks) | 0.5                  | 311.72       | 0.850 ± 0.007 | 0.829 ± 0.005 | 0.528 ± 0.005     | 0.487 ± 0.002   |       5 |
| Lightweight (no norm, 2 blocks) | 1.0                  | 63.96        | 0.839 ± 0.009 | 0.831 ± 0.003 | 0.516 ± 0.007     | 0.484 ± 0.004   |       5 |
| Lightweight (no norm, 2 blocks) | 2.0                  | 19.99        | 0.843 ± 0.008 | 0.833 ± 0.004 | 0.510 ± 0.011     | 0.481 ± 0.005   |       5 |
| Lightweight (no norm, 2 blocks) | 4.0                  | 7.91         | 0.848 ± 0.010 | 0.833 ± 0.005 | 0.514 ± 0.013     | 0.479 ± 0.008   |       5 |

## ulb_paired_tests.csv

| Metric   | Comparison                   | Mean difference [95% CI]   |   W |     p |   p (Holm) |   n |
|:---------|:-----------------------------|:---------------------------|----:|------:|-----------:|----:|
| F1       | Diffusion vs No augmentation | +0.005 [-0.003, +0.012]    |  14 | 0.193 |      0.58  |  10 |
| F1       | Diffusion vs ROS             | +0.003 [-0.004, +0.010]    |  17 | 0.322 |      0.645 |  10 |
| F1       | Diffusion vs SMOTE           | +0.009 [+0.003, +0.016]    |   5 | 0.02  |      0.098 |  10 |
| F1       | Diffusion vs ADASYN          | +0.012 [+0.002, +0.020]    |   7 | 0.037 |      0.148 |  10 |
| F1       | Diffusion vs CTGAN           | -0.002 [-0.008, +0.003]    |  19 | 0.432 |      0.645 |  10 |
| F1       | CTGAN vs No augmentation     | +0.008 [-0.001, +0.016]    |  13 | 0.16  |      0.32  |  10 |
| F1       | CTGAN vs SMOTE               | +0.012 [+0.005, +0.018]    |   3 | 0.01  |      0.039 |  10 |
| F1       | CTGAN vs ROS                 | +0.006 [-0.004, +0.016]    |  16 | 0.496 |      0.496 |  10 |
| F1       | CTGAN vs ADASYN              | +0.014 [+0.004, +0.024]    |   8 | 0.049 |      0.146 |  10 |
| AUPRC    | Diffusion vs No augmentation | -0.003 [-0.007, +0.003]    |  15 | 0.232 |      0.697 |  10 |
| AUPRC    | Diffusion vs ROS             | -0.008 [-0.011, -0.003]    |   4 | 0.014 |      0.068 |  10 |
| AUPRC    | Diffusion vs SMOTE           | -0.002 [-0.007, +0.002]    |  26 | 0.922 |      0.922 |  10 |
| AUPRC    | Diffusion vs ADASYN          | +0.002 [-0.003, +0.007]    |  18 | 0.375 |      0.75  |  10 |
| AUPRC    | Diffusion vs CTGAN           | -0.006 [-0.011, -0.001]    |   7 | 0.037 |      0.148 |  10 |

## ulb_privacy.csv

| Method           | Released artefact             | Attack                        | AUC mean ± SD   | 95% CI      | Exact copies of members   |   Seeds |
|:-----------------|:------------------------------|:------------------------------|:----------------|:------------|:--------------------------|--------:|
| Diffusion (DDPM) | Generator (white-box)         | Loss                          | 0.919 ± 0.003   | 0.881–0.950 | nan                       |      10 |
| ROS              | Synthetic set (344 records)   | Nearest neighbour (k=1)       | 0.812 ± 0.010   | 0.786–0.836 | 65.1%                     |      10 |
| SMOTE            | Synthetic set (1,720 records) | Nearest neighbour (k=1)       | 0.917 ± 0.006   | 0.885–0.944 | 4.3%                      |      10 |
| ADASYN           | Synthetic set (172 records)   | Nearest neighbour (k=1)       | 0.517 ± 0.006   | 0.465–0.570 | 1.2%                      |      10 |
| CTGAN            | Synthetic set (3,440 records) | Nearest neighbour (k=1)       | 0.508 ± 0.014   | 0.459–0.556 | 0.0%                      |      10 |
| Diffusion (DDPM) | Synthetic set (172 records)   | Nearest neighbour (k=1)       | 0.518 ± 0.005   | 0.467–0.570 | 0.0%                      |      10 |
| No augmentation  | Classifier (XGBoost)          | Confidence (classifier-level) | 0.583 ± 0.016   | 0.528–0.636 | nan                       |      10 |
| ROS              | Classifier (XGBoost)          | Confidence (classifier-level) | 0.634 ± 0.007   | 0.582–0.685 | nan                       |      10 |
| SMOTE            | Classifier (XGBoost)          | Confidence (classifier-level) | 0.610 ± 0.012   | 0.560–0.659 | nan                       |      10 |
| ADASYN           | Classifier (XGBoost)          | Confidence (classifier-level) | 0.605 ± 0.012   | 0.550–0.658 | nan                       |      10 |
| CTGAN            | Classifier (XGBoost)          | Confidence (classifier-level) | 0.601 ± 0.014   | 0.551–0.649 | nan                       |      10 |
| Diffusion (DDPM) | Classifier (XGBoost)          | Confidence (classifier-level) | 0.607 ± 0.008   | 0.554–0.658 | nan                       |      10 |

## ulb_utility_all_classifiers.csv

| classifier         | method    |   ratio |       f1 |      f1_sd |    auprc |   auprc_sd |      mcc |     mcc_sd |
|:-------------------|:----------|--------:|---------:|-----------:|---------:|-----------:|---------:|-----------:|
| LogisticRegression | adasyn    |     2   | 0.726688 | 0.00473796 | 0.688344 | 0.00140858 | 0.726544 | 0.00465863 |
| LogisticRegression | ctgan     |     1   | 0.707141 | 0.0103122  | 0.700549 | 0.00867654 | 0.715738 | 0.011359   |
| LogisticRegression | diffusion |     2   | 0.761698 | 0.00376964 | 0.718398 | 0.00431727 | 0.761442 | 0.00372968 |
| LogisticRegression | none      |     0   | 0.713725 | 0          | 0.707952 | 0          | 0.722739 | 0          |
| LogisticRegression | ros       |     5   | 0.759986 | 0.00547004 | 0.718857 | 0.00121439 | 0.759587 | 0.00547879 |
| LogisticRegression | smote     |     5   | 0.76617  | 0.00416936 | 0.719478 | 0.00165934 | 0.765772 | 0.00417743 |
| MLP                | adasyn    |     5   | 0.563891 | 0.0259727  | 0.731862 | 0.0280706  | 0.589339 | 0.0211661  |
| MLP                | ctgan     |     5   | 0.540256 | 0.0610602  | 0.762502 | 0.0166863  | 0.57609  | 0.0474038  |
| MLP                | diffusion |     5   | 0.384083 | 0.0506633  | 0.706321 | 0.0208042  | 0.449837 | 0.038441   |
| MLP                | none      |     0   | 0.284753 | 0.0391844  | 0.711929 | 0.0286211  | 0.377699 | 0.030295   |
| MLP                | ros       |    10   | 0.613097 | 0.0216834  | 0.765442 | 0.0313962  | 0.630816 | 0.0182129  |
| MLP                | smote     |    10   | 0.591284 | 0.0382034  | 0.745777 | 0.037295   | 0.612596 | 0.0302558  |
| RandomForest       | adasyn    |     1   | 0.840739 | 0.00654984 | 0.821784 | 0.00255898 | 0.844162 | 0.00710114 |
| RandomForest       | ctgan     |     1   | 0.815821 | 0.00891336 | 0.802801 | 0.0041367  | 0.819721 | 0.00978206 |
| RandomForest       | diffusion |     1   | 0.841568 | 0.00510359 | 0.81651  | 0.00280994 | 0.846139 | 0.00547152 |
| RandomForest       | none      |     0   | 0.828768 | 0.00815192 | 0.807526 | 0.00197597 | 0.8347   | 0.0079387  |
| RandomForest       | ros       |    10   | 0.82771  | 0.00664255 | 0.816088 | 0.00330919 | 0.827815 | 0.00667803 |
| RandomForest       | smote     |     5   | 0.832814 | 0.00363409 | 0.816607 | 0.00497003 | 0.833136 | 0.0037224  |
| XGBoost            | adasyn    |     0.5 | 0.833551 | 0.0112734  | 0.826108 | 0.00557232 | 0.838619 | 0.0104301  |
| XGBoost            | ctgan     |    10   | 0.847616 | 0.00791639 | 0.834214 | 0.0032643  | 0.851299 | 0.00780693 |
| XGBoost            | diffusion |     0.5 | 0.845282 | 0.00857707 | 0.828192 | 0.00559326 | 0.849949 | 0.00857898 |
| XGBoost            | none      |     0   | 0.840056 | 0.0109884  | 0.830791 | 0.00466506 | 0.846161 | 0.0100888  |
| XGBoost            | ros       |     1   | 0.841988 | 0.00973406 | 0.835835 | 0.00330695 | 0.84632  | 0.00946242 |
| XGBoost            | smote     |     5   | 0.836014 | 0.00895905 | 0.830217 | 0.00447666 | 0.836776 | 0.0091305  |

## ulb_utility_primary.csv

| Method           | Selected ratio r*   | F1 (0.5)      | F1 (tuned)    | AUPRC         | MCC (0.5)     | ROC-AUC       |   Seeds |
|:-----------------|:--------------------|:--------------|:--------------|:--------------|:--------------|:--------------|--------:|
| No augmentation  | none                | 0.840 ± 0.011 | 0.839 ± 0.010 | 0.831 ± 0.005 | 0.846 ± 0.010 | 0.971 ± 0.003 |      10 |
| ROS              | 1×                  | 0.842 ± 0.010 | 0.845 ± 0.010 | 0.836 ± 0.003 | 0.846 ± 0.009 | 0.974 ± 0.002 |      10 |
| SMOTE            | 5×                  | 0.836 ± 0.009 | 0.840 ± 0.010 | 0.830 ± 0.004 | 0.837 ± 0.009 | 0.972 ± 0.002 |      10 |
| ADASYN           | 0.5×                | 0.834 ± 0.011 | 0.831 ± 0.011 | 0.826 ± 0.006 | 0.839 ± 0.010 | 0.973 ± 0.003 |      10 |
| CTGAN            | 10×                 | 0.848 ± 0.008 | 0.845 ± 0.009 | 0.834 ± 0.003 | 0.851 ± 0.008 | 0.971 ± 0.002 |      10 |
| Diffusion (DDPM) | 0.5×                | 0.845 ± 0.009 | 0.843 ± 0.007 | 0.828 ± 0.006 | 0.850 ± 0.009 | 0.973 ± 0.002 |      10 |

## All manuscript values

- `DP_CLF`: XGBoost
- `DP_EPOCHS`: 250
- `DP_NSEEDS`: 5
- `DP_RATIO`: 0.5×
- `DP_light_0p5_auprc`: 0.829 ± 0.005
- `DP_light_0p5_eps`: 311.72
- `DP_light_0p5_f1`: 0.850 ± 0.007
- `DP_light_0p5_f1m`: 0.850
- `DP_light_0p5_loss`: 0.528 ± 0.005
- `DP_light_0p5_lossm`: 0.528
- `DP_light_0p5_nn`: 0.487 ± 0.002
- `DP_light_1_auprc`: 0.831 ± 0.003
- `DP_light_1_eps`: 63.96
- `DP_light_1_f1`: 0.839 ± 0.009
- `DP_light_1_f1m`: 0.839
- `DP_light_1_loss`: 0.516 ± 0.007
- `DP_light_1_lossm`: 0.516
- `DP_light_1_nn`: 0.484 ± 0.004
- `DP_light_2_auprc`: 0.833 ± 0.004
- `DP_light_2_eps`: 19.99
- `DP_light_2_f1`: 0.843 ± 0.008
- `DP_light_2_f1m`: 0.843
- `DP_light_2_loss`: 0.510 ± 0.011
- `DP_light_2_lossm`: 0.510
- `DP_light_2_nn`: 0.481 ± 0.005
- `DP_light_4_auprc`: 0.833 ± 0.005
- `DP_light_4_eps`: 7.91
- `DP_light_4_f1`: 0.848 ± 0.010
- `DP_light_4_f1m`: 0.848
- `DP_light_4_loss`: 0.514 ± 0.013
- `DP_light_4_lossm`: 0.514
- `DP_light_4_nn`: 0.479 ± 0.008
- `DP_light_none_auprc`: 0.828 ± 0.005
- `DP_light_none_eps`: ∞ (no DP)
- `DP_light_none_f1`: 0.844 ± 0.006
- `DP_light_none_f1m`: 0.844
- `DP_light_none_loss`: 0.594 ± 0.002
- `DP_light_none_lossm`: 0.594
- `DP_light_none_nn`: 0.491 ± 0.003
- `DP_matched_0p5_auprc`: 0.833 ± 0.001
- `DP_matched_0p5_eps`: 311.72
- `DP_matched_0p5_f1`: 0.843 ± 0.008
- `DP_matched_0p5_f1m`: 0.843
- `DP_matched_0p5_loss`: 0.567 ± 0.007
- `DP_matched_0p5_lossm`: 0.567
- `DP_matched_0p5_nn`: 0.491 ± 0.005
- `DP_matched_1_auprc`: 0.830 ± 0.006
- `DP_matched_1_eps`: 63.96
- `DP_matched_1_f1`: 0.846 ± 0.009
- `DP_matched_1_f1m`: 0.846
- `DP_matched_1_loss`: 0.554 ± 0.008
- `DP_matched_1_lossm`: 0.554
- `DP_matched_1_nn`: 0.491 ± 0.006
- `DP_matched_2_auprc`: 0.832 ± 0.007
- `DP_matched_2_eps`: 19.99
- `DP_matched_2_f1`: 0.852 ± 0.007
- `DP_matched_2_f1m`: 0.852
- `DP_matched_2_loss`: 0.546 ± 0.013
- `DP_matched_2_lossm`: 0.546
- `DP_matched_2_nn`: 0.490 ± 0.009
- `DP_matched_4_auprc`: 0.833 ± 0.003
- `DP_matched_4_eps`: 7.91
- `DP_matched_4_f1`: 0.844 ± 0.011
- `DP_matched_4_f1m`: 0.844
- `DP_matched_4_loss`: 0.546 ± 0.018
- `DP_matched_4_lossm`: 0.546
- `DP_matched_4_nn`: 0.484 ± 0.011
- `DP_matched_none_auprc`: 0.830 ± 0.007
- `DP_matched_none_eps`: ∞ (no DP)
- `DP_matched_none_f1`: 0.840 ± 0.011
- `DP_matched_none_f1m`: 0.840
- `DP_matched_none_loss`: 0.795 ± 0.007
- `DP_matched_none_lossm`: 0.795
- `DP_matched_none_nn`: 0.501 ± 0.004
- `DS_IEEE_es_fraud`: 1,000
- `DS_IEEE_n_features`: 209
- `DS_IEEE_n_fraud`: 20,663
- `DS_IEEE_n_total`: 590,540
- `DS_IEEE_test_fraud`: 2,400
- `DS_IEEE_test_legit`: 18,000
- `DS_IEEE_train_fraud`: 4,000
- `DS_IEEE_train_legit`: 30,000
- `DS_TEMP_test_fraud`: 108
- `DS_TEMP_train_fraud`: 384
- `DS_ULB_n_features`: 30
- `DS_ULB_n_fraud`: 492
- `DS_ULB_n_total`: 284,807
- `DS_ULB_test_fraud`: 148
- `DS_ULB_test_legit`: 85,295
- `DS_ULB_train_fraud`: 344
- `DS_ULB_train_legit`: 199,020
- `IEEE_NSEEDS`: 5
- `IEEE_PRIMARY`: XGBoost
- `IEEE_adasyn_auprc`: 0.697 ± 0.002
- `IEEE_adasyn_auprcm`: 0.697
- `IEEE_adasyn_f1`: 0.630 ± 0.003
- `IEEE_adasyn_f1m`: 0.630
- `IEEE_adasyn_f1t`: 0.634 ± 0.004
- `IEEE_adasyn_f1tm`: 0.634
- `IEEE_adasyn_mcc`: 0.596 ± 0.003
- `IEEE_adasyn_mccm`: 0.596
- `IEEE_adasyn_prec`: 0.735 ± 0.005
- `IEEE_adasyn_precm`: 0.735
- `IEEE_adasyn_r`: 5×
- `IEEE_adasyn_rec`: 0.552 ± 0.005
- `IEEE_adasyn_recm`: 0.552
- `IEEE_adasyn_roc`: 0.891 ± 0.001
- `IEEE_adasyn_rocm`: 0.891
- `IEEE_ctgan_auprc`: 0.719 ± 0.003
- `IEEE_ctgan_auprcm`: 0.719
- `IEEE_ctgan_f1`: 0.606 ± 0.004
- `IEEE_ctgan_f1m`: 0.606
- `IEEE_ctgan_f1t`: 0.652 ± 0.005
- `IEEE_ctgan_f1tm`: 0.652
- `IEEE_ctgan_mcc`: 0.601 ± 0.004
- `IEEE_ctgan_mccm`: 0.601
- `IEEE_ctgan_prec`: 0.854 ± 0.005
- `IEEE_ctgan_precm`: 0.854
- `IEEE_ctgan_r`: 1×
- `IEEE_ctgan_rec`: 0.469 ± 0.004
- `IEEE_ctgan_recm`: 0.469
- `IEEE_ctgan_roc`: 0.898 ± 0.001
- `IEEE_ctgan_rocm`: 0.898
- `IEEE_diff_stop`: 765 ± 34
- `IEEE_diffusion_auprc`: 0.719 ± 0.002
- `IEEE_diffusion_auprcm`: 0.719
- `IEEE_diffusion_f1`: 0.607 ± 0.003
- `IEEE_diffusion_f1m`: 0.607
- `IEEE_diffusion_f1t`: 0.651 ± 0.005
- `IEEE_diffusion_f1tm`: 0.651
- `IEEE_diffusion_mcc`: 0.604 ± 0.003
- `IEEE_diffusion_mccm`: 0.604
- `IEEE_diffusion_prec`: 0.860 ± 0.006
- `IEEE_diffusion_precm`: 0.860
- `IEEE_diffusion_r`: 1×
- `IEEE_diffusion_rec`: 0.469 ± 0.004
- `IEEE_diffusion_recm`: 0.469
- `IEEE_diffusion_roc`: 0.898 ± 0.001
- `IEEE_diffusion_rocm`: 0.898
- `IEEE_nfit`: 27,200
- `IEEE_nfitfraud`: 3,200
- `IEEE_none_auprc`: 0.733 ± 0.001
- `IEEE_none_auprcm`: 0.733
- `IEEE_none_f1`: 0.620 ± 0.003
- `IEEE_none_f1m`: 0.620
- `IEEE_none_f1t`: 0.667 ± 0.002
- `IEEE_none_f1tm`: 0.667
- `IEEE_none_mcc`: 0.613 ± 0.003
- `IEEE_none_mccm`: 0.613
- `IEEE_none_prec`: 0.858 ± 0.005
- `IEEE_none_precm`: 0.858
- `IEEE_none_r`: none
- `IEEE_none_rec`: 0.485 ± 0.004
- `IEEE_none_recm`: 0.485
- `IEEE_none_roc`: 0.904 ± 0.001
- `IEEE_none_rocm`: 0.904
- `IEEE_nval`: 6,800
- `IEEE_nvalfraud`: 800
- `IEEE_ros_auprc`: 0.731 ± 0.003
- `IEEE_ros_auprcm`: 0.731
- `IEEE_ros_f1`: 0.661 ± 0.003
- `IEEE_ros_f1m`: 0.661
- `IEEE_ros_f1t`: 0.659 ± 0.005
- `IEEE_ros_f1tm`: 0.659
- `IEEE_ros_mcc`: 0.620 ± 0.003
- `IEEE_ros_mccm`: 0.620
- `IEEE_ros_prec`: 0.696 ± 0.006
- `IEEE_ros_precm`: 0.696
- `IEEE_ros_r`: 2×
- `IEEE_ros_rec`: 0.630 ± 0.007
- `IEEE_ros_recm`: 0.630
- `IEEE_ros_roc`: 0.905 ± 0.001
- `IEEE_ros_rocm`: 0.905
- `IEEE_smote_auprc`: 0.705 ± 0.002
- `IEEE_smote_auprcm`: 0.705
- `IEEE_smote_f1`: 0.638 ± 0.004
- `IEEE_smote_f1m`: 0.638
- `IEEE_smote_f1t`: 0.642 ± 0.003
- `IEEE_smote_f1tm`: 0.642
- `IEEE_smote_mcc`: 0.605 ± 0.004
- `IEEE_smote_mccm`: 0.605
- `IEEE_smote_prec`: 0.744 ± 0.002
- `IEEE_smote_precm`: 0.744
- `IEEE_smote_r`: 5×
- `IEEE_smote_rec`: 0.558 ± 0.006
- `IEEE_smote_recm`: 0.558
- `IEEE_smote_roc`: 0.893 ± 0.001
- `IEEE_smote_rocm`: 0.893
- `MIAT_IEEE_adasyn_d`: -0.291 [-0.293, -0.289]
- `MIAT_IEEE_adasyn_ph`: 0.250
- `MIAT_IEEE_ctgan_d`: -0.003 [-0.004, -0.002]
- `MIAT_IEEE_ctgan_ph`: 0.250
- `MIAT_IEEE_ros_d`: -0.429 [-0.432, -0.427]
- `MIAT_IEEE_ros_ph`: 0.250
- `MIAT_IEEE_smote_d`: -0.368 [-0.370, -0.364]
- `MIAT_IEEE_smote_ph`: 0.250
- `MIAT_TMP_adasyn_d`: -0.101 [-0.115, -0.087]
- `MIAT_TMP_adasyn_ph`: 0.250
- `MIAT_TMP_ctgan_d`: -0.002 [-0.032, +0.029]
- `MIAT_TMP_ctgan_ph`: 1.000
- `MIAT_TMP_ros_d`: -0.521 [-0.530, -0.509]
- `MIAT_TMP_ros_ph`: 0.250
- `MIAT_TMP_smote_d`: -0.613 [-0.618, -0.606]
- `MIAT_TMP_smote_ph`: 0.250
- `MIAT_ULB_adasyn_d`: +0.001 [-0.004, +0.007]
- `MIAT_ULB_adasyn_ph`: 0.625
- `MIAT_ULB_ctgan_d`: +0.010 [+0.003, +0.019]
- `MIAT_ULB_ctgan_ph`: 0.129
- `MIAT_ULB_ros_d`: -0.293 [-0.299, -0.287]
- `MIAT_ULB_ros_ph`: 0.008
- `MIAT_ULB_smote_d`: -0.399 [-0.404, -0.394]
- `MIAT_ULB_smote_ph`: 0.008
- `MIA_IEEE_adasyn_clf`: 0.539 ± 0.001
- `MIA_IEEE_adasyn_clfci`: 0.524–0.554
- `MIA_IEEE_adasyn_clfm`: 0.539
- `MIA_IEEE_adasyn_copy`: 0.0%
- `MIA_IEEE_adasyn_nn`: 0.795 ± 0.002
- `MIA_IEEE_adasyn_nn5`: 0.679 ± 0.001
- `MIA_IEEE_adasyn_nn5ci`: 0.666–0.691
- `MIA_IEEE_adasyn_nn5m`: 0.679
- `MIA_IEEE_adasyn_nnci`: 0.786–0.805
- `MIA_IEEE_adasyn_nnfix`: 0.661 ± 0.003
- `MIA_IEEE_adasyn_nnfixci`: 0.649–0.673
- `MIA_IEEE_adasyn_nnfixm`: 0.661
- `MIA_IEEE_adasyn_nnm`: 0.795
- `MIA_IEEE_adasyn_nsyn`: 20,000
- `MIA_IEEE_chance`: 0.485–0.515
- `MIA_IEEE_ctgan_clf`: 0.543 ± 0.001
- `MIA_IEEE_ctgan_clfci`: 0.528–0.557
- `MIA_IEEE_ctgan_clfm`: 0.543
- `MIA_IEEE_ctgan_copy`: 0.0%
- `MIA_IEEE_ctgan_nn`: 0.506 ± 0.001
- `MIA_IEEE_ctgan_nn5`: 0.506 ± 0.000
- `MIA_IEEE_ctgan_nn5ci`: 0.492–0.520
- `MIA_IEEE_ctgan_nn5m`: 0.506
- `MIA_IEEE_ctgan_nnci`: 0.492–0.520
- `MIA_IEEE_ctgan_nnfix`: 0.507 ± 0.000
- `MIA_IEEE_ctgan_nnfixci`: 0.493–0.520
- `MIA_IEEE_ctgan_nnfixm`: 0.507
- `MIA_IEEE_ctgan_nnm`: 0.506
- `MIA_IEEE_ctgan_nsyn`: 4,000
- `MIA_IEEE_diffusion_clf`: 0.542 ± 0.001
- `MIA_IEEE_diffusion_clfci`: 0.527–0.556
- `MIA_IEEE_diffusion_clfm`: 0.542
- `MIA_IEEE_diffusion_copy`: 0.0%
- `MIA_IEEE_diffusion_nn`: 0.503 ± 0.001
- `MIA_IEEE_diffusion_nn5`: 0.504 ± 0.001
- `MIA_IEEE_diffusion_nn5ci`: 0.490–0.518
- `MIA_IEEE_diffusion_nn5m`: 0.504
- `MIA_IEEE_diffusion_nnci`: 0.489–0.518
- `MIA_IEEE_diffusion_nnfix`: 0.503 ± 0.001
- `MIA_IEEE_diffusion_nnfixci`: 0.490–0.518
- `MIA_IEEE_diffusion_nnfixm`: 0.503
- `MIA_IEEE_diffusion_nnm`: 0.503
- `MIA_IEEE_diffusion_nsyn`: 4,000
- `MIA_IEEE_loss`: 0.521 ± 0.001
- `MIA_IEEE_lossci`: 0.507–0.536
- `MIA_IEEE_lossm`: 0.521
- `MIA_IEEE_lossmax`: 0.522
- `MIA_IEEE_lossmin`: 0.521
- `MIA_IEEE_nmem`: 4,000
- `MIA_IEEE_nnon`: 2,400
- `MIA_IEEE_none_clf`: 0.557 ± 0.002
- `MIA_IEEE_none_clfci`: 0.542–0.572
- `MIA_IEEE_none_clfm`: 0.557
- `MIA_IEEE_ros_clf`: 0.558 ± 0.002
- `MIA_IEEE_ros_clfci`: 0.543–0.573
- `MIA_IEEE_ros_clfm`: 0.558
- `MIA_IEEE_ros_copy`: 83.2%
- `MIA_IEEE_ros_nn`: 0.933 ± 0.003
- `MIA_IEEE_ros_nn5`: 0.672 ± 0.001
- `MIA_IEEE_ros_nn5ci`: 0.659–0.684
- `MIA_IEEE_ros_nn5m`: 0.672
- `MIA_IEEE_ros_nnci`: 0.930–0.936
- `MIA_IEEE_ros_nnfix`: 0.855 ± 0.002
- `MIA_IEEE_ros_nnfixci`: 0.849–0.860
- `MIA_IEEE_ros_nnfixm`: 0.855
- `MIA_IEEE_ros_nnm`: 0.933
- `MIA_IEEE_ros_nsyn`: 8,000
- `MIA_IEEE_smote_clf`: 0.538 ± 0.000
- `MIA_IEEE_smote_clfci`: 0.523–0.553
- `MIA_IEEE_smote_clfm`: 0.538
- `MIA_IEEE_smote_copy`: 0.0%
- `MIA_IEEE_smote_nn`: 0.871 ± 0.004
- `MIA_IEEE_smote_nn5`: 0.725 ± 0.002
- `MIA_IEEE_smote_nn5ci`: 0.712–0.737
- `MIA_IEEE_smote_nn5m`: 0.725
- `MIA_IEEE_smote_nnci`: 0.863–0.879
- `MIA_IEEE_smote_nnfix`: 0.720 ± 0.001
- `MIA_IEEE_smote_nnfixci`: 0.709–0.731
- `MIA_IEEE_smote_nnfixm`: 0.720
- `MIA_IEEE_smote_nnm`: 0.871
- `MIA_IEEE_smote_nsyn`: 20,000
- `MIA_TMP_adasyn_clf`: 0.713 ± 0.011
- `MIA_TMP_adasyn_clfci`: 0.661–0.767
- `MIA_TMP_adasyn_clfm`: 0.713
- `MIA_TMP_adasyn_copy`: 0.0%
- `MIA_TMP_adasyn_nn`: 0.485 ± 0.010
- `MIA_TMP_adasyn_nn5`: 0.421 ± 0.007
- `MIA_TMP_adasyn_nn5ci`: 0.371–0.473
- `MIA_TMP_adasyn_nn5m`: 0.421
- `MIA_TMP_adasyn_nnci`: 0.438–0.536
- `MIA_TMP_adasyn_nnfix`: 0.497 ± 0.002
- `MIA_TMP_adasyn_nnfixci`: 0.448–0.547
- `MIA_TMP_adasyn_nnfixm`: 0.497
- `MIA_TMP_adasyn_nnm`: 0.485
- `MIA_TMP_adasyn_nsyn`: 192
- `MIA_TMP_chance`: 0.440–0.559
- `MIA_TMP_ctgan_clf`: 0.726 ± 0.036
- `MIA_TMP_ctgan_clfci`: 0.680–0.770
- `MIA_TMP_ctgan_clfm`: 0.726
- `MIA_TMP_ctgan_copy`: 0.0%
- `MIA_TMP_ctgan_nn`: 0.386 ± 0.042
- `MIA_TMP_ctgan_nn5`: 0.393 ± 0.042
- `MIA_TMP_ctgan_nn5ci`: 0.350–0.438
- `MIA_TMP_ctgan_nn5m`: 0.393
- `MIA_TMP_ctgan_nnci`: 0.346–0.428
- `MIA_TMP_ctgan_nnfix`: 0.396 ± 0.024
- `MIA_TMP_ctgan_nnfixci`: 0.350–0.441
- `MIA_TMP_ctgan_nnfixm`: 0.396
- `MIA_TMP_ctgan_nnm`: 0.386
- `MIA_TMP_ctgan_nsyn`: 3,840
- `MIA_TMP_diffusion_clf`: 0.728 ± 0.015
- `MIA_TMP_diffusion_clfci`: 0.679–0.778
- `MIA_TMP_diffusion_clfm`: 0.728
- `MIA_TMP_diffusion_copy`: 0.0%
- `MIA_TMP_diffusion_nn`: 0.384 ± 0.009
- `MIA_TMP_diffusion_nn5`: 0.372 ± 0.005
- `MIA_TMP_diffusion_nn5ci`: 0.329–0.423
- `MIA_TMP_diffusion_nn5m`: 0.372
- `MIA_TMP_diffusion_nnci`: 0.343–0.434
- `MIA_TMP_diffusion_nnfix`: 0.450 ± 0.011
- `MIA_TMP_diffusion_nnfixci`: 0.410–0.500
- `MIA_TMP_diffusion_nnfixm`: 0.450
- `MIA_TMP_diffusion_nnm`: 0.384
- `MIA_TMP_diffusion_nsyn`: 192
- `MIA_TMP_loss`: 1.000 ± 0.000
- `MIA_TMP_lossci`: 0.999–1.000
- `MIA_TMP_lossm`: 1.000
- `MIA_TMP_lossmax`: 1.000
- `MIA_TMP_lossmin`: 1.000
- `MIA_TMP_nmem`: 384
- `MIA_TMP_nnon`: 108
- `MIA_TMP_none_clf`: 0.651 ± 0.008
- `MIA_TMP_none_clfci`: 0.593–0.708
- `MIA_TMP_none_clfm`: 0.651
- `MIA_TMP_ros_clf`: 0.570 ± 0.011
- `MIA_TMP_ros_clfci`: 0.493–0.647
- `MIA_TMP_ros_clfm`: 0.570
- `MIA_TMP_ros_copy`: 65.6%
- `MIA_TMP_ros_nn`: 0.906 ± 0.016
- `MIA_TMP_ros_nn5`: 0.812 ± 0.024
- `MIA_TMP_ros_nn5ci`: 0.777–0.845
- `MIA_TMP_ros_nn5m`: 0.812
- `MIA_TMP_ros_nnci`: 0.888–0.924
- `MIA_TMP_ros_nnfix`: 1.000 ± 0.000
- `MIA_TMP_ros_nnfixci`: 1.000–1.000
- `MIA_TMP_ros_nnfixm`: 1.000
- `MIA_TMP_ros_nnm`: 0.906
- `MIA_TMP_ros_nsyn`: 384
- `MIA_TMP_smote_clf`: 0.803 ± 0.024
- `MIA_TMP_smote_clfci`: 0.763–0.841
- `MIA_TMP_smote_clfm`: 0.803
- `MIA_TMP_smote_copy`: 7.8%
- `MIA_TMP_smote_nn`: 0.997 ± 0.002
- `MIA_TMP_smote_nn5`: 0.986 ± 0.002
- `MIA_TMP_smote_nn5ci`: 0.977–0.992
- `MIA_TMP_smote_nn5m`: 0.986
- `MIA_TMP_smote_nnci`: 0.995–0.999
- `MIA_TMP_smote_nnfix`: 0.999 ± 0.000
- `MIA_TMP_smote_nnfixci`: 0.999–1.000
- `MIA_TMP_smote_nnfixm`: 0.999
- `MIA_TMP_smote_nnm`: 0.997
- `MIA_TMP_smote_nsyn`: 3,840
- `MIA_ULB_adasyn_clf`: 0.605 ± 0.012
- `MIA_ULB_adasyn_clfci`: 0.550–0.658
- `MIA_ULB_adasyn_clfm`: 0.605
- `MIA_ULB_adasyn_copy`: 1.2%
- `MIA_ULB_adasyn_nn`: 0.517 ± 0.006
- `MIA_ULB_adasyn_nn5`: 0.499 ± 0.003
- `MIA_ULB_adasyn_nn5ci`: 0.445–0.551
- `MIA_ULB_adasyn_nn5m`: 0.499
- `MIA_ULB_adasyn_nnci`: 0.465–0.570
- `MIA_ULB_adasyn_nnfix`: 0.558 ± 0.001
- `MIA_ULB_adasyn_nnfixci`: 0.510–0.611
- `MIA_ULB_adasyn_nnfixm`: 0.558
- `MIA_ULB_adasyn_nnm`: 0.517
- `MIA_ULB_adasyn_nsyn`: 172
- `MIA_ULB_chance`: 0.446–0.556
- `MIA_ULB_ctgan_clf`: 0.601 ± 0.014
- `MIA_ULB_ctgan_clfci`: 0.551–0.649
- `MIA_ULB_ctgan_clfm`: 0.601
- `MIA_ULB_ctgan_copy`: 0.0%
- `MIA_ULB_ctgan_nn`: 0.508 ± 0.014
- `MIA_ULB_ctgan_nn5`: 0.508 ± 0.010
- `MIA_ULB_ctgan_nn5ci`: 0.460–0.558
- `MIA_ULB_ctgan_nn5m`: 0.508
- `MIA_ULB_ctgan_nnci`: 0.459–0.556
- `MIA_ULB_ctgan_nnfix`: 0.506 ± 0.008
- `MIA_ULB_ctgan_nnfixci`: 0.458–0.555
- `MIA_ULB_ctgan_nnfixm`: 0.506
- `MIA_ULB_ctgan_nnm`: 0.508
- `MIA_ULB_ctgan_nsyn`: 3,440
- `MIA_ULB_diffusion_clf`: 0.607 ± 0.008
- `MIA_ULB_diffusion_clfci`: 0.554–0.658
- `MIA_ULB_diffusion_clfm`: 0.607
- `MIA_ULB_diffusion_copy`: 0.0%
- `MIA_ULB_diffusion_nn`: 0.518 ± 0.005
- `MIA_ULB_diffusion_nn5`: 0.503 ± 0.002
- `MIA_ULB_diffusion_nn5ci`: 0.451–0.556
- `MIA_ULB_diffusion_nn5m`: 0.503
- `MIA_ULB_diffusion_nnci`: 0.467–0.570
- `MIA_ULB_diffusion_nnfix`: 0.573 ± 0.005
- `MIA_ULB_diffusion_nnfixci`: 0.523–0.620
- `MIA_ULB_diffusion_nnfixm`: 0.573
- `MIA_ULB_diffusion_nnm`: 0.518
- `MIA_ULB_diffusion_nsyn`: 172
- `MIA_ULB_loss`: 0.919 ± 0.003
- `MIA_ULB_lossci`: 0.881–0.950
- `MIA_ULB_lossm`: 0.919
- `MIA_ULB_lossmax`: 0.924
- `MIA_ULB_lossmin`: 0.914
- `MIA_ULB_nmem`: 344
- `MIA_ULB_nnon`: 148
- `MIA_ULB_none_clf`: 0.583 ± 0.016
- `MIA_ULB_none_clfci`: 0.528–0.636
- `MIA_ULB_none_clfm`: 0.583
- `MIA_ULB_ros_clf`: 0.634 ± 0.007
- `MIA_ULB_ros_clfci`: 0.582–0.685
- `MIA_ULB_ros_clfm`: 0.634
- `MIA_ULB_ros_copy`: 65.1%
- `MIA_ULB_ros_nn`: 0.812 ± 0.010
- `MIA_ULB_ros_nn5`: 0.672 ± 0.013
- `MIA_ULB_ros_nn5ci`: 0.631–0.711
- `MIA_ULB_ros_nn5m`: 0.672
- `MIA_ULB_ros_nnci`: 0.786–0.836
- `MIA_ULB_ros_nnfix`: 0.976 ± 0.000
- `MIA_ULB_ros_nnfixci`: 0.956–0.992
- `MIA_ULB_ros_nnfixm`: 0.976
- `MIA_ULB_ros_nnm`: 0.812
- `MIA_ULB_ros_nsyn`: 344
- `MIA_ULB_smote_clf`: 0.610 ± 0.012
- `MIA_ULB_smote_clfci`: 0.560–0.659
- `MIA_ULB_smote_clfm`: 0.610
- `MIA_ULB_smote_copy`: 4.3%
- `MIA_ULB_smote_nn`: 0.917 ± 0.006
- `MIA_ULB_smote_nn5`: 0.836 ± 0.009
- `MIA_ULB_smote_nn5ci`: 0.797–0.872
- `MIA_ULB_smote_nn5m`: 0.836
- `MIA_ULB_smote_nnci`: 0.885–0.944
- `MIA_ULB_smote_nnfix`: 0.937 ± 0.004
- `MIA_ULB_smote_nnfixci`: 0.904–0.965
- `MIA_ULB_smote_nnfixm`: 0.937
- `MIA_ULB_smote_nnm`: 0.917
- `MIA_ULB_smote_nsyn`: 1,720
- `RT_total_h`: 7.2
- `SC_IEEE_1000_e100`: 0.528 ± 0.004
- `SC_IEEE_1000_e1000`: 0.597 ± 0.001
- `SC_IEEE_1000_e1000m`: 0.597
- `SC_IEEE_1000_e100m`: 0.528
- `SC_IEEE_1000_e1500`: 0.609 ± 0.000
- `SC_IEEE_1000_e1500m`: 0.609
- `SC_IEEE_1000_e2250`: 0.621 ± 0.001
- `SC_IEEE_1000_e2250m`: 0.621
- `SC_IEEE_1000_e250`: 0.554 ± 0.003
- `SC_IEEE_1000_e250m`: 0.554
- `SC_IEEE_1000_e500`: 0.577 ± 0.003
- `SC_IEEE_1000_e500m`: 0.577
- `SC_IEEE_1000_heldout`: 0.605 ± 0.003
- `SC_IEEE_1000_heldout_ep`: 1333 ± 123
- `SC_IEEE_1000_heldoutm`: 0.605
- `SC_IEEE_1000_trainrule`: 0.613 ± 0.004
- `SC_IEEE_1000_trainrule_ep`: 1900 ± 87
- `SC_IEEE_1000_trainrulem`: 0.613
- `SC_IEEE_2000_e100`: 0.518 ± 0.007
- `SC_IEEE_2000_e1000`: 0.550 ± 0.007
- `SC_IEEE_2000_e1000m`: 0.550
- `SC_IEEE_2000_e100m`: 0.518
- `SC_IEEE_2000_e1500`: 0.554 ± 0.007
- `SC_IEEE_2000_e1500m`: 0.554
- `SC_IEEE_2000_e2250`: 0.560 ± 0.008
- `SC_IEEE_2000_e2250m`: 0.560
- `SC_IEEE_2000_e250`: 0.533 ± 0.006
- `SC_IEEE_2000_e250m`: 0.533
- `SC_IEEE_2000_e500`: 0.542 ± 0.006
- `SC_IEEE_2000_e500m`: 0.542
- `SC_IEEE_2000_heldout`: 0.556 ± 0.014
- `SC_IEEE_2000_heldout_ep`: 1717 ± 772
- `SC_IEEE_2000_heldoutm`: 0.556
- `SC_IEEE_2000_trainrule`: 0.556 ± 0.008
- `SC_IEEE_2000_trainrule_ep`: 1750 ± 87
- `SC_IEEE_2000_trainrulem`: 0.556
- `SC_IEEE_344_e100`: 0.572 ± 0.005
- `SC_IEEE_344_e1000`: 0.692 ± 0.005
- `SC_IEEE_344_e1000m`: 0.692
- `SC_IEEE_344_e100m`: 0.572
- `SC_IEEE_344_e1500`: 0.715 ± 0.010
- `SC_IEEE_344_e1500m`: 0.715
- `SC_IEEE_344_e2250`: 0.737 ± 0.011
- `SC_IEEE_344_e2250m`: 0.737
- `SC_IEEE_344_e250`: 0.610 ± 0.001
- `SC_IEEE_344_e250m`: 0.610
- `SC_IEEE_344_e500`: 0.648 ± 0.002
- `SC_IEEE_344_e500m`: 0.648
- `SC_IEEE_344_heldout`: 0.630 ± 0.039
- `SC_IEEE_344_heldout_ep`: 392 ± 236
- `SC_IEEE_344_heldoutm`: 0.630
- `SC_IEEE_344_trainrule`: 0.739 ± 0.010
- `SC_IEEE_344_trainrule_ep`: 2100 ± 212
- `SC_IEEE_344_trainrulem`: 0.739
- `SC_IEEE_4000_e100`: 0.511 ± 0.001
- `SC_IEEE_4000_e1000`: 0.524 ± 0.000
- `SC_IEEE_4000_e1000m`: 0.524
- `SC_IEEE_4000_e100m`: 0.511
- `SC_IEEE_4000_e1500`: 0.526 ± 0.000
- `SC_IEEE_4000_e1500m`: 0.526
- `SC_IEEE_4000_e2250`: 0.528 ± 0.001
- `SC_IEEE_4000_e2250m`: 0.528
- `SC_IEEE_4000_e250`: 0.518 ± 0.001
- `SC_IEEE_4000_e250m`: 0.518
- `SC_IEEE_4000_e500`: 0.521 ± 0.001
- `SC_IEEE_4000_e500m`: 0.521
- `SC_IEEE_4000_heldout`: 0.527 ± 0.001
- `SC_IEEE_4000_heldout_ep`: 1908 ± 311
- `SC_IEEE_4000_heldoutm`: 0.527
- `SC_IEEE_4000_trainrule`: 0.526 ± 0.001
- `SC_IEEE_4000_trainrule_ep`: 1450 ± 87
- `SC_IEEE_4000_trainrulem`: 0.526
- `SC_IEEE_rho`: -0.97
- `SC_IEEE_rho_p`: <0.001
- `SC_ULB_e100`: 0.675 ± 0.013
- `SC_ULB_e1000`: 0.913 ± 0.003
- `SC_ULB_e1000m`: 0.913
- `SC_ULB_e100m`: 0.675
- `SC_ULB_e1500`: 0.923 ± 0.002
- `SC_ULB_e1500m`: 0.923
- `SC_ULB_e2250`: 0.933 ± 0.001
- `SC_ULB_e2250m`: 0.933
- `SC_ULB_e250`: 0.794 ± 0.006
- `SC_ULB_e250m`: 0.794
- `SC_ULB_e500`: 0.869 ± 0.004
- `SC_ULB_e500m`: 0.869
- `SC_ULB_heldout`: 0.754 ± 0.024
- `SC_ULB_heldout_ep`: 183 ± 38
- `SC_ULB_heldoutm`: 0.754
- `SC_ULB_n`: 275
- `SC_ULB_trainrule`: 0.927 ± 0.006
- `SC_ULB_trainrule_ep`: 1750 ± 229
- `SC_ULB_trainrulem`: 0.927
- `SHAP_rho`: 0.68
- `SHAP_top5_overlap`: 3
- `TMP_NSEEDS`: 5
- `TMP_PRIMARY`: XGBoost
- `TMP_adasyn_auprc`: 0.794 ± 0.004
- `TMP_adasyn_auprcm`: 0.794
- `TMP_adasyn_f1`: 0.794 ± 0.003
- `TMP_adasyn_f1m`: 0.794
- `TMP_adasyn_f1t`: 0.790 ± 0.010
- `TMP_adasyn_f1tm`: 0.790
- `TMP_adasyn_mcc`: 0.806 ± 0.001
- `TMP_adasyn_mccm`: 0.806
- `TMP_adasyn_prec`: 0.959 ± 0.018
- `TMP_adasyn_precm`: 0.959
- `TMP_adasyn_r`: 0.5×
- `TMP_adasyn_rec`: 0.678 ± 0.014
- `TMP_adasyn_recm`: 0.678
- `TMP_adasyn_roc`: 0.984 ± 0.002
- `TMP_adasyn_rocm`: 0.984
- `TMP_ctgan_auprc`: 0.799 ± 0.006
- `TMP_ctgan_auprcm`: 0.799
- `TMP_ctgan_f1`: 0.807 ± 0.015
- `TMP_ctgan_f1m`: 0.807
- `TMP_ctgan_f1t`: 0.766 ± 0.020
- `TMP_ctgan_f1tm`: 0.766
- `TMP_ctgan_mcc`: 0.811 ± 0.015
- `TMP_ctgan_mccm`: 0.811
- `TMP_ctgan_prec`: 0.896 ± 0.041
- `TMP_ctgan_precm`: 0.896
- `TMP_ctgan_r`: 10×
- `TMP_ctgan_rec`: 0.735 ± 0.027
- `TMP_ctgan_recm`: 0.735
- `TMP_ctgan_roc`: 0.982 ± 0.004
- `TMP_ctgan_rocm`: 0.982
- `TMP_diff_stop`: 2010 ± 227
- `TMP_diffusion_auprc`: 0.800 ± 0.002
- `TMP_diffusion_auprcm`: 0.800
- `TMP_diffusion_f1`: 0.817 ± 0.013
- `TMP_diffusion_f1m`: 0.817
- `TMP_diffusion_f1t`: 0.800 ± 0.020
- `TMP_diffusion_f1tm`: 0.800
- `TMP_diffusion_mcc`: 0.827 ± 0.011
- `TMP_diffusion_mccm`: 0.827
- `TMP_diffusion_prec`: 0.968 ± 0.016
- `TMP_diffusion_precm`: 0.968
- `TMP_diffusion_r`: 0.5×
- `TMP_diffusion_rec`: 0.707 ± 0.023
- `TMP_diffusion_recm`: 0.707
- `TMP_diffusion_roc`: 0.982 ± 0.001
- `TMP_diffusion_rocm`: 0.982
- `TMP_nfit`: 159,492
- `TMP_nfitfraud`: 356
- `TMP_none_auprc`: 0.755 ± 0.015
- `TMP_none_auprcm`: 0.755
- `TMP_none_f1`: 0.777 ± 0.024
- `TMP_none_f1m`: 0.777
- `TMP_none_f1t`: 0.760 ± 0.018
- `TMP_none_f1tm`: 0.760
- `TMP_none_mcc`: 0.792 ± 0.021
- `TMP_none_mccm`: 0.792
- `TMP_none_prec`: 0.959 ± 0.016
- `TMP_none_precm`: 0.959
- `TMP_none_r`: none
- `TMP_none_rec`: 0.654 ± 0.034
- `TMP_none_recm`: 0.654
- `TMP_none_roc`: 0.978 ± 0.002
- `TMP_none_rocm`: 0.978
- `TMP_nval`: 39,873
- `TMP_nvalfraud`: 28
- `TMP_ros_auprc`: 0.793 ± 0.007
- `TMP_ros_auprcm`: 0.793
- `TMP_ros_f1`: 0.804 ± 0.019
- `TMP_ros_f1m`: 0.804
- `TMP_ros_f1t`: 0.809 ± 0.019
- `TMP_ros_f1tm`: 0.809
- `TMP_ros_mcc`: 0.808 ± 0.020
- `TMP_ros_mccm`: 0.808
- `TMP_ros_prec`: 0.893 ± 0.037
- `TMP_ros_precm`: 0.893
- `TMP_ros_r`: 1×
- `TMP_ros_rec`: 0.731 ± 0.009
- `TMP_ros_recm`: 0.731
- `TMP_ros_roc`: 0.981 ± 0.004
- `TMP_ros_rocm`: 0.981
- `TMP_smote_auprc`: 0.797 ± 0.010
- `TMP_smote_auprcm`: 0.797
- `TMP_smote_f1`: 0.795 ± 0.013
- `TMP_smote_f1m`: 0.795
- `TMP_smote_f1t`: 0.809 ± 0.032
- `TMP_smote_f1tm`: 0.809
- `TMP_smote_mcc`: 0.800 ± 0.013
- `TMP_smote_mccm`: 0.800
- `TMP_smote_prec`: 0.889 ± 0.028
- `TMP_smote_precm`: 0.889
- `TMP_smote_r`: 10×
- `TMP_smote_rec`: 0.720 ± 0.020
- `TMP_smote_recm`: 0.720
- `TMP_smote_roc`: 0.982 ± 0.002
- `TMP_smote_rocm`: 0.982
- `TSTA_IEEE_adasyn_W`: 0.0
- `TSTA_IEEE_adasyn_d`: +0.021 [+0.019, +0.024]
- `TSTA_IEEE_adasyn_p`: 0.062
- `TSTA_IEEE_adasyn_ph`: 0.312
- `TSTA_IEEE_ctgan_W`: 6.0
- `TSTA_IEEE_ctgan_d`: -0.000 [-0.002, +0.002]
- `TSTA_IEEE_ctgan_p`: 0.812
- `TSTA_IEEE_ctgan_ph`: 0.812
- `TSTA_IEEE_none_W`: 0.0
- `TSTA_IEEE_none_d`: -0.014 [-0.016, -0.013]
- `TSTA_IEEE_none_p`: 0.062
- `TSTA_IEEE_none_ph`: 0.312
- `TSTA_IEEE_ros_W`: 0.0
- `TSTA_IEEE_ros_d`: -0.012 [-0.015, -0.010]
- `TSTA_IEEE_ros_p`: 0.062
- `TSTA_IEEE_ros_ph`: 0.312
- `TSTA_IEEE_smote_W`: 0.0
- `TSTA_IEEE_smote_d`: +0.014 [+0.012, +0.016]
- `TSTA_IEEE_smote_p`: 0.062
- `TSTA_IEEE_smote_ph`: 0.312
- `TSTA_TMP_adasyn_W`: 0.0
- `TSTA_TMP_adasyn_d`: +0.006 [+0.002, +0.010]
- `TSTA_TMP_adasyn_p`: 0.062
- `TSTA_TMP_adasyn_ph`: 0.312
- `TSTA_TMP_ctgan_W`: 7.0
- `TSTA_TMP_ctgan_d`: +0.001 [-0.004, +0.007]
- `TSTA_TMP_ctgan_p`: 1.000
- `TSTA_TMP_ctgan_ph`: 1.000
- `TSTA_TMP_none_W`: 0.0
- `TSTA_TMP_none_d`: +0.045 [+0.036, +0.058]
- `TSTA_TMP_none_p`: 0.062
- `TSTA_TMP_none_ph`: 0.312
- `TSTA_TMP_ros_W`: 1.0
- `TSTA_TMP_ros_d`: +0.007 [+0.002, +0.013]
- `TSTA_TMP_ros_p`: 0.125
- `TSTA_TMP_ros_ph`: 0.375
- `TSTA_TMP_smote_W`: 5.0
- `TSTA_TMP_smote_d`: +0.003 [-0.004, +0.010]
- `TSTA_TMP_smote_p`: 0.625
- `TSTA_TMP_smote_ph`: 1.000
- `TSTA_ULB_adasyn_W`: 18.0
- `TSTA_ULB_adasyn_d`: +0.002 [-0.003, +0.007]
- `TSTA_ULB_adasyn_p`: 0.375
- `TSTA_ULB_adasyn_ph`: 0.750
- `TSTA_ULB_ctgan_W`: 7.0
- `TSTA_ULB_ctgan_d`: -0.006 [-0.011, -0.001]
- `TSTA_ULB_ctgan_p`: 0.037
- `TSTA_ULB_ctgan_ph`: 0.148
- `TSTA_ULB_none_W`: 15.0
- `TSTA_ULB_none_d`: -0.003 [-0.007, +0.003]
- `TSTA_ULB_none_p`: 0.232
- `TSTA_ULB_none_ph`: 0.697
- `TSTA_ULB_ros_W`: 4.0
- `TSTA_ULB_ros_d`: -0.008 [-0.011, -0.003]
- `TSTA_ULB_ros_p`: 0.014
- `TSTA_ULB_ros_ph`: 0.068
- `TSTA_ULB_smote_W`: 26.0
- `TSTA_ULB_smote_d`: -0.002 [-0.007, +0.002]
- `TSTA_ULB_smote_p`: 0.922
- `TSTA_ULB_smote_ph`: 0.922
- `TST_IEEE_adasyn_W`: 0.0
- `TST_IEEE_adasyn_d`: -0.023 [-0.027, -0.018]
- `TST_IEEE_adasyn_p`: 0.062
- `TST_IEEE_adasyn_ph`: 0.312
- `TST_IEEE_ctgan_W`: 3.0
- `TST_IEEE_ctgan_adasyn_d`: -0.025 [-0.030, -0.019]
- `TST_IEEE_ctgan_adasyn_ph`: 0.250
- `TST_IEEE_ctgan_d`: +0.002 [-0.000, +0.004]
- `TST_IEEE_ctgan_none_d`: -0.014 [-0.017, -0.012]
- `TST_IEEE_ctgan_none_ph`: 0.250
- `TST_IEEE_ctgan_p`: 0.312
- `TST_IEEE_ctgan_ph`: 0.312
- `TST_IEEE_ctgan_ros_d`: -0.056 [-0.059, -0.053]
- `TST_IEEE_ctgan_ros_ph`: 0.250
- `TST_IEEE_ctgan_smote_d`: -0.032 [-0.035, -0.028]
- `TST_IEEE_ctgan_smote_ph`: 0.250
- `TST_IEEE_none_W`: 0.0
- `TST_IEEE_none_d`: -0.013 [-0.015, -0.009]
- `TST_IEEE_none_p`: 0.062
- `TST_IEEE_none_ph`: 0.312
- `TST_IEEE_ros_W`: 0.0
- `TST_IEEE_ros_d`: -0.054 [-0.056, -0.052]
- `TST_IEEE_ros_p`: 0.062
- `TST_IEEE_ros_ph`: 0.312
- `TST_IEEE_smote_W`: 0.0
- `TST_IEEE_smote_d`: -0.030 [-0.032, -0.029]
- `TST_IEEE_smote_p`: 0.062
- `TST_IEEE_smote_ph`: 0.312
- `TST_TMP_adasyn_W`: 0.0
- `TST_TMP_adasyn_d`: +0.023 [+0.012, +0.033]
- `TST_TMP_adasyn_p`: 0.062
- `TST_TMP_adasyn_ph`: 0.312
- `TST_TMP_ctgan_W`: 3.0
- `TST_TMP_ctgan_adasyn_d`: +0.013 [-0.000, +0.028]
- `TST_TMP_ctgan_adasyn_ph`: 0.562
- `TST_TMP_ctgan_d`: +0.010 [-0.002, +0.022]
- `TST_TMP_ctgan_none_d`: +0.030 [+0.015, +0.045]
- `TST_TMP_ctgan_none_ph`: 0.250
- `TST_TMP_ctgan_p`: 0.312
- `TST_TMP_ctgan_ph`: 0.625
- `TST_TMP_ctgan_ros_d`: +0.003 [-0.022, +0.030]
- `TST_TMP_ctgan_ros_ph`: 0.812
- `TST_TMP_ctgan_smote_d`: +0.011 [-0.002, +0.027]
- `TST_TMP_ctgan_smote_ph`: 0.625
- `TST_TMP_none_W`: 0.0
- `TST_TMP_none_d`: +0.040 [+0.025, +0.059]
- `TST_TMP_none_p`: 0.062
- `TST_TMP_none_ph`: 0.312
- `TST_TMP_ros_W`: 5.0
- `TST_TMP_ros_d`: +0.013 [-0.008, +0.034]
- `TST_TMP_ros_p`: 0.625
- `TST_TMP_ros_ph`: 0.625
- `TST_TMP_smote_W`: 1.0
- `TST_TMP_smote_d`: +0.021 [+0.001, +0.035]
- `TST_TMP_smote_p`: 0.125
- `TST_TMP_smote_ph`: 0.375
- `TST_ULB_adasyn_W`: 7.0
- `TST_ULB_adasyn_d`: +0.012 [+0.002, +0.020]
- `TST_ULB_adasyn_p`: 0.037
- `TST_ULB_adasyn_ph`: 0.148
- `TST_ULB_ctgan_W`: 19.0
- `TST_ULB_ctgan_adasyn_d`: +0.014 [+0.004, +0.024]
- `TST_ULB_ctgan_adasyn_ph`: 0.146
- `TST_ULB_ctgan_d`: -0.002 [-0.008, +0.003]
- `TST_ULB_ctgan_none_d`: +0.008 [-0.001, +0.016]
- `TST_ULB_ctgan_none_ph`: 0.320
- `TST_ULB_ctgan_p`: 0.432
- `TST_ULB_ctgan_ph`: 0.645
- `TST_ULB_ctgan_ros_d`: +0.006 [-0.004, +0.016]
- `TST_ULB_ctgan_ros_ph`: 0.496
- `TST_ULB_ctgan_smote_d`: +0.012 [+0.005, +0.018]
- `TST_ULB_ctgan_smote_ph`: 0.039
- `TST_ULB_none_W`: 14.0
- `TST_ULB_none_d`: +0.005 [-0.003, +0.012]
- `TST_ULB_none_p`: 0.193
- `TST_ULB_none_ph`: 0.580
- `TST_ULB_ros_W`: 17.0
- `TST_ULB_ros_d`: +0.003 [-0.004, +0.010]
- `TST_ULB_ros_p`: 0.322
- `TST_ULB_ros_ph`: 0.645
- `TST_ULB_smote_W`: 5.0
- `TST_ULB_smote_d`: +0.009 [+0.003, +0.016]
- `TST_ULB_smote_p`: 0.020
- `TST_ULB_smote_ph`: 0.098
- `ULB_NSEEDS`: 10
- `ULB_PRIMARY`: XGBoost
- `ULB_adasyn_auprc`: 0.826 ± 0.006
- `ULB_adasyn_auprcm`: 0.826
- `ULB_adasyn_f1`: 0.834 ± 0.011
- `ULB_adasyn_f1m`: 0.834
- `ULB_adasyn_f1t`: 0.831 ± 0.011
- `ULB_adasyn_f1tm`: 0.831
- `ULB_adasyn_mcc`: 0.839 ± 0.010
- `ULB_adasyn_mccm`: 0.839
- `ULB_adasyn_prec`: 0.938 ± 0.010
- `ULB_adasyn_precm`: 0.938
- `ULB_adasyn_r`: 0.5×
- `ULB_adasyn_rec`: 0.750 ± 0.018
- `ULB_adasyn_recm`: 0.750
- `ULB_adasyn_roc`: 0.973 ± 0.003
- `ULB_adasyn_rocm`: 0.973
- `ULB_ctgan_auprc`: 0.834 ± 0.003
- `ULB_ctgan_auprcm`: 0.834
- `ULB_ctgan_f1`: 0.848 ± 0.008
- `ULB_ctgan_f1m`: 0.848
- `ULB_ctgan_f1t`: 0.845 ± 0.009
- `ULB_ctgan_f1tm`: 0.845
- `ULB_ctgan_mcc`: 0.851 ± 0.008
- `ULB_ctgan_mccm`: 0.851
- `ULB_ctgan_prec`: 0.937 ± 0.015
- `ULB_ctgan_precm`: 0.937
- `ULB_ctgan_r`: 10×
- `ULB_ctgan_rec`: 0.774 ± 0.013
- `ULB_ctgan_recm`: 0.774
- `ULB_ctgan_roc`: 0.971 ± 0.002
- `ULB_ctgan_rocm`: 0.971
- `ULB_diff_stop`: 2025 ± 162
- `ULB_diffusion_auprc`: 0.828 ± 0.006
- `ULB_diffusion_auprcm`: 0.828
- `ULB_diffusion_f1`: 0.845 ± 0.009
- `ULB_diffusion_f1m`: 0.845
- `ULB_diffusion_f1t`: 0.843 ± 0.007
- `ULB_diffusion_f1tm`: 0.843
- `ULB_diffusion_mcc`: 0.850 ± 0.009
- `ULB_diffusion_mccm`: 0.850
- `ULB_diffusion_prec`: 0.946 ± 0.017
- `ULB_diffusion_precm`: 0.946
- `ULB_diffusion_r`: 0.5×
- `ULB_diffusion_rec`: 0.764 ± 0.013
- `ULB_diffusion_recm`: 0.764
- `ULB_diffusion_roc`: 0.973 ± 0.002
- `ULB_diffusion_rocm`: 0.973
- `ULB_nfit`: 159,491
- `ULB_nfitfraud`: 275
- `ULB_none_auprc`: 0.831 ± 0.005
- `ULB_none_auprcm`: 0.831
- `ULB_none_f1`: 0.840 ± 0.011
- `ULB_none_f1m`: 0.840
- `ULB_none_f1t`: 0.839 ± 0.010
- `ULB_none_f1tm`: 0.839
- `ULB_none_mcc`: 0.846 ± 0.010
- `ULB_none_mccm`: 0.846
- `ULB_none_prec`: 0.956 ± 0.011
- `ULB_none_precm`: 0.956
- `ULB_none_r`: none
- `ULB_none_rec`: 0.749 ± 0.018
- `ULB_none_recm`: 0.749
- `ULB_none_roc`: 0.971 ± 0.003
- `ULB_none_rocm`: 0.971
- `ULB_nval`: 39,873
- `ULB_nvalfraud`: 69
- `ULB_ros_auprc`: 0.836 ± 0.003
- `ULB_ros_auprcm`: 0.836
- `ULB_ros_f1`: 0.842 ± 0.010
- `ULB_ros_f1m`: 0.842
- `ULB_ros_f1t`: 0.845 ± 0.010
- `ULB_ros_f1tm`: 0.845
- `ULB_ros_mcc`: 0.846 ± 0.009
- `ULB_ros_mccm`: 0.846
- `ULB_ros_prec`: 0.939 ± 0.014
- `ULB_ros_precm`: 0.939
- `ULB_ros_r`: 1×
- `ULB_ros_rec`: 0.764 ± 0.015
- `ULB_ros_recm`: 0.764
- `ULB_ros_roc`: 0.974 ± 0.002
- `ULB_ros_rocm`: 0.974
- `ULB_smote_auprc`: 0.830 ± 0.004
- `ULB_smote_auprcm`: 0.830
- `ULB_smote_f1`: 0.836 ± 0.009
- `ULB_smote_f1m`: 0.836
- `ULB_smote_f1t`: 0.840 ± 0.010
- `ULB_smote_f1tm`: 0.840
- `ULB_smote_mcc`: 0.837 ± 0.009
- `ULB_smote_mccm`: 0.837
- `ULB_smote_prec`: 0.879 ± 0.015
- `ULB_smote_precm`: 0.879
- `ULB_smote_r`: 5×
- `ULB_smote_rec`: 0.797 ± 0.010
- `ULB_smote_recm`: 0.797
- `ULB_smote_roc`: 0.972 ± 0.002
- `ULB_smote_rocm`: 0.972
