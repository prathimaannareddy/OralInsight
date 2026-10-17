# Week 6 — Explainable AI Analysis

## 1. Methods and implementation (`src/explainability.py`)
| Method | Implementation | Target |
|---|---|---|
| **Grad-CAM** (primary) [Selvaraju et al., ICCV 2017] | Forward hook on the last convolutional block of MobileNetV3-Large (`features[-1]`, 7 × 7 grid). Channel weights = spatially averaged gradients of the predicted-class logit; ReLU; bilinear up-sampling to 224 × 224 | Predicted class |
| **Grad-CAM++** [Chattopadhay et al., WACV 2018] | Same layer, higher-order gradient weighting | Predicted class |
| **Integrated Gradients** [Sundararajan et al., ICML 2017] | Captum, black-image baseline, 32 steps; absolute attribution summed over channels; Gaussian smoothing (σ = 4) | Predicted class |

**Which model explains which image**
- All 258 development images are explained **out of fold**, using configuration E13 (the final configuration). Each image is explained by the fold model that never saw it.
- The 65 test images are explained by the final model, **after** the single test evaluation.
- In-place activations are disabled in the loaded copy, so gradient hooks work; model weights are unchanged.

**Output folders** (one 4-panel figure per image: input, Grad-CAM, Grad-CAM++, IG):

| Folder | Content |
|---|---|
| `results/xai/correct/` | 209 images |
| `results/xai/incorrect/` | 49 images |
| `results/xai/false_positive/` | 26 images |
| `results/xai/false_negative/` | 23 images |
| `results/xai/class_benign/` | 132 images |
| `results/xai/class_malignant/` | 126 images |
| `results/xai/test/...` | 65 test images, same sub-folders |

Representative grids: `results/figures/fig_gradcam_examples.png` (out-of-fold) and `fig_gradcam_test_examples.png` (test).

## 2. Quantitative checks (out of fold, n = 258)
| Check | Result | Reading |
|---|---|---|
| Border ratio: share of Grad-CAM mass in the outer 15% band (the band covers 51% of the area) | 0.297 | Attention is concentrated towards the centre of the image, where photographers usually place the lesion. This is not proof that the lesion itself is attended. |
| Agreement Grad-CAM vs Grad-CAM++ (Spearman, 28 × 28) | 0.859 | The two CAM variants largely agree. |
| Agreement Grad-CAM vs Integrated Gradients | 0.388 | Only partial agreement: gradient-path and activation-map methods emphasise different pixels. |
| Deletion test: drop in predicted-class probability, top-20% Grad-CAM pixels vs 20% random pixels (replaced by mean colour) | 0.264 vs 0.105 | The highlighted regions matter more than random regions. The top-20% deletion hurts more than random deletion in 65% of images. |
| Model-randomisation sanity check (stratified, n = 40; Spearman, trained vs re-initialised last block + classifier) | mean 0.092 | Maps change substantially when the learned weights are destroyed, so they are not just edge detectors. |

The held-out test set (n = 65) gives a similar picture:
- border ratio 0.286;
- agreement with Grad-CAM++ 0.868 and with IG 0.450;
- deletion top-20% 0.215 vs random 0.161.

## 3. Correct vs incorrect predictions — Table 7 (XAI observations, out of fold)
| Outcome | n | Border ratio | Agree CAM++ | Agree IG | Deletion top-20% | Deletion random | Sanity ρ | Qualitative observation |
|---|---|---|---|---|---|---|---|---|
| TP | 103 | 0.284 | 0.871 | 0.380 | 0.300 | 0.068 | 0.221 | Attention usually concentrates on the discoloured, white or red mucosal area that dominates the image (e.g. pigmented buccal mucosa, a white patch); retractor mirrors and gloves receive little weight. |
| TN | 106 | 0.294 | 0.891 | 0.428 | 0.152 | 0.104 | 0.029 | Attention is often on the tongue dorsum or central mucosa, but in some images spreads to lips, teeth, skin and even clothing at the image border - a correct output does not imply lesion-focused reasoning. |
| FP | 26 | 0.314 | 0.812 | 0.381 | 0.421 | 0.153 | 0.233 | Several false positives concentrate on heavily stained or decayed teeth, calculus and restorations, or on bulky/bleeding tissue, rather than on the mucosal lesion. |
| FN | 23 | 0.350 | 0.717 | 0.249 | 0.443 | 0.218 | -0.114 | Attention is more peripheral and the methods agree less; in the clearest cases the map highlights teeth or the image edge and misses a central red or discoloured area (e.g. palate, tongue patches). |

Test-set breakdown:

| Outcome | n | Border ratio | Agree CAM++ | Agree IG | Deletion top-20% | Deletion random |
|---|---|---|---|---|---|---|
| TP | 28 | 0.342 | 0.869 | 0.423 | 0.226 | 0.134 |
| TN | 30 | 0.231 | 0.883 | 0.475 | 0.159 | 0.146 |
| FP | 3 | 0.293 | 0.735 | 0.398 | 0.541 | 0.489 |
| FN | 4 | 0.305 | 0.847 | 0.494 | 0.314 | 0.224 |

## 4. Observations
1. **Correct malignant predictions** mostly attend to the visibly abnormal mucosa that fills the frame. This is consistent with the model using lesion appearance in these images. It does **not** show that the model has learned clinically meaningful features: without lesion masks, overlap could not be measured.
2. **Correct benign predictions are not always lesion-focused.** Several highlight lips, teeth, skin or clothing near the border. A right answer can rest on context.
3. **False positives** repeatedly focus on stained or decayed teeth, calculus, restorations or bulky bleeding tissue. Saldivia-Siracusa et al. (2025) reported the same kind of off-lesion attention (teeth, gloves, retractors). In this dataset, poor dentition and plaque may co-occur with malignant cases and act as a contextual cue.
4. **False negatives** have the most peripheral attention and the lowest agreement between methods. The model is least certain about *where* to look exactly where it fails, which is useful when reading an individual explanation in the prototype.
5. **Explanations and the resolution shortcut.** The selected configuration was chosen partly because it is insensitive to synthetic down-sampling (`ERROR_ANALYSIS.md`). Its Grad-CAM maps still cannot reveal whether acquisition properties that are spread over the whole image (camera, compression) influence the output. Pixel-level saliency is not suited to detecting such global cues.

## 5. Limitations of the explanations
- Grad-CAM for MobileNetV3 is computed on a 7 × 7 grid, so maps are coarse and cannot outline small lesions.
- Grad-CAM, Grad-CAM++ and IG agree only partially; there is no single "true" explanation.
- No lesion annotations exist, so "clinically meaningful" could only be judged visually by non-clinicians (the student team). These judgements need confirmation by an oral-medicine specialist.
- Heat maps show which pixels influenced this network. They do not prove lesion location, malignancy or medical causality, and they must not be used as diagnostic evidence.
- The sanity check randomises only the top layers (cascading randomisation of the full network was not performed).
