# Learn++ Planning Architecture (No Implementation)

## Confirmed Scope
1. This phase is documentation and planning only. No implementation code should be written.
2. The core comparison is weak vs strong classifiers used in a Learn++ workflow.
3. Model selection must combine predictive quality and compute cost.
4. Data strategy includes both tracks: public benchmark data and medical imaging data.
5. Initial technical priority for medical imaging is NIfTI and BIDS.

## Proposed High-Level Architecture
1. Data Intake Layer
   - Build a dataset index from public benchmark sources and medical sources.
   - Start with BIDS-organized studies and NIfTI volumes as primary medical inputs.
2. Data Standardization Layer
   - Convert all metadata to one schema: sample_id, source, split, class_label, modality, site, batch_id.
   - Store provenance fields needed for later audit and reproducibility.
3. Feature Preparation Layer
   - Define a baseline feature strategy for classical classifiers (radiomics/statistics or precomputed embeddings).
   - Keep this layer explicit in docs so users can swap feature strategies later.
4. Incremental Batch Layer
   - Organize data into ordered batches D1, D2, ..., Dk for incremental learning.
   - Ensure each batch has class-distribution statistics and drift notes.
5. Learn++ Experiment Layer
   - For each classifier candidate, run Learn++ across batches.
   - Store hypothesis-level metadata: weak learner type, epsilon_t, beta_t, voting weight, batch index.
6. Evaluation and Decision Layer
   - Evaluate per-batch and cumulative performance.
   - Compare candidates with a combined score that includes quality and compute cost.
7. Artifact Layer
   - Keep experiment logs, scorecards, confusion matrices, and recommendation notes.

## Learn++ Input and Output Definition
### Inputs
1. Dataset batches Dk with features and labels.
2. WeakLearn classifier specification per experiment.
3. Iteration budget Tk per batch.
4. Cost-tracking metadata: training time, inference time, memory footprint.

### Outputs
1. Hypotheses h_t and their voting weights.
2. Composite hypotheses H_t per batch and final H_final.
3. Performance metrics by batch and overall.
4. Cost metrics by batch and overall.
5. Ranked classifier recommendations with rationale.

## Scoring Rule for Strong vs Weak Comparison
Use a weighted score so classifier ranking reflects both quality and efficiency.
This is a ranking equation, not the classifier training equation.

CompositeScore = 0.50 * MacroF1 + 0.20 * BalancedAccuracy + 0.10 * (1 - TrainTimeNorm) + 0.10 * (1 - InferenceTimeNorm) + 0.10 * (1 - MemoryNorm)

Notes:
1. Normalize each cost metric to [0, 1] within a benchmark run.
2. Higher CompositeScore is better.
3. Weights can be tuned if compute budget becomes stricter.
4. Full Learn++ and per-classifier equations are centralized in CLASSIFIER_EQUATIONS.md.

## Candidate Classifier Groups
### Weak candidates
1. Decision Tree (depth 1-3)
2. Gaussian Naive Bayes
3. Logistic Regression (strong regularization)
4. Linear SVM or SGD hinge
5. LDA
6. Small MLP

### Strong candidates
1. Random Forest
2. RBF SVM
3. Gradient Boosting (XGBoost/LightGBM/CatBoost style)
4. Medium MLP
5. Feature-encoder plus linear head (for richer imaging features)

## Recommended First Evaluation Set
1. Weak shortlist: Decision Tree (depth 2), Gaussian Naive Bayes, Linear SVM.
2. Strong shortlist: RBF SVM, Random Forest, Gradient Boosting.
3. Run each shortlist on both tracks: one public benchmark and one medical NIfTI/BIDS dataset.

## Non-Implementation Deliverables from This Plan
1. Classifier shortlist and decision rubric.
2. Input and output schema notes for Learn++.
3. Incremental batch design template.
4. Cost-aware evaluation framework.

## Long-Term Phase Split (Confirmed)
1. Phase 1: Learn++ weak and strong classifier selection plus algorithm design documentation.
2. Phase 2: implementation of medical image recognition ML pipeline.
3. Phase 3A: non-medical photo and drone-image recognition.
4. Phase 3B: area mapping and geospatial output layer.
5. Phase 3C: swarm control integration, simulation-first.
6. Detailed phase gates and sequencing are tracked in PHASED_ROADMAP.md.
