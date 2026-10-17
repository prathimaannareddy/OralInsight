# Final Completion Checklist

Legend: `[x]` done and checked · `[~]` done with a documented deviation · `[ ]` not done (reason given).

## Integrity rules
- [x] No fabricated papers, DOIs, datasets, sizes, class counts or metrics. All 36 references are verified via Crossref, DataCite, arXiv or the official IARC PDF (`literature/verification/`). All results come from saved runs.
- [x] Unverifiable information marked or removed. Examples: an unverified GLOBOCAN claim was dropped; P02's dataset size is marked "not stated in abstract".
- [x] `references_verified.csv` has all requested columns; only `Verified = YES` papers are used as evidence.
- [x] Original writing; no copied sentences. Direct quotes are short, marked, and limited to the gap evidence.
- [x] No exaggerated results. Non-significant differences, poor configurations and the test-vs-CV gap are reported.
- [x] Medical-safety language throughout ("research prototype", "model prediction", "requires clinical validation"). Wording-review report: `results/tables/paper_qc.txt`.
- [x] No deliberate AI-detection evasion. The text is grounded in this project's own data and results.

## Progress tracking
- [x] `PROJECT_PROGRESS.md` with task / evidence / files / verification / status for Weeks 1–8; all VERIFIED COMPLETE.
- [x] `FINAL_COMPLETION_CHECKLIST.md` (this file), `FINAL_AUDIT.md` (requested format), `FINAL_DELIVERABLES.md`.

## Week 1
- [x] Background: clinical motivation, early screening, computer vision, limits of manual screening, deep learning, explainability (Week 1 report; paper §I).
- [x] Searched >20 candidates (23 screened, Europe PMC); 2021–2026 preferred (18 of the 20 matrix papers).
- [x] 15–20 strong papers selected (20).
- [~] Every selected paper opened and verified: 15 full texts (PMC/author PDF) and 5 paywalled papers via the published abstract. The source is recorded per paper.
- [x] Extracted dataset, size, classes, model, preprocessing, metrics, result, limitation and gap for each paper.
- [x] 3–5 traceable research gaps (4), with gap → papers → evidence → response (`docs/RESEARCH_GAP_EVIDENCE.md`).
- [x] Candidate datasets studied (7) with source, size, classes, licence, access, limitations and suitability (`docs/DATASET_COMPARISON.csv`).
- [x] One primary dataset selected with scientific justification.
- [x] Preliminary methodology: preprocessing, augmentation, 3 models, task, XAI, metrics.
- [x] Deliverables: problem statement (191 words), 4 objectives, matrix, gaps, dataset, methodology, workflow diagram, references. Single PDF.
- [~] NotebookLM was not used; extraction was done directly from saved paper text (equivalent purpose, reproducible).

## Week 2
- [x] Dataset downloaded (scripted, hash-verified).
- [x] Directory structure `data/raw|cleaned|train|validation|test`.
- [x] Totals, per-class counts, percentages, duplicates, corrupt files, missing labels, dimensions, formats.
- [x] Cleaning rules documented; nothing deleted silently (no deletions were needed).
- [x] Class imbalance analysed (balanced).
- [x] Metadata recorded (EXIF, camera, resolution); acquisition bias quantified.
- [x] Ethics and licensing documented.
- [x] Reproducible preprocessing script.
- [x] `dataset_statistics.csv` with Class / Number / % / Dimensions / Train / Validation / Test counts.
- [x] `DATASET_CARD.md` (source, licence, classes, size, preprocessing, limitations, ethics, data dictionary).

## Week 3
- [x] Resizing, normalisation, augmentation, train/validation/test split.
- [x] No leakage: group-aware split, test never used before Week 7, train-only augmentation, the repository's augmented folder excluded (all asserted in code).
- [x] Baseline CNN.
- [x] Training/validation curves, confusion matrix, accuracy, precision, recall, F1, ROC-AUC, class-wise metrics.
- [x] `BASELINE_RESULTS.md`, `baseline_results.csv`.
- [x] Seed, batch size, learning rate, optimiser, epochs, image size, augmentation, loss and environment recorded.

## Week 4
- [x] 3 candidate models with justification (capacity/compute range, use in the literature).
- [x] Per model: architecture, weights, trainable layers, learning rate, batch size, epochs, optimiser, loss, augmentation, training time, parameter count, performance.
- [x] Compared by accuracy, precision, recall, F1, ROC-AUC, confusion matrices and class-wise metrics.
- [x] `MODEL_COMPARISON.csv`.
- [x] Accuracy / F1 / precision / recall / AUC comparison figure (one multi-panel figure) and confusion-matrix figures.
- [x] Best model selected by a justified, balanced criterion, not accuracy alone.

## Week 5
- [x] Investigated learning rate, batch size, augmentation, dropout, class weighting, optimiser, scheduler length and weight decay.
- [~] Early stopping: investigated in Week 3 and rejected (documented); a fixed schedule was used instead.
- [~] Focal loss: not run. The classes are balanced, so it was not justified (documented).
- [x] Documented experiment table (ID, change, LR, batch size, augmentation, validation F1, validation accuracy, notes).
- [x] Error analysis: false positives, false negatives, difficult/confusing cases, dataset limitations.
- [x] `ERROR_ANALYSIS.md`, `error_analysis.csv`.
- [x] Poor results reported honestly.

## Week 6
- [x] Grad-CAM, plus Grad-CAM++ and Integrated Gradients.
- [x] Explanations for correct, incorrect, FP, FN and per-class examples, saved under `results/xai/` in the requested structure.
- [x] Analysis of whether highlighted regions are plausible, with no clinical claims.
- [x] `XAI_ANALYSIS.md` (method, implementation, examples, observations, correct/incorrect analysis, limitations).

## Week 7
- [x] Final evaluation only on the held-out test set (run once).
- [x] Accuracy, precision, recall, F1, ROC-AUC, confusion matrix, class-wise metrics, confidence.
- [x] Bootstrap CIs, McNemar tests, paired bootstrap ΔAUC.
- [x] Comparison with baseline and candidates.
- [x] `FINAL_RESULTS.csv`, `FINAL_EVALUATION.md`.
- [x] Prototype accepts an image, preprocesses it, predicts, shows class, probability and Grad-CAM, and states that it is a research prototype.
- [x] Streamlit; runs; tested with several images (65 programmatically, 7 via the browser UI).
- [x] `RUN_PROTOTYPE.md` with exact commands.

## Week 8 / paper
- [x] Paper sections: introduction / problem statement, objectives, literature review, research gap, dataset, methodology, experimental setup, models, results, error analysis, XAI, prototype, discussion, limitations, conclusion, future work, references.
- [x] Week 1 structure preserved inside the paper (problem statement, objectives, Table I matrix, gaps, dataset, workflow Fig. 1, references).
- [x] Preferred structure: title, authors, affiliation field, abstract, keywords, numbered sections and subsections, appendix.
- [x] Figures 1–7 as specified (+ Figs. 8–9) and Tables 1–7 as specified (+ Table VIII); all numbered, captioned and referenced (checked by `src/paper_qc.py`).
- [x] IEEE-style two-column layout, IEEE citations and reference list. Readable 10 pt text; length set by content (11 pages including appendix).
- [x] DOCX and PDF.

## Code / reproducibility
- [x] Python; structure as requested (adapted: `docs/`, `paper/*.py`, `results/logs/`, extra scripts).
- [x] `requirements.txt`, `ENVIRONMENT.md` (Python, packages, framework, seeds, hardware, CPU-only note, dataset version, model files).
- [~] Bit-exact reproduction is not guaranteed across machines and thread counts (~0.01 variation, measured and documented).

## Result verification
- [x] Confusion matrices sum correctly (asserted).
- [x] Class counts match dataset counts.
- [x] Accuracy, precision, recall and F1 are consistent with the counts (recomputed).
- [x] Reported values match saved outputs; figures and tables are generated from the CSVs; paper numbers are generated from files (QC checks key values).

## Research gap / dataset / model selection sections
- [x] `RESEARCH_GAP_EVIDENCE.md`; gaps supported only by evidence; unsupported gaps explicitly not claimed.
- [x] `DATASET_COMPARISON.csv` with all requested columns plus `Selected`.
- [x] Model choice justified by data size and compute.

## Final paper quality-control audit
- [x] Every citation corresponds to a real, verified paper.
- [x] No fabricated references or DOIs (every DOI in the reference list is in the verified list).
- [x] All results come from actual experiments.
- [x] Dataset numbers and class counts correct.
- [x] Tables match CSV files; figures match experiments.
- [x] Model names correct.
- [x] XAI results actually generated (702 explanation panels: 565 out-of-fold + 137 test).
- [x] Research gaps have evidence.
- [x] No plagiarism or copying from papers.
- [x] No unsupported medical claims (14 flagged sentences reviewed).
- [x] No contradictory numbers. Wrong figure/table references found by QC were fixed.
- [~] No placeholder text remains, except the intentional team-fill fields `[NAME / REGISTRATION NUMBER]` and `[DEPARTMENT, UNIVERSITY]`, which cannot be filled without the team's details.
- [x] No "[INSERT HERE]" text.
- [x] No fake author names: student names were deliberately not invented.
- [x] No fake results.
- [x] Paper references all figures and tables.
- [x] README run order documented; every listed script has been executed in this project.
- [x] Prototype runs (verified in the browser).
- [x] Code organised.
- [x] `requirements.txt` present.
- [x] Final PDF opens (11 pages, inspected).

## Final deliverables
- [x] Paper (DOCX, PDF) · [x] Presentation (PPTX) · [x] Source code · [x] Trained models · [x] Dataset documentation · [x] Literature matrix · [x] Verified references · [x] Dataset comparison · [x] Research gap evidence · [x] Experiment results · [x] Model comparison · [x] Error analysis · [x] XAI results · [x] Working prototype · [x] README · [x] Environment setup · [x] Project progress · [x] Final checklist

## Not achievable within the project (documented, not fabricated)
- [ ] External validation: no openly licensed comparable dataset; the larger datasets (Piyarathne et al.; Ayman et al.) are restricted-access.
- [ ] Clinical validation / specialist review of the XAI observations: requires clinicians and ethics approval.
