# Assumptions

## Purpose
Record working assumptions that the planning documents depend on.

## Project Assumptions
1. This phase remains documentation-only and excludes implementation code.
2. Learn++ is the target incremental-learning framework for comparison design.
3. Model comparison uses both predictive quality and compute cost.
4. The project will evaluate both data tracks:
   - public benchmark
   - medical imaging
5. Medical track will start with NIfTI and BIDS formats.

## Data Assumptions
1. At least one public dataset and one medical dataset with usable labels will be available.
2. Both selected datasets can be split into incremental batches D1 to Dk.
3. Metadata is sufficient to support reproducibility notes.
4. Label definitions can be harmonized for model comparison logic.

## Evaluation Assumptions
1. Macro F1 and Balanced Accuracy are required core metrics.
2. Training time, inference time, and memory are required cost metrics.
3. Final ranking will use a fixed weighted score agreed before implementation.

## Operational Assumptions
1. The implementation handoff will be done to the end user after planning sign-off.
2. Team members will update scorecards and decision log during experimentation.
3. Compute budget limits will be defined before model training starts.

## Assumptions to Validate First
1. Dataset licensing and access timelines.
2. Class label quality and minority class support.
3. Feasible compute budget for stronger candidates.

## If Any Assumption Fails
1. Record failure in decision log.
2. Update architecture and experiment plan.
3. Re-baseline shortlist and scorecard criteria.
