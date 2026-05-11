# TODO

## Repo Hygiene (Do First)

- [ ] Commit current changes (v2 folder, updated README, deleted folders).
- [ ] Decide: remove `output_improved/` (v1) or keep as history.
- [ ] Decide: archive or remove `comments_from_M.txt` (feedback addressed in v2).
- [ ] Review `.gitignore` — ensure data files and experiment outputs won't be committed.

## Immediate (Phase 1 Execution)

- [ ] Download Fashion-MNIST and validate format and label distribution.
- [ ] Register for OASIS-3 access and complete data use agreement (1–3 day lead time).
- [ ] Confirm class labels and task type for both datasets.
- [ ] Define compute budget limits (what hardware is available?).
- [ ] Get M's feedback on v2 docs before proceeding.

## Learn++ Preparation

- [ ] Define batch sequence D₁, D₂, D₃ with class distribution notes.
- [ ] Finalize simple base learner shortlist (3 models).
- [ ] Finalize complex base learner shortlist (3 models).
- [ ] Agree on fixed evaluation metrics and CompositeScore weights.
- [ ] Define hyperparameter search spaces and tuning budget per model.

## Experiment Execution

- [ ] Run EXP-01: Decision Tree vs Gradient Boosting on Fashion-MNIST (public benchmark).
- [ ] Run EXP-02: Decision Tree vs Gradient Boosting on OASIS-3 MRI (medical track).
- [ ] Compare results: CompositeScore + Wilcoxon significance test.

## Documentation Completion

- [ ] Fill scorecard in 07_REFINEMENT_PROCESS.md for each completed experiment.
- [ ] Add decision entries to 09_DECISION_LOG.md after dataset selection.
- [ ] Validate that 02_ARCHITECTURE.md reflects final shortlist.

## Handoff Readiness

- [ ] Complete 12_HANDOFF_TEMPLATE.md with experiment results.
- [ ] Document unresolved risks and required Phase 2 implementation decisions.

## Phase Transition

- [ ] Confirm Phase 1 exit gate criteria are all satisfied.
- [ ] Freeze final base learner selections for implementation handoff.
- [ ] Get stakeholder sign-off on handoff brief.

## Phase 2 Preparation

- [ ] Define Phase 2 implementation owner and timeline.
- [ ] Lock medical dataset pipeline assumptions for NIfTI/BIDS.
- [ ] Define reproducibility checklist for training and evaluation runs.

## Phase 3 Preparation (Future)

- [ ] Define scope and success criteria for Phase 3A (photo/drone recognition).
- [ ] Define scope and success criteria for Phase 3B (area mapping).
- [ ] Define simulation-first constraints for Phase 3C (swarm integration).
