# Running the OralInsight Prototype

> **Research prototype — not a medical device.** It outputs an image-classification model prediction and a model-generated Grad-CAM explanation. It does not diagnose oral cancer and has not been clinically validated.

## 1. Requirements
- Python 3.13 (tested with 3.13.15) and the packages in `requirements.txt`
- The trained model `models/final_model.pt` (created by `python src/finalize_model.py`)
- CPU only; no GPU is needed. One prediction plus Grad-CAM took 148 ms on average on the project laptop (AMD Ryzen 5 7530U), measured over the 65 test images.

## 2. Install
Run these from the project folder `OralInsight/`:

```bash
python -m venv .venv
```
```bash
.venv\Scripts\activate
```
(On Linux/macOS use `source .venv/bin/activate` instead.)
```bash
pip install -r requirements.txt
```

## 3. Start the web app
```bash
streamlit run prototype/app.py
```
Streamlit prints a local URL (default http://localhost:8501). Open it in a browser and upload a JPG/PNG photograph of an oral lesion. The app shows:
1. the preprocessed model input (224 × 224);
2. the Grad-CAM overlay for the predicted class;
3. the predicted class (*Benign-like* / *Malignant-like*) and P(malignant) / P(benign);
4. a low-confidence warning when 0.35 ≤ P(malignant) ≤ 0.65;
5. the non-diagnostic disclaimer.

To use a different checkpoint, set the environment variable `OI_MODEL`.
- Windows PowerShell: `$env:OI_MODEL="models/resnet50.pt"`
- Linux/macOS: `export OI_MODEL=models/resnet50.pt`

## 4. Command-line use (no browser)
```bash
python src/inference.py data/raw/original_data/malignant/10.jpg --out overlay.png
```
This prints the predicted class and probability and writes the Grad-CAM overlay to `overlay.png`.

## 5. Preprocessing used by the prototype
Identical to training (`src/inference.py`):
1. EXIF orientation;
2. RGB conversion;
3. resize to 256 × 256 (Lanczos), then to 224 × 224;
4. ImageNet mean/std normalisation.

The decision threshold is fixed at 0.5, as in the evaluation.

## 6. Prototype test log (2026-10-09, final model, test images, after the Week 7 evaluation)

**A. Automated pipeline check.** `src/inference.py` (the code path used by the app) was run on all 65 raw test photographs and compared with `results/metrics/test_predictions_final.csv` from `src/evaluate.py`:
- maximum absolute difference in P(malignant): 8.4e-07;
- prediction agreement: 100%.

Details are in `results/tables/prototype_consistency_test.csv`. Mean time per image, including Grad-CAM: 148 ms (CPU).

**B. Browser test of the Streamlit app** (in-app browser, `streamlit run prototype/app.py`). Seven test photographs were uploaded:

| Image | True class | App output | App P(malignant) | Evaluation P(malignant) | Low-confidence warning | Note |
|---|---|---|---|---|---|---|
| malignant/10.jpg | malignant | Malignant-like | 0.999 | 0.999 | – | correct |
| malignant/101.jpg | malignant | Malignant-like | 0.981 | 0.981 | – | correct |
| benign/104.jpg | benign | Benign-like | 0.001 | 0.001 | – | correct |
| benign/105.jpg | benign | Benign-like | 0.000 | 0.000 | – | correct |
| benign/101.jpg | benign | Malignant-like | 0.750 | 0.750 | – | model error |
| malignant/144.jpg | malignant | Benign-like | 0.009 | 0.009 | – | model error |
| benign/69.jpg | benign | Malignant-like | 0.543 | 0.543 | shown | model error |

**Results**
- Every app output matched the evaluation pipeline.
- The page showed the input, the Grad-CAM overlay, both probabilities and the disclaimer.
- The low-confidence warning appeared only for the image with 0.35 ≤ P ≤ 0.65.
- Model errors (benign/101 false positive, malignant/144 false negative) are displayed as such. The app gives no indication that a prediction is wrong, which is why the disclaimer is essential.
- Screenshot: `results/figures/fig_prototype.png` (Figure 7 of the paper).

**Fixes made during testing**
- A truncated class label (`st.metric` → heading).
- The deprecated `use_container_width` argument (→ `width="stretch"`).

