# Environment

All experiments in this project were run on the machine described below. No GPU was available, so every model was trained on CPU.

| Item | Value |
|---|---|
| OS | Windows 11 Home Single Language 10.0.26200 (64-bit) |
| CPU | AMD Ryzen 5 7530U (6 cores / 12 threads) |
| RAM | 14 GB |
| GPU | None (CPU-only PyTorch build `2.13.0+cpu`) |
| Python | 3.13.15 (MSC v.1944, AMD64) |
| PyTorch / torchvision | 2.13.0+cpu / 0.28.0+cpu |
| PyTorch intra-op threads | 8 (set in `src/common.py`: `torch.set_num_threads(min(8, os.cpu_count()))`) |
| Other packages | see `requirements.txt` (exact versions) |
| Document tools | Microsoft Word (DOCX -> PDF conversion), python-docx, python-pptx |

## Pretrained weights (torchvision)

| Model | Weights enum | File |
|---|---|---|
| ResNet-50 | `ResNet50_Weights.IMAGENET1K_V2` | resnet50-11ad3fa6.pth |
| EfficientNet-B0 | `EfficientNet_B0_Weights.IMAGENET1K_V1` | efficientnet_b0_rwightman-7f5810bc.pth |
| MobileNetV3-Large | `MobileNet_V3_Large_Weights.IMAGENET1K_V2` | mobilenet_v3_large-5c1a4163.pth |

## Reproducibility controls

- Global seed 42 (`random`, `numpy`, `torch`), seeded `DataLoader` shuffling, `num_workers=0`.
- The data split is deterministic (`StratifiedGroupKFold(shuffle=True, random_state=42)`) and stored in `data/splits.csv`.
- Dataset version: Mendeley Data doi:10.17632/mhjyrn35p4.2. Every file is checked against the published SHA-256 hash (`data/raw/manifest.csv`).
- Some PyTorch CPU kernels are not bit-wise deterministic across machines or thread counts. Re-running on different hardware can change metrics in the last decimal places; the split and protocol stay identical.

## Setup

```bash
python -m venv .venv
.venv\Scripts\activate          # Windows  (Linux/macOS: source .venv/bin/activate)
pip install -r requirements.txt
```

## Trained model files

| File | Content |
|---|---|
| `models/final_model.pt` | Final model: MobileNetV3-Large, tuning configuration E13, trained on all 258 development images for 15 epochs; threshold 0.5. Contains `state_dict`, `cfg`, `epochs`, `threshold`. |
| `models/final_model_card.json` | Held-out test metrics shown in the prototype sidebar |
| `models/{baseline_cnn,mobilenet_v3_large,efficientnet_b0,resnet50}.pt` | Week 3–4 models re-trained on all development images (used in the test comparison) |
| `models/folds/<tag>/fold{0-4}.pt` | Cross-validation fold models of the tuning experiments (used for the out-of-fold error and XAI analyses) |

Approximate CPU training times on this machine are recorded in `MODEL_COMPARISON.csv` and `EXPERIMENT_TABLE.csv`.
