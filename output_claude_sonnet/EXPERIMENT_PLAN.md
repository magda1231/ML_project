# Experiment Plan (No Code)

## Objective
Compare weak and strong classifiers inside Learn++ with a quality-plus-cost lens.

## Comparison Setup
1. Weak shortlist
   - Decision Tree (depth 1-3)
   - Gaussian Naive Bayes
   - Linear SVM or SGD hinge
2. Strong shortlist
   - Random Forest
   - RBF SVM
   - Gradient Boosting family

## Incremental Protocol
1. Prepare ordered batches D1, D2, ..., Dk.
2. For each classifier candidate, run the same batch order.
3. Record per-batch and cumulative results.
4. Document where performance improves or degrades after each new batch.

## Metrics to Track
1. Macro F1
2. Balanced Accuracy
3. Per-class Recall
4. Training time
5. Inference time
6. Peak memory estimate

## Ranking Rule
This is a selection ranking rule, not the internal learning equation of each classifier.
CompositeScore = 0.50 * MacroF1 + 0.20 * BalancedAccuracy + 0.10 * (1 - TrainTimeNorm) + 0.10 * (1 - InferenceTimeNorm) + 0.10 * (1 - MemoryNorm)

For Learn++ equations and per-classifier equations, see CLASSIFIER_EQUATIONS.md.

## Exit Criteria for Model Selection
1. Stable performance across at least two consecutive incremental batches.
2. Compute cost within agreed budget.
3. No severe collapse on minority classes.
