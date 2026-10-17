# Week 7 — Final Evaluation on the Held-out Test Set

**Protocol**
- The test split (65 images: 33 benign, 32 malignant; 20% of the duplicate groups, stratified) was fixed on 2026-10-09, before any modelling.
- It was not used for training, model selection, tuning, error analysis or explanation analysis.
- Every model was trained on the 258 development images only and evaluated **once** at the fixed threshold 0.5 (`src/evaluate.py`).
- 95% confidence intervals come from 2,000 stratified bootstrap resamples.

**Final model:** MobileNetV3-Large, tuning configuration E13 (resolution-degradation augmentation), `models/final_model.pt`.

## Results of all models (`FINAL_RESULTS.csv`)
| Model | Params (M) | Accuracy | Sensitivity | Specificity | Precision | F1 | ROC-AUC | TN/FP/FN/TP | CPU ms/img |
|---|---|---|---|---|---|---|---|---|---|
| Baseline CNN | 1.17 | 0.662 [0.554, 0.769] | 0.562 [0.406, 0.750] | 0.758 [0.606, 0.909] | 0.692 [0.556, 0.850] | 0.621 [0.480, 0.762] | 0.765 [0.645, 0.875] | 25/8/14/18 | 87 |
| MobileNetV3-Large | 4.20 | 0.877 [0.800, 0.954] | 0.781 [0.625, 0.906] | 0.970 [0.909, 1.000] | 0.962 [0.875, 1.000] | 0.862 [0.755, 0.951] | 0.943 [0.873, 0.989] | 32/1/7/25 | 25 |
| EfficientNet-B0 | 4.01 | 0.862 [0.785, 0.938] | 0.750 [0.594, 0.906] | 0.970 [0.909, 1.000] | 0.960 [0.880, 1.000] | 0.842 [0.731, 0.933] | 0.957 [0.900, 0.998] | 32/1/8/24 | 37 |
| ResNet-50 | 23.51 | 0.831 [0.738, 0.923] | 0.812 [0.656, 0.938] | 0.848 [0.697, 0.970] | 0.839 [0.727, 0.955] | 0.825 [0.721, 0.918] | 0.920 [0.842, 0.980] | 28/5/6/26 | 127 |
| OralInsight final (tuned) | 4.20 | 0.892 [0.815, 0.954] | 0.875 [0.750, 0.969] | 0.909 [0.788, 1.000] | 0.903 [0.806, 1.000] | 0.889 [0.806, 0.955] | 0.941 [0.874, 0.989] | 30/3/4/28 | 28 |

**Consistency checks (run in the generating script)**
- The final model's confusion matrix sums to 65.
- Accuracy recomputed as (TN+TP)/n = 0.892 matches the reported value.
- Sensitivity = 28/32 and specificity = 30/33.

## Class-wise results, final model
| Class | Support | Precision | Recall | F1 |
|---|---|---|---|---|
| benign | 33 | 0.882 | 0.909 | 0.896 |
| malignant | 32 | 0.903 | 0.875 | 0.889 |

## Statistical comparison: final model vs each other model (`results/tables/statistical_tests.csv`)
| Comparison | Final right & other wrong / final wrong & other right | Exact McNemar p | ΔAUC [95% CI] | Bootstrap p |
|---|---|---|---|---|
| final vs Baseline CNN | 17 / 2 | 0.0007 | +0.176 [+0.063, +0.292] | 0.003 |
| final vs MobileNetV3-Large | 4 / 3 | 1.0000 | -0.002 [-0.031, +0.029] | 0.932 |
| final vs EfficientNet-B0 | 6 / 4 | 0.7539 | -0.016 [-0.061, +0.024] | 0.430 |
| final vs ResNet-50 | 8 / 4 | 0.3877 | +0.022 [-0.025, +0.079] | 0.373 |

## Interpretation
- **Against the baseline**, the final model is significantly better: McNemar p = 0.0007, ΔAUC = +0.176 [+0.063, +0.292]. Transfer learning matters on a dataset of this size.
- **Against the three candidate architectures**, there is **no statistically significant difference**: all McNemar p ≥ 0.39, and every ΔAUC interval includes 0.
  - The final model has the highest sensitivity of the five (0.875).
  - The untuned MobileNetV3-Large and EfficientNet-B0 have higher specificity.
  - With 65 images, differences of a few cases cannot be resolved.
- **Test vs cross-validation.** Test accuracy (0.892) is higher than the cross-validation estimate for the same configuration (accuracy 0.810 ± 0.032, AUC 0.893 ± 0.046). The test CI (0.815–0.954) is wide, so the gap is consistent with sampling variability. The CV estimate is the more conservative expectation.
- **Confidence.** Correct predictions had median confidence 0.998; wrong ones 0.860.

## Test-set errors of the final model
| File | True class | P(malignant) | Type |
|---|---|---|---|
| benign/101.jpg | benign | 0.750 | FP |
| benign/16.jpg | benign | 0.860 | FP |
| benign/69.jpg | benign | 0.543 | FP |
| malignant/144.jpg | malignant | 0.009 | FN |
| malignant/53.jpg | malignant | 0.000 | FN |
| malignant/79.jpg | malignant | 0.088 | FN |
| malignant/85.jpg | malignant | 0.410 | FN |

Explanations for these images are in `results/xai/test/false_positive/` and `results/xai/test/false_negative/`.

## Figures
- `results/figures/fig_test_confusion_matrices.png` — confusion matrices of all models.
- `results/figures/fig_test_roc.png` — ROC curves.
- `results/figures/fig_gradcam_test_examples.png` — Grad-CAM on test images.

## Caveats
- Single source, single held-out split: these numbers estimate performance on photographs like these, not on other cameras, clinics or populations.
- External and clinical validation have not been performed.
