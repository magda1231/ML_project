# Experiment Plan

## Objective

Compare simple and complex base learners inside Learn++ to determine which delivers the best quality-to-cost trade-off for incremental classification on both a public benchmark and a medical imaging dataset.

> **Reframing note**: Earlier project versions described this as "weak vs strong classifier comparison." The comparison is now framed as **base learner selection** — testing classifiers of varying capacity as Learn++'s base learner component. "Simple" replaces "weak" and "complex" replaces "strong" where referring to model capacity.

---

## Selected Models

The project compares **one simple and one complex base learner** head-to-head within Learn++.

| Role | Model | Key Properties |
|------|-------|----------------|
| **Simple** | Decision Tree (depth 2) | Low capacity, fast, interpretable, canonical boosting base learner |
| **Complex** | Gradient Boosting (XGBoost/LightGBM) | High capacity, top tabular performer, tree-based (same family as simple) |

**Why this pair**: Both are tree-based, which isolates the effect of model capacity (single shallow tree vs boosted ensemble of trees) while keeping feature assumptions consistent. This makes the comparison clean and interpretable.

See [05_CLASSIFIER_CATALOG.md](05_CLASSIFIER_CATALOG.md) for the full candidate pool considered and selection rationale.

---

## Experiment Matrix

**Total experiments**: 2 (one pair tested on each data track).

| Experiment ID | Simple Model | Complex Model | Data Track |
|--------------|-------------|---------------|------------|
| EXP-01 | Decision Tree (depth 2) | Gradient Boosting | Public benchmark (Fashion-MNIST) |
| EXP-02 | Decision Tree (depth 2) | Gradient Boosting | Medical NIfTI (OASIS-3) |

Each experiment runs both models through the same Learn++ pipeline on the same batch sequence, then compares results using the CompositeScore and statistical significance testing.

**What we are NOT doing**:
- Not testing multiple pairs (project scope is 1 simple + 1 complex).
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
