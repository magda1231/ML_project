# System Architecture

## Scope

This phase is documentation and planning only. No implementation code is written.

The core task is **base learner selection**: determine which classifiers, when used as base learners in an incremental ensemble framework, deliver the best quality-to-cost trade-off across both a public benchmark and a medical imaging dataset.

---

## Incremental Learning Framework Selection

### Primary Candidate: Learn++

Learn++ (Polikar et al., 2001) is an ensemble-based incremental learning algorithm that accumulates weak hypotheses across sequential data batches without retraining on previous data. It extends AdaBoost to the incremental setting.

**Why Learn++**:
- Designed explicitly for incremental learning with non-stationary data.
- Well-studied theoretical properties (error bounds via boosting theory).
- Framework-agnostic base learner — any supervised classifier can be plugged in.
- No requirement to store or replay old data batches.

### Alternative Frameworks Worth Acknowledging

| Framework | Key Idea | Pros | Cons | Why Not Primary |
|-----------|----------|------|------|-----------------|
| **EWC** (Elastic Weight Consolidation) | Regularizes neural weights to avoid catastrophic forgetting | Strong for neural nets, principled Bayesian motivation | Only applicable to neural networks, not classical ML | Limits base learner choice to neural models |
| **PackNet** | Prunes and freezes network subsets per task | Efficient parameter use | Neural-only, complex implementation | Too narrow for our classifier comparison |
| **Progressive Neural Networks** | Adds new columns per task | No forgetting by design | Grows linearly, neural-only | Scalability concern, neural-only |
| **Learn++.NSE** | Learn++ variant for non-stationary environments | Handles concept drift explicitly | More complex than base Learn++ | Good Phase 2 upgrade candidate if drift is significant |
| **ELLA** (Efficient Lifelong Learning) | Shared latent basis across tasks | Efficient multi-task transfer | Assumes linear task relationship | Less suitable for diverse medical imaging tasks |

**Recommendation**: Start with Learn++ for Phase 1. It is the most flexible for comparing diverse base learners (from Decision Trees to SVMs to MLPs). If Phase 2 experiments reveal significant concept drift, consider upgrading to Learn++.NSE.

---

## High-Level Architecture

### Processing Layers

1. **Data Intake Layer**
   - Build dataset index from public benchmark and medical sources.
   - Medical inputs: BIDS-organized studies with NIfTI volumes.

2. **Data Standardization Layer**
   - Unified schema: sample_id, source, split, class_label, modality, site, batch_id.
   - Store provenance fields for audit and reproducibility.

3. **Feature Preparation Layer**
   - Define baseline feature strategy for classical classifiers (radiomics/statistics or precomputed embeddings).
   - Keep this layer swappable so feature strategies can be changed without affecting downstream logic.

4. **Incremental Batch Layer**
   - Organize data into ordered batches D₁, D₂, ..., Dₖ.
   - Each batch includes class-distribution statistics and drift notes.

5. **Experiment Layer**
   - For each base learner candidate, run the incremental framework across batches.
   - Store hypothesis-level metadata: base learner type, ε_t, β_t, voting weight, batch index.

6. **Evaluation and Decision Layer**
   - Evaluate per-batch and cumulative performance.
   - Compare candidates using the CompositeScore formula.

7. **Artifact Layer**
   - Experiment logs, scorecards, confusion matrices, recommendation notes.

---

## Inputs and Outputs

### Inputs
1. Dataset batches Dₖ with features and labels.
2. Base learner specification per experiment.
3. Iteration budget Tₖ per batch.
4. Cost-tracking metadata: training time, inference time, memory footprint.

### Outputs
1. Hypotheses h_t and their voting weights.
2. Composite hypotheses H_t per batch and final H_final.
3. Performance metrics by batch and overall.
4. Cost metrics by batch and overall.
5. Ranked base learner recommendations with rationale.

---

## Scoring Rule: CompositeScore

Used to rank base learner candidates after experiments complete. This is a **selection ranking equation**, not a training objective.

$$
\text{CompositeScore} = w_1 \cdot \text{MacroF1} + w_2 \cdot \text{BalancedAccuracy} + w_3 \cdot (1 - \text{TrainTimeNorm}) + w_4 \cdot (1 - \text{InferenceTimeNorm}) + w_5 \cdot (1 - \text{MemoryNorm})
$$

### Default Weights

| Component | Weight | Rationale |
|-----------|--------|-----------|
| MacroF1 | 0.40 | Primary quality metric, class-balanced |
| BalancedAccuracy | 0.15 | Secondary quality check, less correlated with F1 when precision varies |
| 1 − TrainTimeNorm | 0.15 | Training efficiency matters for iterative experiments |
| 1 − InferenceTimeNorm | 0.15 | Deployment speed matters for medical applications |
| 1 − MemoryNorm | 0.15 | Reproducibility on constrained hardware |

> **Changes from v1**: Reduced MacroF1 weight from 0.50 to 0.40 and BalancedAccuracy from 0.20 to 0.15. These metrics are correlated (both reward balanced class performance), so the previous 70% combined weight over-counted classification quality. Cost metrics are now weighted more equally to better reflect practical deployment constraints.

### Normalization

Cost metrics are min-max normalized within each experiment batch:

$$
\text{TrainTimeNorm} = \frac{\text{TrainTime}_{\text{model}} - \text{TrainTime}_{\text{min}}}{\text{TrainTime}_{\text{max}} - \text{TrainTime}_{\text{min}}}
$$

Same formula applies to InferenceTimeNorm and MemoryNorm.

**Robustness note**: If an outlier model distorts the min-max range (e.g., one model is 100x slower), consider removing that model from normalization bounds or using percentile-based normalization instead.

### Weight Sensitivity

Weights are tunable. If the team determines that compute cost is more critical (e.g., tight deployment budget), shift weight from quality to cost metrics. Document any weight changes in [09_DECISION_LOG.md](09_DECISION_LOG.md).

---

## Base Learner Candidate Groups

### Simple (low-capacity) candidates
1. Decision Tree (depth 1–3)
2. Gaussian Naive Bayes
3. Logistic Regression (strong regularization)
4. Linear SVM / SGD hinge
5. LDA
6. Small MLP (1–2 hidden layers, < 100 units)

### Complex (high-capacity) candidates
1. Random Forest
2. RBF SVM
3. Gradient Boosting (XGBoost / LightGBM / CatBoost)
4. Medium MLP (2–3 hidden layers, 100–500 units)
5. Feature-encoder + linear head

### Selected Pair for Phase 1

The project compares **one simple and one complex base learner** head-to-head within Learn++.

| Role | Selected Model | Rationale |
|------|---------------|----------|
| Simple | **Decision Tree (depth 2)** | Canonical boosting base learner, high diversity under reweighting, fast, interpretable |
| Complex | **Gradient Boosting (XGBoost/LightGBM)** | Top performer on tabular/engineered features, shared tree-based intuition enables clean capacity comparison |

**Why this pair**: Both are tree-based, which isolates the effect of model capacity (single shallow tree vs boosted ensemble) while keeping feature assumptions consistent. This makes the comparison interpretable and fair.

See [05_CLASSIFIER_CATALOG.md](05_CLASSIFIER_CATALOG.md) for the full candidate pool and selection rationale.

---

## Long-Term Phase Split

Detailed in [01_ROADMAP.md](01_ROADMAP.md):
1. Phase 1: Base learner selection + algorithm design documentation (current).
2. Phase 2: Medical image recognition implementation.
3. Phase 3: Vision domain expansion (photo/drone → mapping → swarm integration).
