# Classifier Refinement Guide

## Purpose
Define a rigorous, documentation-first process to refine classifier candidates before implementation.

## Refinement Flow
1. Eligibility Gate
2. Role Assignment Gate
3. Baseline Quality Gate
4. Incremental Stability Gate
5. Cost and Efficiency Gate
6. Operational Readiness Gate
7. Final Selection Gate

## Gate 1: Eligibility
1. Confirm candidate supports supervised classification for the chosen label setup.
2. Confirm candidate can be evaluated fairly on both public and medical tracks.
3. Confirm required metadata and splits are available.

## Gate 2: Role Assignment
1. Label candidate as **weak** if low complexity and low cost are core characteristics.
2. Label candidate as **strong** if higher capacity and higher expected quality are core characteristics.
3. Reject ambiguous role definitions unless rationale is clearly documented.

**Note**: "Weak" and "strong" refer to model capacity, not model quality. A weak classifier can still perform well in Learn++.

## Gate 3: Baseline Quality
1. Compare macro metrics against minimum acceptable baseline.
2. Inspect per-class behavior, not only aggregate score.
3. Mark any model that appears high-performing on majority class but weak on minority class.

## Gate 4: Incremental Stability
1. Check behavior across D1, D2, D3 and later batches.
2. Track trend quality, not only endpoint quality.
3. Flag candidates with sharp degradation after new data introduction.

## Gate 5: Cost and Efficiency
1. Evaluate training time against budget limits.
2. Evaluate inference time for practical use constraints.
3. Evaluate memory footprint for reproducible reruns.
4. Reject models that exceed budget without clear quality gain.

## Gate 6: Operational Readiness
1. Assess reproducibility burden.
2. Assess explainability level for team communication.
3. Assess failure transparency for debugging.

## Gate 7: Final Selection
1. Rank with agreed composite score.
2. Validate top candidates with risk review.
3. Select one primary and one backup model per role (one weak, one strong).

## Recommended Evidence Per Candidate
1. One-line role statement (weak or strong).
2. Batch trend summary.
3. Cost summary.
4. Failure mode summary.
5. Decision status: keep, watch, or drop.

## Keep, Watch, Drop Rules
| Status | Meaning | Typical Trigger |
|---|---|---|
| Keep | Candidate performs well enough to continue evaluation | Stable metrics plus acceptable cost |
| Watch | Candidate has potential but unresolved issue | Good metrics with instability or cost concerns |
| Drop | Candidate is unlikely to make final shortlist | Poor metrics or repeated budget violations |

**Terminology clarification**:
- "Performs well enough" means meeting quality thresholds for its role (weak or strong)
- This is NOT about whether the model is "strong classifier type" vs "weak classifier type"
- Both weak and strong classifier types can achieve "keep" status if they perform well in their category

## Tie-Break Rules
1. Prefer better minority-class recall if composite scores are close.
2. Prefer lower operational complexity if quality difference is small.
3. Prefer more stable incremental trend over a single high-scoring batch.

## Documentation Outputs from This Guide
1. Updated shortlist in experiment plan.
2. Decision entries in decision log.
3. Filled scorecard rows for each evaluated candidate.
4. Updated assumptions and risks based on observed behavior.
