# Experiment Plan

## Objective

Compare simple and complex base learners inside Learn++ to determine which delivers the best quality-to-cost trade-off for incremental classification on both a public benchmark and a medical imaging dataset.

> **Reframing note**: Earlier project versions described this as "weak vs strong classifier comparison." The comparison is now framed as **base learner selection** — testing classifiers of varying capacity as Learn++'s base learner component. "Simple" replaces "weak" and "complex" replaces "strong" where referring to model capacity.

---

## Selected Models

The project compares **one simple and one complex base learner** head-to-head within Learn++.

| Role | Model | Key Properties |
|------|-------|----------------|
| **From paper** | MLP (Multi-Layer Perceptron) | The base classifier from the original Learn++ paper (Polikar et al., 2001); neural network; higher capacity |
| **Recommended** | Decision Tree (depth-limited) | Used in Learn++.NSE (Elwell & Polikar, 2011); interpretable, fast; research shows strong results on MRI/medical data |

**Why this pair**: The assignment requires using one classifier from the original paper — that is MLP (used for OCR in Learn++, 2001). Decision Tree was adopted in later Learn++ variants and is well-suited to structured features from medical imaging (ROI-based). This tests whether Learn++'s incremental mechanism works better with a neural network (original design) or a tree-based model (domain-appropriate choice).

See [05_CLASSIFIER_CATALOG.md](05_CLASSIFIER_CATALOG.md) for the full candidate pool considered and selection rationale.

**Paper clarification**: The original Learn++ paper (Polikar et al., 2001) used MLP/Single-Layer Perceptron for OCR. Decision Trees appeared in later Learn++ variants (Learn++.NSE, Elwell & Polikar, 2011). Gradient Boosting was not used in any original Learn++ publication.

---

## State of the Art and Reference Baselines

### Learn++ Published Results

| Source | Dataset | Base Learner | Reported Accuracy | Notes |
|--------|---------|-------------|-------------------|-------|
| Polikar et al., 2001 (original Learn++) | Optical character recognition | MLP / Single-Layer Perceptron | ~85–92% | Incremental, multiple batches |
| Polikar et al., 2001 | VOC dataset | MLP | ~80–88% | Demonstrates batch stability |
| Learn++.NSE (Elwell & Polikar, 2011) | Various | Decision Tree | 75–95% range | Non-stationary variant, concept drift |

### State of the Art for Incremental/Continual Learning (2023–2026)

| Method | Approach | Typical Accuracy (MNIST-class tasks) | Key Advantage |
|--------|----------|---------------------------------------|---------------|
| Learn++ (ensemble) | Weighted voting of base learners | 85–95% | No old data replay needed |
| EWC (Kirkpatrick et al.) | Regularize important weights | 90–97% | Neural-only, principled |
| PackNet | Prune & freeze subnetworks | 92–97% | Zero forgetting |
| DER++ (Dark Experience Replay) | Replay + knowledge distillation | 93–98% | Strong on class-incremental |
| Gradient Boosting (static, non-incremental) | Full retrain | 95–99% | Upper bound reference |

### How to Use These Baselines

1. **Our MLP in Learn++** should aim to match or approach the original paper's results (~85–92% on similar complexity tasks).
2. **Our Decision Tree in Learn++** tests whether a tree-based model (recommended for MRI) outperforms the original neural network approach.
3. **Static MLP** (trained on all data at once) serves as an upper bound — incremental methods will typically be slightly below this.
4. **Report our results relative to these baselines** in the final presentation/report.

> These numbers are indicative ranges. Exact comparison requires matching dataset complexity. Our experiments will produce our own baselines on Fashion-MNIST and BraTS.

---

## Experiment Matrix

**Total experiments**: 2 (one pair tested on each data track).

| Experiment ID | From Paper | Recommended | Data Track |
|--------------|-----------|-------------|------------|
| EXP-01 | MLP (Polikar et al., 2001) | Decision Tree (Learn++.NSE, 2011) | Public benchmark (Fashion-MNIST) |
| EXP-02 | MLP (Polikar et al., 2001) | Decision Tree (Learn++.NSE, 2011) | Medical MRI (BraTS) |

Each experiment runs both models through the same Learn++ pipeline on the same batch sequence, then compares results using the CompositeScore and statistical significance testing.

**What we are NOT doing**:
- Not testing multiple pairs (project scope is MLP vs Decision Tree).
- Not testing models in isolation outside Learn++ (defeats the comparison purpose).
- Not mixing data tracks within a single experiment (breaks reproducibility).

---

## Incremental Protocol

1. Prepare ordered batches D₁, D₂, ..., Dₖ (minimum 3, recommended 4–5).
2. For each base learner candidate, run Learn++ across the same batch sequence.
3. Record per-batch and cumulative results.
4. Document where performance improves or degrades after each new batch.

### Batch Design Parameters
- Minimum batches: 3 (D₁, D₂, D₃)
- Recommended: 4–5 for stable trend analysis
- Each batch must have documented class distribution
- Batch design details in [04_DATA_STRATEGY.md](04_DATA_STRATEGY.md)

---

## Hyperparameter Strategy

Fair comparison requires a consistent hyperparameter approach across all candidates.

### Approach: Constrained Random Search

1. **Budget**: 20 random configurations per model per experiment.
2. **Validation**: 3-fold stratified cross-validation within each batch.
3. **Selection**: Choose configuration with best average MacroF1 on validation folds.
4. **Freezing**: Once selected on D₁, hyperparameters are fixed for D₂, D₃, etc. (no re-tuning per batch — this tests incremental robustness).

### Per-Model Search Spaces

| Model | Hyperparameters | Search Range |
|-------|----------------|--------------|
| Decision Tree | max_depth, criterion | {1, 2, 3}, {gini, entropy} |
| Gradient Boosting | n_estimators, max_depth, learning_rate | {50, 100, 200}, {3, 5, 7}, {0.01, 0.05, 0.1} |

### Why Random Search
Random search provides better coverage of the hyperparameter space than grid search with a fixed budget (Bergstra & Bengio, 2012). With only 2 models, cost is manageable either way, but random search remains the default for consistency.

---

## Metrics

### Primary Quality Metrics (used in CompositeScore)
1. **Macro F1** — weight: 0.40 in CompositeScore
2. **Balanced Accuracy** — weight: 0.15 in CompositeScore

### Primary Cost Metrics (used in CompositeScore)
3. **Training time** — weight: 0.15, normalized
4. **Inference time** — weight: 0.15, normalized
5. **Peak memory** — weight: 0.15, normalized

### Diagnostic Metrics (not in score, used for analysis)
6. Per-class Recall — detect minority-class collapse
7. Confusion matrices per batch
8. Hypothesis count and voting weight distribution
9. Per-batch ε_t and β_t statistics

### Statistical Significance

- Report mean ± standard deviation across cross-validation folds.
- Use Wilcoxon signed-rank test (non-parametric) to compare paired models when sample sizes are small.
- A result is considered significant at p < 0.05.
- Do not claim one model is "better" based solely on point estimates without significance testing.

---

## Ranking Rule

$$
\text{CompositeScore} = 0.40 \cdot \text{MacroF1} + 0.15 \cdot \text{BalancedAccuracy} + 0.15 \cdot (1 - \text{TrainTimeNorm}) + 0.15 \cdot (1 - \text{InferenceTimeNorm}) + 0.15 \cdot (1 - \text{MemoryNorm})
$$

**Important distinctions**:
- This equation ranks experiment results AFTER training completes.
- It does NOT describe how any classifier trains internally.
- For classifier training equations, see [06_LEARNPP_ALGORITHM.md](06_LEARNPP_ALGORITHM.md).
- Weights are adjustable — document changes in [09_DECISION_LOG.md](09_DECISION_LOG.md).

---

## Exit Criteria for Model Selection

A model passes selection if it meets ALL of the following:

1. **Stability**: Performance does not drop more than 10% in MacroF1 across consecutive batches.
2. **Budget compliance**: Compute cost within agreed budget (defined before experiments start).
3. **Minority class protection**: Minimum per-class recall ≥ 0.30.
4. **Incremental compatibility**: No catastrophic degradation when new batches are added.
5. **Statistical significance**: Performance advantage over the alternative is significant (p < 0.05) or within margin of practical equivalence (±0.02 MacroF1).

---

## Experiment Documentation Requirements

For each experiment (EXP-01 and EXP-02):
1. Fill scorecard in [07_REFINEMENT_PROCESS.md](07_REFINEMENT_PROCESS.md).
2. Add decision rationale to [09_DECISION_LOG.md](09_DECISION_LOG.md).
3. Store confusion matrices and per-batch metric tables.
4. Note any assumption violations or unexpected behavior.

---

## Phase 1 Deliverables

At end of Phase 1 experiments:
1. Completed scorecards for EXP-01 and EXP-02.
2. Head-to-head comparison summary: Decision Tree vs Gradient Boosting.
3. Recommendation on which base learner is more suitable (with CompositeScore and significance test).
4. Updated risk assessment in [08_RISKS_AND_ASSUMPTIONS.md](08_RISKS_AND_ASSUMPTIONS.md).
5. Completed handoff brief in [12_HANDOFF_TEMPLATE.md](12_HANDOFF_TEMPLATE.md).
