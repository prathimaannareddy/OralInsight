# Week 3 — Baseline Results

**Model:** small CNN trained from scratch (`BaselineCNN` in `src/common.py`; 4 conv blocks of 32/64/128/256 filters, global average pooling, dropout 0.3, 1,173,730 parameters).
**Protocol:** 5-fold stratified group cross-validation on the 258 development images. The held-out test split is not used here.
Source files: `baseline_results.csv`, `results/metrics/cv/baseline_cnn/`, `results/figures/fig_baseline_curves.png`.

## Experimental record (reproducibility)
| Item | Value |
|---|---|
| Random seed | 42 (Python, NumPy, PyTorch; seeded DataLoader) |
| Image size | 224 × 224 (from 256 × 256 cache) |
| Batch size | 16 |
| Optimizer | AdamW, learning rate 0.001, weight decay 0.0001 |
| Schedule | cosine annealing over 30 epochs, last epoch kept |
| Loss | cross-entropy |
| Augmentation | random resized crop (0.7–1.0), horizontal + vertical flip, rotation ±20°, colour jitter (0.2) |
| Hardware / software | AMD Ryzen 5 7530U CPU, PyTorch 2.13.0+cpu (see `ENVIRONMENT.md`) |
| Mean training time per fold | 1756 s |

## Per-fold validation results (threshold 0.5)
| Fold | n | Accuracy | Precision | Recall | Specificity | F1 | Macro-F1 | ROC-AUC | TN/FP/FN/TP |
|---|---|---|---|---|---|---|---|---|---|
| 0 | 52 | 0.577 | 0.583 | 0.538 | 0.615 | 0.560 | 0.576 | 0.578 | 16/10/12/14 |
| 1 | 52 | 0.538 | 0.522 | 0.480 | 0.593 | 0.500 | 0.536 | 0.570 | 16/11/13/12 |
| 2 | 51 | 0.549 | 0.533 | 0.640 | 0.462 | 0.582 | 0.546 | 0.602 | 12/14/9/16 |
| 3 | 52 | 0.769 | 0.810 | 0.680 | 0.852 | 0.739 | 0.766 | 0.779 | 23/4/8/17 |
| 4 | 51 | 0.667 | 0.722 | 0.520 | 0.808 | 0.605 | 0.658 | 0.775 | 21/5/12/13 |
| **Mean ± SD** | | **0.620 ± 0.097** | 0.634 ± 0.126 | 0.572 ± 0.085 | 0.666 ± 0.162 | 0.597 ± 0.088 | **0.617 ± 0.096** | **0.661 ± 0.107** | |

Pooled out-of-fold (n = 258): accuracy 0.620, macro-F1 0.619, ROC-AUC 0.653; confusion matrix TN 88, FP 44, FN 54, TP 72 (sums to 258).

## Class-wise (pooled out-of-fold)
| Class | Support | Precision | Recall | F1 |
|---|---|---|---|---|
| benign | 132 | 0.620 | 0.667 | 0.642 |
| malignant | 126 | 0.621 | 0.571 | 0.595 |

## Observations
- Trained from random weights on 206–207 images per fold, the baseline learns only a weak signal. Its mean CV AUC of 0.661 is well below the pretrained models (`MODEL_COMPARISON.csv`).
- Results vary widely across folds (accuracy SD 0.097).
- Learning curves (`fig_baseline_curves.png`): the epoch of minimum validation loss differs by fold (fold 0: epoch 13, fold 1: epoch 1, fold 2: epoch 12, fold 3: epoch 24, fold 4: epoch 21). Where the minimum comes early, validation loss rises afterwards while training loss keeps falling (over-fitting); in the remaining folds it is still improving late in the schedule. A single fixed schedule is therefore a compromise for this model.
- An earlier run under protocol v1 (early stopping on validation loss) gave macro-F1 0.597 ± 0.163 and collapsed to a single class in one fold. That protocol was abandoned for all models (`docs/EXPERIMENT_LOG.md`).
- The baseline is kept as the reference point for the statistical comparison in Week 7.
