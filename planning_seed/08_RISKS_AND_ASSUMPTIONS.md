# Risks and Assumptions

---

## Part A: Assumptions

### Project Assumptions
1. This phase remains documentation-only and excludes implementation code.
2. Learn++ is the primary incremental learning framework (alternatives acknowledged in [02_ARCHITECTURE.md](02_ARCHITECTURE.md)).
3. Model comparison uses both predictive quality and compute cost.
4. Two data tracks are evaluated: public benchmark and medical imaging.
5. Medical track will start with NIfTI and BIDS formats.

### Data Assumptions
1. At least one public dataset and one medical dataset with usable labels will be available.
2. Both selected datasets can be split into incremental batches D₁ to Dₖ.
3. Metadata is sufficient to support reproducibility.
4. Label definitions can be harmonized for comparison logic.

### Evaluation Assumptions
1. Macro F1 and Balanced Accuracy are the primary quality metrics.
2. Training time, inference time, and memory are the primary cost metrics.
3. Final ranking uses a fixed weighted CompositeScore agreed before experiments start.
4. Hyperparameters are tuned once on D₁ and frozen for subsequent batches.

### Operational Assumptions
1. Implementation handoff will be performed after planning sign-off.
2. Team members will update scorecards and decision log during experimentation.
3. Compute budget limits will be defined before model training starts.

### Assumptions to Validate First
1. Dataset licensing and access timelines (especially OASIS-3 DUA).
2. Class label quality and minority class support.
3. Feasible compute budget for complex candidates (especially RBF SVM on medical data).

### If Any Assumption Fails
1. Record failure in [09_DECISION_LOG.md](09_DECISION_LOG.md).
2. Update architecture and experiment plan.
3. Re-baseline shortlist and scorecard criteria.

---

## Part B: Risk Register

| ID | Risk | Prob. | Impact | Early Signal | Mitigation |
|----|------|-------|--------|-------------|------------|
| R1 | Public and medical datasets not label-compatible for fair comparison | Medium | High | Label mapping becomes ambiguous | Define label harmonization sheet before experiments |
| R2 | Medical dataset access or license restrictions delay progress | Medium | High | Access requests remain pending | Backup dataset identified (ChestX-ray14, no registration needed) |
| R3 | Batch design introduces distribution shifts that hide true model behavior | Medium | High | Sudden unstable metrics between D₁ and D₂ | Document class distribution per batch and run drift checks |
| R4 | Compute cost too high for complex candidates | High | Medium | Training time exceeds budget repeatedly | Set strict compute caps and downselect early |
| R5 | Simple/complex role assignments become subjective | Medium | Medium | Repeated debate on model categories | Use fixed capacity/cost thresholds in decision rubric |
| R6 | Class imbalance causes misleading aggregate scores | High | High | High accuracy with poor minority recall | Prioritize Macro F1, Balanced Accuracy, per-class recall |
| R7 | Medical metadata quality inconsistent across sites | Medium | Medium | Missing scanner/session fields | Add required metadata fields to intake checklist |
| R8 | Documentation phase drifts into premature implementation | Medium | Medium | Code appears before sign-off | Enforce documentation-only gate |
| R9 | Final recommendation lacks reproducibility context | Medium | High | Results cannot be traced to dataset versions | Track data version, split seed, metric config in scorecards |
| R10 | Hyperparameter tuning unfair across candidates | Medium | High | Complex models benefit disproportionately from tuning budget | Fixed 20-config random search budget per model |

### Top 3 Priority Risks
1. **R6** — Class imbalance masking true performance.
2. **R4** — Compute budget exceeded by complex candidates.
3. **R2** — Medical dataset access delays.

### Immediate Actions
1. Lock one primary and one backup dataset per track.
2. Define compute budget ceiling before any model training.
3. Freeze evaluation metric policy and CompositeScore weights.
