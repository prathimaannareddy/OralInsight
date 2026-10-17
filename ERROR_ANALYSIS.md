# Week 5 — Error Analysis

**Model analysed:** the selected configuration **E13** (MobileNetV3-Large with resolution-degradation augmentation), using **out-of-fold** predictions on the 258 development images. Each image is scored by the fold model that did not train on it. The test split is not used here; test-set errors are reported in `FINAL_EVALUATION.md`.

**Files:**
- Script: `src/error_analysis.py`
- Per-image table: `error_analysis.csv`
- Result tables: `results/tables/error_profile.csv`, `degradation_probe.csv`, `degradation_probe_E00.csv`, `shortcut_pixel_probe.csv`, `performance_by_resolution.csv`
- Figures: `results/figures/fig_error_examples.png`, `fig_degradation_probe_compare.png`

## 1. Outcome counts (threshold 0.5)
| TP | TN | FP | FN | Total |
|---|---|---|---|---|
| 103 | 106 | 26 | 23 | 258 |

- **False positives** (benign called malignant): 26 of 132 benign images.
- **False negatives** (malignant called benign): 23 of 126 malignant images.
- In a screening setting, false negatives are the more serious error.

## 2. Highest-confidence errors
| File | Outcome | P(malignant) | Original MP | Camera (EXIF) |
|---|---|---|---|---|
| benign/36.jpg | FP | 1.000 | 0.39 | none/unknown |
| benign/29.jpg | FP | 1.000 | 0.16 | none/unknown |
| benign/150.jpg | FP | 1.000 | 0.33 | none/unknown |
| benign/33.jpg | FP | 1.000 | 0.10 | none/unknown |
| benign/11.jpg | FP | 1.000 | 0.37 | samsung SM-G360H |
| malignant/133.jpg | FN | 0.000 | 0.63 | none/unknown |
| malignant/86.jpg | FN | 0.000 | 0.42 | none/unknown |
| malignant/136.jpg | FN | 0.004 | 0.21 | none/unknown |
| malignant/59.jpg | FN | 0.020 | 0.28 | none/unknown |
| malignant/40.jpg | FN | 0.020 | 0.24 | none/unknown |

Many errors are made with very high confidence:

| Median confidence | Benign images | Malignant images |
|---|---|---|
| Wrong predictions | 0.977 | 0.823 |
| Correct predictions | 0.999 | 0.998 |

The model's probabilities are poorly calibrated, so a high probability must not be read as certainty.

## 3. Visually confusing cases
Observations from `fig_error_examples.png` and the error panels in `results/xai/false_positive/` and `results/xai/false_negative/`. They describe image appearance, not diagnoses.
- **False positives** are mostly benign lesions with bulky, nodular, red or bleeding surfaces, or with heavy plaque or debris on adjacent teeth. Several include teeth, restorations or instruments in the frame.
- **False negatives** are mostly flat, small or subtle changes (a small red area on the palate, diffuse discolouration of the tongue, a small lesion at the commissure), or wide views in which the lesion covers a small part of the image.

## 4. Error profile vs. image properties
Mann–Whitney U test comparing correct and wrong predictions within each true class:

| Property | Class | Median (correct) | Median (error) | p |
|---|---|---|---|---|
| megapixels | benign | 0.447 | 0.212 | 0.0000 |
| megapixels | malignant | 0.179 | 0.228 | 0.1765 |
| bytes | benign | 95129.000 | 62479.500 | 0.0002 |
| bytes | malignant | 59655.000 | 60907.000 | 0.4715 |
| sharpness_laplacian_var | benign | 206.500 | 202.367 | 0.8615 |
| sharpness_laplacian_var | malignant | 194.809 | 144.251 | 0.0113 |
| brightness | benign | 132.852 | 124.358 | 0.0625 |
| brightness | malignant | 126.063 | 117.757 | 0.3974 |
| colourfulness | benign | 42.133 | 39.732 | 0.2374 |
| colourfulness | malignant | 42.069 | 39.939 | 0.4299 |
| confidence | benign | 0.999 | 0.977 | 0.0001 |
| confidence | malignant | 0.998 | 0.823 | 0.0000 |

- **Benign errors** come from markedly lower-resolution photographs: median 0.21 MP vs 0.45 MP for correct benign predictions (p = 9.8e-07).
- **Malignant errors** come from somewhat higher-resolution photographs (0.23 vs 0.18 MP, p = 0.177) and from images with lower sharpness in the preprocessed image (p = 0.011).

## 5. Performance by original resolution (tertiles)
| Tertile | MP range | n | Accuracy | Sensitivity | Specificity | AUC |
|---|---|---|---|---|---|---|
| low | 0.009-0.192 | 86 (21 B / 65 M) | 0.744 | 0.846 | 0.429 | 0.771 |
| mid | 0.195-0.395 | 86 (44 B / 42 M) | 0.779 | 0.810 | 0.750 | 0.861 |
| high | 0.397-18.109 | 86 (67 B / 19 M) | 0.907 | 0.737 | 0.955 | 0.917 |

Low-resolution photographs are mostly malignant and high-resolution ones mostly benign, so the model is weakest where an image breaks this pattern:
- specificity is only 0.429 in the low-resolution tertile;
- sensitivity is 0.737 in the high-resolution tertile.

## 6. Shortcut probes
- **Correlation with resolution.** The model's P(malignant) correlates with original resolution: Spearman ρ = -0.532 with log megapixels.
- **Global pixel statistics.** Sharpness, brightness and colourfulness of the preprocessed 256-px image predict the label only weakly (CV AUC 0.560 using all three). The confound is therefore not a simple global image statistic.
- **Counterfactual degradation** (`fig_degradation_probe_compare.png`):

| Perturbation | Benign→malignant flips (E00) | Benign→malignant flips (E13, selected) | Malignant→benign flips (E00) | Malignant→benign flips (E13) | AUC (E00) | AUC (E13) |
|---|---|---|---|---|---|---|
| Down-sample to 64 px and back | 0.462 | 0.019 | 0.000 | 0.126 | 0.867 | 0.882 |
| Gaussian blur (r = 2) | 0.358 | 0.047 | 0.010 | 0.049 | 0.866 | 0.881 |
| JPEG quality 20 | 0.189 | 0.038 | 0.029 | 0.126 | 0.873 | 0.874 |
| Brightness −25% | 0.019 | 0.019 | 0.029 | 0.039 | 0.903 | 0.889 |

**Interpretation**
- With the Week 4 configuration (E00), lowering image quality pushed benign images towards "malignant": 46% flipped after down-sampling. The network had learned that blurry or low-resolution images tend to be malignant in this dataset.
- Resolution-degradation augmentation (E13) removed most of this behaviour (2% flips) at a similar CV macro-F1.
- However, the selected model's output **still** correlates with the original resolution, and low-resolution benign images are still its main source of false positives.
  - In this dataset, resolution travels together with other acquisition properties (camera, framing, compression history) and possibly with genuine differences between the lesion groups.
  - Augmentation removes sensitivity to blur, but it cannot remove this confound.
- This is a **dataset limitation** that tuning cannot fix; it can only be resolved with external, acquisition-balanced data.

## 7. Dataset limitations exposed by the errors
- Class-correlated acquisition (resolution, camera model); see `DATASET_CARD.md`.
- Heterogeneous framing: close-ups vs whole-mouth views, with teeth, instruments and lips in the frame.
- No lesion masks, so it is not possible to check whether a wrong prediction attended to the lesion.
- Binary labels hide the lesion types that are hardest to separate, e.g. proliferative benign lesions vs carcinoma.
- No histopathology information, so it is not possible to check whether some errors are label errors.
