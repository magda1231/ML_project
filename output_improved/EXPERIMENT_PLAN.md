# Experiment Plan (No Code)

## Objective
Compare weak and strong classifiers inside Learn++ with a quality-plus-cost lens.

## Comparison Setup

### Full Candidate Pool (Longlist)
#### Weak candidates (6 total)
- Decision Tree (depth 1-3)
- Gaussian Naive Bayes
- Logistic Regression (strong regularization)
- Linear SVM or SGD hinge
- LDA
- Small MLP

#### Strong candidates (5 total)
- Random Forest
- RBF SVM
- Gradient Boosting family (XGBoost/LightGBM/CatBoost)
- Medium MLP
- Feature encoder plus linear head

### Recommended Shortlist for Phase 1 Experiments
Based on compute budget and interpretability requirements:

#### Weak shortlist (3 models)
1. **Decision Tree (depth 2)** - primary weak candidate
2. **Gaussian Naive Bayes** - ultra-fast baseline
3. **Linear SVM** - margin-based alternative

#### Strong shortlist (3 models)
1. **Gradient Boosting family** - primary strong candidate
2. **Random Forest** - robust ensemble baseline
3. **RBF SVM** - nonlinear boundary explorer

### Experiment Scope and Comparison Matrix

**Total experiments planned**: 6-8 runs (not all 18 combinations)

#### Recommended experiment configuration:
| Experiment ID | Weak Model | Strong Model | Data Track | Priority |
|--------------|------------|--------------|------------|----------|
| EXP-01 | Decision Tree | Gradient Boosting | Public benchmark | P0 (must run) |
| EXP-02 | Decision Tree | Gradient Boosting | Medical NIfTI | P0 (must run) |
| EXP-03 | Naive Bayes | Random Forest | Public benchmark | P1 (should run) |
| EXP-04 | Naive Bayes | Random Forest | Medical NIfTI | P1 (should run) |
| EXP-05 | Linear SVM | RBF SVM | Public benchmark | P2 (optional) |
| EXP-06 | Linear SVM | RBF SVM | Medical NIfTI | P2 (optional) |

**Rationale for this scope**:
- Each weak-strong pair is tested on BOTH tracks (public + medical)
- 2 pairs (4 experiments) are mandatory minimum
- 3rd pair is optional based on budget and early results
- This avoids testing all 3×3=9 possible weak-strong combinations

**What we are NOT doing**:
- ❌ Not testing every weak model with every strong model (too expensive)
- ❌ Not testing models in isolation (defeats purpose of weak vs strong comparison)
- ❌ Not mixing tracks within a single experiment (breaks reproducibility)

See [CLASSIFIER_PAIRING_STRATEGY.md](CLASSIFIER_PAIRING_STRATEGY.md) for detailed pairing rationale.

## Incremental Protocol
1. Prepare ordered batches D1, D2, ..., Dk.
2. For each classifier candidate, run the same batch order.
3. Record per-batch and cumulative results.
4. Document where performance improves or degrades after each new batch.

### Batch Design Parameters
- Minimum batches: 3 (D1, D2, D3)
- Recommended batches: 4-5 for stable trend analysis
- Each batch should have documented class distribution

## Metrics to Track

### Primary Quality Metrics
1. **Macro F1** (weight: 0.50 in CompositeScore)
2. **Balanced Accuracy** (weight: 0.20 in CompositeScore)
3. **Per-class Recall** (diagnostic, not in score)

### Primary Cost Metrics
4. **Training time** (weight: 0.10 in CompositeScore, normalized)
5. **Inference time** (weight: 0.10 in CompositeScore, normalized)
6. **Peak memory estimate** (weight: 0.10 in CompositeScore, normalized)

### Secondary Metrics (for analysis only)
- Confusion matrices per batch
- Hypothesis count and voting weight distribution
- Per-batch epsilon_t and beta_t statistics

## Ranking Rule
This is a **selection ranking rule**, not the internal learning equation of each classifier.

$$
\text{CompositeScore}=
0.50\cdot\text{MacroF1}+0.20\cdot\text{BalancedAccuracy}
+0.10\cdot(1-\text{TrainTimeNorm})
+0.10\cdot(1-\text{InferenceTimeNorm})
+0.10\cdot(1-\text{MemoryNorm})
$$

**Important distinction**:
- This equation ranks experiment results AFTER training completes
- It does NOT describe how any classifier trains internally
- For classifier training equations, see [CLASSIFIER_EQUATIONS_DETAILED.md](CLASSIFIER_EQUATIONS_DETAILED.md)
- For Learn++ ensemble equations, see [LEARNPP_EQUATION_FLOW_MAP.md](LEARNPP_EQUATION_FLOW_MAP.md)

### Normalization Details
Cost metrics are normalized to [0,1] within each experiment batch:
$$
\text{TrainTimeNorm} = \frac{\text{TrainTime}_{\text{model}} - \text{TrainTime}_{\text{min}}}{\text{TrainTime}_{\text{max}} - \text{TrainTime}_{\text{min}}}
$$

Same formula applies to InferenceTimeNorm and MemoryNorm.

## Exit Criteria for Model Selection
A model passes selection if it meets ALL of the following:
1. **Stability**: Stable performance across at least two consecutive incremental batches (no >10% MacroF1 drop).
2. **Budget compliance**: Compute cost within agreed budget (defined before experiments start).
3. **Minority class protection**: No severe collapse on minority classes (min per-class recall ≥ 0.30).
4. **Incremental learning compatibility**: Performance does not degrade catastrophically with new batches.

## Experiment Documentation Requirements
For each experiment (EXP-01 through EXP-06):
1. Fill one row in [SCORECARD_TEMPLATE.md](SCORECARD_TEMPLATE.md)
2. Add decision rationale to [DECISION_LOG.md](DECISION_LOG.md)
3. Store confusion matrices and per-batch metrics
4. Note any assumption violations or unexpected behavior

## Phase 1 Deliverables
At end of Phase 1 experiments:
1. Completed scorecards for all P0 and P1 experiments
2. Final weak classifier recommendation (+ 1 backup)
3. Final strong classifier recommendation (+ 1 backup)
4. Updated risk assessment
5. Handoff brief for Phase 2 implementation (see [IMPLEMENTATION_HANDOFF.md](IMPLEMENTATION_HANDOFF.md))
