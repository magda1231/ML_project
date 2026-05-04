# Classifier Pairing Strategy

## Purpose
Provide practical weak-versus-strong pairing options for structured comparison and final downselection.

## How Pairing Helps
1. Creates fair and readable comparisons.
2. Exposes cost versus quality trade-offs quickly.
3. Makes final recommendation easier to justify.

## Pairing Principles
1. Pair models with similar feature assumptions when possible.
2. Include at least one efficiency-first pair and one quality-first pair.
3. Keep one pair that is easy to explain to non-technical reviewers.

## Recommended Pair Sets

### Pair Set A: Efficiency First
1. Weak: Gaussian Naive Bayes
2. Strong: Random Forest
3. Why this pair: clear contrast in capacity with moderate cost increase.
4. Expected outcome: reliable first benchmark with practical runtime.
5. Main risk: weak candidate may underfit complex classes too strongly.

### Pair Set B: Balanced Trade-Off
1. Weak: Decision Tree (depth 2)
2. Strong: Gradient Boosting family
3. Why this pair: shared tree-based intuition with clear quality ladder.
4. Expected outcome: easy interpretation of what additional complexity buys.
5. Main risk: boosting cost can escalate if complexity controls are loose.

### Pair Set C: Margin-Based Contrast
1. Weak: Linear SVM
2. Strong: RBF SVM
3. Why this pair: isolates the impact of linear versus nonlinear boundary.
4. Expected outcome: useful signal on whether nonlinearity is required.
5. Main risk: RBF candidate may violate compute budget on larger datasets.

### Pair Set D: Representation Step-Up
1. Weak: Small MLP
2. Strong: Medium MLP
3. Why this pair: direct neural-capacity comparison with similar model family.
4. Expected outcome: clean view of capacity gain under same paradigm.
5. Main risk: tuning sensitivity can blur fair comparison.

## Track-Specific Pair Priorities

### Public Benchmark Priority
1. Start with Pair Set A for baseline speed and quality anchor.
2. Add Pair Set B for stronger tabular performance check.
3. Use Pair Set C if boundary complexity appears significant.

### Medical NIfTI and BIDS Priority
1. Start with Pair Set B for robust structured baseline.
2. Add Pair Set A to preserve a low-cost reference.
3. Consider Pair Set D only if feature representation is stable enough.

## Decision Signals for Final Pair Choice
1. Quality signal: stable macro metrics and minority recall across batches.
2. Cost signal: no repeated budget breaches in training or inference.
3. Stability signal: no major drop after adding new incremental batches.
4. Communication signal: easy explanation of why strong model is selected.

## Final Recommendation Format
1. Selected weak model and one backup.
2. Selected strong model and one backup.
3. One-paragraph rationale on quality plus cost trade-off.
4. Key risk note and mitigation plan.
