# Final Audit — OralInsight (against the original 8-week plan)

Audit date: 2026-10-09. Each status below was assigned only after the listed evidence had been checked; the checks are described in `PROJECT_PROGRESS.md`.

WEEK 1
Requirement: Problem definition and literature review. D1 problem statement (150–200 words) + objectives; D2 15–20 relevant papers reviewed; D3 dataset and methodology finalised. Submit one PDF with problem statement, 3–4 objectives, literature matrix, 3–5 evidence-based gaps, dataset with source/justification, methodology with workflow diagram, references.
Evidence: Problem statement of 191 words. 4 objectives. 20-paper matrix and 36 references, all verified through Crossref/DataCite/arXiv or the official IARC PDF. 4 gaps, each mapped to papers. 7 datasets compared and 1 selected with justification. Methodology table and workflow figure. Single 6-page PDF. Deviation: the Week 1 instructions mention NotebookLM; evidence was instead extracted directly from saved full texts (15 papers) or abstracts (5 paywalled papers, marked in the matrix's `Extraction source` column).
File: paper/OralInsight_Week1_Submission.pdf; docs/WEEK1_REPORT.md; literature/literature_matrix.csv; literature/references_verified.csv; docs/RESEARCH_GAP_EVIDENCE.md; docs/DATASET_COMPARISON.csv; results/figures/fig_workflow.png
Status: VERIFIED COMPLETE

WEEK 2
Requirement: Dataset acquisition and preparation. Organise classes; remove duplicates/corrupt images; analyse class distribution and imbalance; document metadata and ethics. D1 clean dataset, D2 dataset statistics, D3 data dictionary and preprocessing protocol.
Evidence: 323/323 files downloaded with SHA-256 verified. Checks for corrupt files, exact duplicates and near-duplicates (perceptual hash + embeddings, threshold sweep and visual review): 0 removed, 14 duplicate groups kept within single splits. Class balance 51.1% / 48.9%. Acquisition bias quantified (metadata AUC 0.776). EXIF/privacy check. Leakage audit of the augmented folder. Group-aware split 206/52/65. All cleaning rules logged.
File: DATASET_CARD.md; dataset_statistics.csv; data/splits.csv; data/cleaning_log.csv; data/cleaned/; data/{train,validation,test}/; src/download_dataset.py; src/dataset_analysis.py; src/duplicate_and_leakage_audit.py; src/data_preprocessing.py
Status: VERIFIED COMPLETE

WEEK 3
Requirement: Image preprocessing and baseline model. Resizing, normalisation, augmentation; train/validation/test split; evaluation scripts; baseline CNN. D1 reproducible pipeline, D2 baseline model, D3 accuracy, precision, recall, F1, AUC.
Evidence: Pipeline in src/common.py (256 px cache, train-only augmentation, ImageNet normalisation). Baseline CNN evaluated with 5-fold group CV: accuracy 0.620 ± 0.097, macro-F1 0.617 ± 0.096, AUC 0.661 ± 0.107. Class-wise metrics, confusion matrices and curves. Seed, hyper-parameters and environment recorded. The protocol revision (abandoning early stopping on validation loss) is documented and the v1 runs archived.
File: BASELINE_RESULTS.md; baseline_results.csv; models/baseline_cnn.pt; results/figures/fig_baseline_curves.png; results/metrics/cv/baseline_cnn/; docs/EXPERIMENT_LOG.md
Status: VERIFIED COMPLETE

WEEK 4
Requirement: Model development. Train 2–3 candidate models (e.g. ResNet/EfficientNet/MobileNet); compare models, confusion matrices and class-wise performance. D1 trained candidates, D2 comparison table, D3 best model selected by a justified criterion.
Evidence: MobileNetV3-Large, EfficientNet-B0 and ResNet-50 trained under an identical protocol. CV macro-F1 0.829 / 0.809 / 0.836; AUC 0.894 / 0.901 / 0.903. Parameters, GFLOPs, training time and settings recorded. Pre-registered rule applied in code: all three within 1 SD on macro-F1 and AUC, so the lowest-compute model (MobileNetV3-Large) was selected.
File: MODEL_COMPARISON.csv; results/tables/model_selection.json; models/{mobilenet_v3_large,efficientnet_b0,resnet50}.pt; results/figures/{fig_model_comparison,fig_cv_confusion_matrices,fig_cv_roc,fig_training_curves_all}.png
Status: VERIFIED COMPLETE

WEEK 5
Requirement: Model improvement and robustness. Tune learning rate, batch size, augmentation and class balancing; error analysis of false positives/negatives and difficult classes. D1 optimised model, D2 hyper-parameter record, D3 error-analysis report.
Evidence: 14-configuration CV study covering learning rate, batch size, augmentation, dropout, weight decay, label smoothing, class weighting, optimiser, schedule, a combination run and resolution-degradation augmentation. Focal loss omitted with justification (balanced classes). Run-to-run noise measured. Error analysis found a resolution shortcut (46% benign flips under down-sampling), mitigated by E13 (2%) and selected under a rule amended before the remaining results were inspected. Leakage replication: 1.000 → 0.788 accuracy (size control 0.981). Poor configurations reported as such.
File: EXPERIMENT_TABLE.csv; results/tables/tuning_experiments.csv; results/tables/tuning_selection.json; models/final_model.pt; ERROR_ANALYSIS.md; error_analysis.csv; results/tables/leakage_experiment.csv; docs/EXPERIMENT_LOG.md
Status: VERIFIED COMPLETE

WEEK 6
Requirement: Explainable AI. Integrate Grad-CAM/other XAI with the selected model; analyse explanations for correct and incorrect predictions. D1 working XAI pipeline, D2 explanation images, D3 XAI analysis of model decisions.
Evidence: Grad-CAM (primary), Grad-CAM++ and Integrated Gradients. All 258 development images explained out of fold; 65 test images explained after the evaluation. Images filed as correct (209), incorrect (49), FP (26), FN (23), benign (132), malignant (126), plus test folders. Checks: border ratio 0.297; method agreement 0.859 / 0.388; deletion 0.264 vs 0.105; stratified randomisation sanity ρ = 0.092. Per-outcome observations without clinical claims.
File: XAI_ANALYSIS.md; src/explainability.py; src/xai_sanity.py; results/xai/; results/tables/xai_*.csv; results/figures/fig_gradcam_examples_paper.png
Status: VERIFIED COMPLETE

WEEK 7
Requirement: Validation and prototype. Final test-set evaluation with statistical/comparative analysis; simple screening interface integrating model and explanation. D1 final evaluation results, D2 working OralInsight prototype, D3 detection + explanation demonstration.
Evidence: Single held-out evaluation of 5 models. Final model: accuracy 0.892 [0.815, 0.954], sensitivity 0.875, specificity 0.909, F1 0.889, AUC 0.941 [0.874, 0.989]. vs baseline: McNemar p = 0.0007; vs other pretrained models: not significant. Streamlit prototype tested: pipeline identical to the evaluation on all 65 test images (max |ΔP| 8.4e-7); 7 uploads through the browser, including errors and a low-confidence case; disclaimers shown.
File: FINAL_RESULTS.csv; FINAL_EVALUATION.md; results/tables/statistical_tests.csv; prototype/app.py; src/inference.py; RUN_PROTOTYPE.md; results/tables/prototype_consistency_test.csv; results/figures/fig_prototype.png
Status: VERIFIED COMPLETE

WEEK 8
Requirement: Research paper and final presentation. Methodology, experiments, results, related work, XAI analysis, discussion and conclusion. D1 complete research paper draft, D2 final source code/documentation, D3 presentation + project demonstration.
Evidence: 11-page IEEE-style paper (DOCX + PDF) with all requested sections and the Week 1 components integrated; Figures 1–9 and Tables I–VIII; every result generated from saved files. Automated QC passed (citations verified, figures/tables referenced, numbers match CSVs, no unintended placeholders). 13-slide presentation (validator passed, visually inspected). README with full run order, environment, deliverables list and results notebook; demonstration steps in RUN_PROTOTYPE.md.
File: paper/OralInsight_Final_Paper.pdf; paper/OralInsight_Final_Paper.docx; presentation/OralInsight_Final_Presentation.pptx; results/tables/paper_qc.txt; README.md; FINAL_DELIVERABLES.md
Status: VERIFIED COMPLETE

## Items that remain the team's responsibility
- Fill in student names, registration numbers and department/university (marked fields in the paper, Week 1 PDF, slides and README).
- Before submission, have the supervisor review the qualitative XAI observations; they were judged by non-clinicians.

## Known scientific limitations (stated in the paper, not hidden)
- No external validation was possible: no openly licensed comparable dataset; the larger datasets are restricted-access.
- The acquisition confound (resolution/camera) is reduced but not removed.
- The test set is small (65 images), giving wide CIs.
- Floating-point results are not bit-exact across machines and thread counts (~0.01 run-to-run variation, documented).
