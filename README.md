# OralInsight — Explainable AI for Oral Lesion Screening

Undergraduate research project (8 weeks) · Supervisor: **Dr. Pankaj Pandey**
Team: Student 1 [NAME / REGISTRATION NUMBER] · Student 2 [NAME / REGISTRATION NUMBER]

OralInsight classifies clinical photographs of oral lesions as **benign** or **malignant**. It uses transfer-learning CNNs evaluated under a leakage-controlled protocol, and explains every prediction with Grad-CAM. It is a **research prototype — not a medical device, not a diagnostic tool, and not clinically validated.**

## Results at a glance
| | |
|---|---|
| Dataset | Mendeley *Oral Images Dataset*: 323 original clinical photographs (165 benign, 158 malignant), CC BY 4.0 |
| Split | 65 held-out test images; 258 development images in 5 duplicate-group-aware folds |
| Models | Baseline CNN (from scratch); MobileNetV3-Large, EfficientNet-B0, ResNet-50 (ImageNet, fully fine-tuned) |
| Final model | MobileNetV3-Large + resolution-degradation augmentation (tuning experiment E13), `models/final_model.pt` |
| **Test accuracy** | **0.892** (95% CI 0.815–0.954) |
| Sensitivity / specificity | 0.875 / 0.909 |
| F1 / ROC-AUC | 0.889 / 0.941 (95% CI 0.874–0.989) |
| vs baseline | McNemar p = 0.0007; ΔAUC +0.176 |
| vs other pretrained models | no statistically significant difference |
| Leakage replication | training on the repository's augmented copies: accuracy 1.000; copies removed: 0.788 |
| Shortcut finding | 64-px down-sampling flipped 46% of benign predictions (Week 4 model) vs 2% (final) |
| XAI | Grad-CAM (primary), Grad-CAM++, Integrated Gradients, with sanity, faithfulness and agreement checks |

Full details: `FINAL_EVALUATION.md`, `ERROR_ANALYSIS.md`, `XAI_ANALYSIS.md`, and the paper `paper/OralInsight_Final_Paper.pdf`.


## Repository layout

```
OralInsight/
├── README.md, PROJECT_PROGRESS.md, FINAL_COMPLETION_CHECKLIST.md, FINAL_AUDIT.md, FINAL_DELIVERABLES.md
├── ENVIRONMENT.md, requirements.txt, RUN_PROTOTYPE.md
├── DATASET_CARD.md, dataset_statistics.csv
├── baseline_results.csv, BASELINE_RESULTS.md        (Week 3)
├── MODEL_COMPARISON.csv                              (Week 4)
├── EXPERIMENT_TABLE.csv, ERROR_ANALYSIS.md, error_analysis.csv   (Week 5)
├── XAI_ANALYSIS.md                                   (Week 6)
├── FINAL_RESULTS.csv, FINAL_EVALUATION.md            (Week 7)
├── data/         raw/ (download + audit sample), cleaned/ (256 px PNG), train/ validation/ test/, splits.csv
├── src/          all code (see below)
├── models/       final checkpoints (+ folds/ for out-of-fold analyses)
├── results/      figures/ tables/ metrics/ xai/ logs/
├── prototype/    Streamlit app
├── literature/   literature_matrix.csv, references_verified.csv, papers/ (extracted text), verification/
├── docs/         WEEK1_REPORT.md, RESEARCH_GAP_EVIDENCE.md, DATASET_COMPARISON.csv, EXPERIMENT_LOG.md
├── paper/        OralInsight_Final_Paper.docx/.pdf, Week 1 submission, build scripts
└── presentation/ OralInsight_Final_Presentation.pptx
```

## Reproducing the project

Run all commands from the project root, after `pip install -r requirements.txt` (see `ENVIRONMENT.md`). Times are for a 6-core laptop CPU.

| Step | Command | What it does |
|---|---|---|
| 1 | `python src/verify_references.py` | Re-verifies every DOI against Crossref |
| 2 | `python src/download_dataset.py` | Downloads the 323 originals and checks SHA-256 |
| 3 | `python src/dataset_analysis.py` | Inventory, duplicates, metadata-shortcut probe, Fig. 2 data |
| 4 | `python src/duplicate_and_leakage_audit.py` | Near-duplicate groups + audit of the augmented folder (~40 min, downloads 2,000 files) |
| 5 | `python src/data_preprocessing.py` | Cleaning log, 256 px cache, group-aware split, `dataset_statistics.csv` |
| 6 | `python src/train_models.py` | 5-fold CV + final fit: MobileNetV3-L, EfficientNet-B0, ResNet-50 (~3 h) |
| 7 | `python src/train_baseline.py` | 5-fold CV + final fit of the baseline CNN (~1.5 h) |
| 8 | `python src/report_week3_4.py` | Baseline results, model comparison, selection, figures |
| 9 | `python src/tune_model.py <model>` | Week 5 hyper-parameter study (saves fold models) |
| 10 | `python src/finalize_model.py` | Re-trains the selected configuration on all development data → `models/final_model.pt` |
| 11 | `python src/error_analysis.py --tag <tag>` | Error analysis + shortcut / degradation probes |
| 12 | `python src/leakage_experiment.py` | Replicates the leakage-prone protocol vs. a de-leaked control |
| 13 | `python src/explainability.py --tag <tag>` | Out-of-fold Grad-CAM / Grad-CAM++ / IG + checks |
| 14 | `python src/evaluate.py` | **Single** evaluation of all final models on the held-out test set |
| 15 | `python src/explainability.py --test --final models/final_model.pt` | Test-set explanations (after step 14) |
| 16 | `streamlit run prototype/app.py` | Research prototype (see `RUN_PROTOTYPE.md`) |
| 17 | `python src/report_week3_4.py`, `python src/make_reports.py baseline`, `python src/report_error_analysis.py`, `python src/report_xai.py`, `python src/report_final.py` | Regenerate the Markdown result documents from saved results |
| 18 | `python src/make_dataset_figure.py`, `make_leakage_figure.py`, `make_degradation_figure.py`, `make_test_figure.py`, `make_gradcam_paper_figure.py`, `make_workflow_figure.py`, `redraw_audit_figure.py` | Regenerate the paper figures |
| 19 | `python src/xai_sanity.py --tag tune_mobilenet_v3_large_E13` | Stratified randomisation sanity check for Grad-CAM |
| 20 | `python paper/build_week1.py`, `python paper/build_paper.py` | Build the Week 1 submission and the final paper (DOCX + PDF via Microsoft Word) |
| 21 | `python src/paper_qc.py` | Automated quality control of the paper PDF (citations, figures/tables, numbers, placeholders) |
| 22 | `python presentation/build_presentation.py` | Build the final presentation (.pptx) |

The `<tag>` used in steps 11–15 for the final configuration is `tune_mobilenet_v3_large_E13`.

## Data and licence
- **Data:** *Oral Images Dataset*, Chandrashekar et al., Mendeley Data V2, doi:10.17632/mhjyrn35p4.2, CC BY 4.0.
- **Code:** released for academic use with the project report.
- The dataset's `augmented_data` folder is **not** used for modelling (see `DATASET_CARD.md`).

## Responsible use
OralInsight outputs *model predictions* and *model-generated explanations* for research and teaching. It does not diagnose cancer, does not replace clinical examination or biopsy, and has not been validated on external or prospective data. Grad-CAM shows which pixels influenced the network. It does not show where a lesion is, and it is not proof of a clinically meaningful feature.
