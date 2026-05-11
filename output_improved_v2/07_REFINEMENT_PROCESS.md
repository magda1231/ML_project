# Refinement Process and Scorecard

This file combines the selection gate process with the experiment scorecard template.

---

## Part A: Refinement Gates

### Purpose
Define a rigorous, documentation-first process to refine base learner candidates before committing to implementation.

### Gate Flow

```
Gate 1: Eligibility → Gate 2: Role Assignment → Gate 3: Baseline Quality →
Gate 4: Incremental Stability → Gate 5: Cost & Efficiency →
Gate 6: Operational Readiness → Gate 7: Final Selection
```

### Gate 1: Eligibility
1. Confirm candidate supports supervised classification for the chosen label setup.
2. Confirm candidate can be evaluated fairly on both data tracks (public + medical).
3. Confirm required metadata and splits are available.

### Gate 2: Role Assignment
1. Label candidate as **simple** if low complexity and low cost are core characteristics.
2. Label candidate as **complex** if higher capacity and quality potential are core characteristics.
3. Reject ambiguous role assignments unless rationale is documented.

> "Simple" and "complex" refer to model capacity, not performance quality. A simple base learner can still perform well in Learn++ ensembles.

### Gate 3: Baseline Quality
1. Compare macro metrics against minimum acceptable baseline.
2. Inspect per-class behavior, not only aggregate score.
3. Flag models that appear high-performing on majority class but fail minority classes.

### Gate 4: Incremental Stability
1. Check behavior across D₁, D₂, D₃ and later batches.
2. Track trend quality, not only endpoint quality.
3. Flag candidates with sharp degradation after new data introduction.

### Gate 5: Cost and Efficiency
1. Evaluate training time against budget limits.
2. Evaluate inference time for practical deployment.
3. Evaluate memory footprint for reproducible reruns.
4. Reject models that exceed budget without clear quality gain.

### Gate 6: Operational Readiness
1. Assess reproducibility burden.
2. Assess explainability level for team communication.
3. Assess failure transparency for debugging.

### Gate 7: Final Selection
1. Compare Decision Tree vs Gradient Boosting using CompositeScore.
2. Validate with statistical significance test.
3. Document which base learner is more suitable and under what conditions.

---

## Keep / Watch / Drop Rules

| Status | Meaning | Typical Trigger |
|--------|---------|-----------------|
| **Keep** | Candidate meets quality thresholds for its role | Stable metrics + acceptable cost |
| **Watch** | Potential but unresolved concern | Good metrics with instability or cost worry |
| **Drop** | Unlikely to make final shortlist | Poor metrics or repeated budget violations |

> "Meets quality thresholds for its role" means performing well enough in its category (simple or complex). Both simple and complex base learners can achieve "Keep" status.

### Tie-Break Rules
1. Prefer better minority-class recall if CompositeScores are within 0.02.
2. Prefer lower operational complexity if quality difference is marginal.
3. Prefer more stable incremental trend over a single high-scoring batch.

---

## Required Evidence Per Candidate

1. Role statement (simple or complex).
2. Batch trend summary (performance across D₁, D₂, D₃).
3. Cost summary (training time, inference time, memory).
4. Failure mode summary.
5. Decision status: Keep, Watch, or Drop.

---

## Part B: Scorecard Template

### Experiment Header

| Field | Value |
|-------|-------|
| Experiment ID | EXP-XX |
| Dataset | |
| Track | public / medical |
| Data format | |
| Batch sequence | D₁, D₂, ... |
| Date | |
| Owner | |

### Per-Model Summary

| Model | Role | MacroF1 | BalancedAcc | TrainTime | InferenceTime | Memory | CompositeScore | Status |
|-------|------|---------|-------------|-----------|---------------|--------|----------------|--------|
| | | | | | | | | |

### Per-Batch Tracking

| Model | Batch | MacroF1 | BalancedAcc | Per-class Recall | TrainTime | InferenceTime | Memory | Drift Notes |
|-------|-------|---------|-------------|-----------------|-----------|---------------|--------|-------------|
| | D₁ | | | | | | | |
| | D₂ | | | | | | | |
| | D₃ | | | | | | | |

### CompositeScore Breakdown

| Component | Raw Value | Weight | Weighted |
|-----------|-----------|--------|----------|
| MacroF1 | | 0.40 | |
| BalancedAccuracy | | 0.15 | |
| 1 − TrainTimeNorm | | 0.15 | |
| 1 − InferenceTimeNorm | | 0.15 | |
| 1 − MemoryNorm | | 0.15 | |
| **Total** | | | |

### Statistical Comparison

| Comparison | Test | p-value | Significant? |
|------------|------|---------|--------------|
| Simple vs Complex (MacroF1) | Wilcoxon | | |
| Simple vs Complex (CompositeScore) | Wilcoxon | | |

### Selection Decision

1. Simple base learner: Decision Tree (depth 2)
2. Complex base learner: Gradient Boosting
3. Which performed better in Learn++:
4. Key quality-cost trade-off observed:
5. Recommendation for Phase 2:
