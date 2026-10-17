# Final Deliverables — exact locations

All paths are relative to the project folder `OralInsight/`.

| # | Deliverable | Location |
|---|---|---|
| 1 | **Final research paper** | `paper/OralInsight_Final_Paper.docx`, `paper/OralInsight_Final_Paper.pdf` (11 pages, IEEE-style two-column). Source: `paper/paper_template_part{1,2,3}.md`, `paper/results_text.py`, `paper/build_paper.py`; generated Markdown `paper/OralInsight_Final_Paper.md`; figures `paper/figures/` |
| 1a | Week 1 submission (single PDF) | `paper/OralInsight_Week1_Submission.pdf` (+ `.docx`), built by `paper/build_week1.py` |
| 2 | **Final presentation** | `presentation/OralInsight_Final_Presentation.pptx` (13 slides with speaker notes), built by `presentation/build_presentation.py` |
| 3 | **Complete source code** | `src/` (31 scripts; run order in `README.md`), `prototype/app.py`, `paper/*.py`, `presentation/build_presentation.py`, `literature/build_literature_tables.py`, `notebooks/results_overview.ipynb` |
| 4 | **Trained models** | `models/final_model.pt` (final), `models/{baseline_cnn,mobilenet_v3_large,efficientnet_b0,resnet50}.pt`, CV fold models in `models/folds/`, model card `models/final_model_card.json` |
| 5 | **Dataset documentation** | `DATASET_CARD.md` (source, licence, classes, size, preprocessing, data dictionary, limitations, ethics), `dataset_statistics.csv`, `data/splits.csv`, `data/cleaning_log.csv`, `data/raw/manifest.csv` |
| 6 | **Literature review matrix** | `literature/literature_matrix.csv` (20 papers); Table I of the paper and of the Week 1 PDF |
| 7 | **Verified references** | `literature/references_verified.csv` (36 references, all `Verified = YES`); verification records in `literature/verification/`; paper text excerpts in `literature/papers/` |
| 8 | **Dataset comparison** | `docs/DATASET_COMPARISON.csv` (7 candidates) |
| 9 | **Research gap evidence** | `docs/RESEARCH_GAP_EVIDENCE.md` |
| 10 | **Experiment results** | `baseline_results.csv`, `BASELINE_RESULTS.md`, `EXPERIMENT_TABLE.csv`, `results/metrics/cv/*/` (fold metrics, histories, out-of-fold predictions), `results/tables/`, `docs/EXPERIMENT_LOG.md` |
| 11 | **Model comparison** | `MODEL_COMPARISON.csv`, `results/tables/model_selection.json`, `results/figures/fig_model_comparison.png`, `fig_cv_confusion_matrices.png`, `fig_cv_roc.png`, `fig_training_curves_all.png` |
| 12 | **Error analysis** | `ERROR_ANALYSIS.md`, `error_analysis.csv`, `results/tables/{error_profile,degradation_probe,degradation_probe_E00,performance_by_resolution,shortcut_pixel_probe}.csv`, `results/figures/fig_degradation_probe_compare.png`, `fig_error_examples.png` |
| 13 | **XAI / Grad-CAM results** | `XAI_ANALYSIS.md`; `results/xai/{correct,incorrect,false_positive,false_negative,class_benign,class_malignant}/` (out-of-fold, development set) and `results/xai/test/...`; `results/tables/xai_*.csv`; `results/figures/fig_gradcam_examples*.png` |
| 14 | **Working OralInsight prototype** | `prototype/app.py` (`streamlit run prototype/app.py`), CLI `src/inference.py`, instructions and test log `RUN_PROTOTYPE.md`, screenshot `results/figures/fig_prototype.png` |
| 15 | **README** | `README.md` |
| 16 | **Environment setup** | `ENVIRONMENT.md`, `requirements.txt` |
| 17 | **Project progress** | `PROJECT_PROGRESS.md` |
| 18 | **Final completion checklist** | `FINAL_COMPLETION_CHECKLIST.md` |
| — | Final audit | `FINAL_AUDIT.md` |
| — | Final evaluation | `FINAL_RESULTS.csv`, `FINAL_EVALUATION.md`, `results/tables/statistical_tests.csv`, `results/figures/fig_test_cm_roc.png` |
| — | Leakage audit and replication | `results/tables/leakage_audit*.csv`, `results/tables/leakage_experiment.csv`, `results/figures/fig_leakage_audit.png`, `fig_leakage_experiment.png` |
| — | Paper quality-control report | `results/tables/paper_qc.txt` (`src/paper_qc.py`) |

**Fields the team must complete:** student names and registration numbers, and department/university. These appear as `[NAME / REGISTRATION NUMBER]` and `[DEPARTMENT, UNIVERSITY]` in the paper, the Week 1 PDF, the presentation and the README. They were deliberately not invented.
