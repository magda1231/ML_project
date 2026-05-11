# Classifier Pairing Strategy

## Purpose
Provide practical weak-versus-strong pairing options for structured comparison and final downselection.

## When to Use This Document
**Timeline**: Use this during **Phase 1 experiment planning**, after initial classifier longlist is created but before final experiments begin.

**Goal**: Define which weak-strong pairs to test together in Learn++ experiments to make fair, interpretable comparisons.

**Why pairing matters**:
- Prevents overwhelming experiment matrix (testing all 6 weak × 5 strong = 30 combinations is computationally expensive)
- Creates fair comparisons between models with similar assumptions
- Makes results easier to communicate to stakeholders
- Focuses compute budget on most informative experiments

## What This Document Is NOT
- This is NOT about selecting the final model (that happens after experiments complete)
- This is NOT about configuring Learn++ algorithm parameters
- This is NOT mandatory - you can test unpaired models if preferred

## How Pairing Helps
1. Creates fair and readable comparisons.
2. Exposes cost versus quality trade-offs quickly.
3. Makes final recommendation easier to justify.
4. Reduces total number of experiments while maintaining coverage.

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
6. **Best for**: Quick baseline experiments and projects with tight compute budgets.

### Pair Set B: Balanced Trade-Off
1. Weak: Decision Tree (depth 2)
2. Strong: Gradient Boosting family
3. Why this pair: shared tree-based intuition with clear quality ladder.
4. Expected outcome: easy interpretation of what additional complexity buys.
5. Main risk: boosting cost can escalate if complexity controls are loose.
6. **Best for**: Projects needing interpretable results and moderate quality gains.

### Pair Set C: Margin-Based Contrast
1. Weak: Linear SVM
2. Strong: RBF SVM
3. Why this pair: isolates the impact of linear versus nonlinear boundary.
4. Expected outcome: useful signal on whether nonlinearity is required.
5. Main risk: RBF candidate may violate compute budget on larger datasets.
6. **Best for**: Understanding feature space complexity and boundary requirements.

### Pair Set D: Representation Step-Up
1. Weak: Small MLP
2. Strong: Medium MLP
3. Why this pair: direct neural-capacity comparison with same model family.
4. Expected outcome: clean view of capacity gain under same paradigm.
5. Main risk: tuning sensitivity can blur fair comparison.
6. **Best for**: Projects with stable feature pipelines exploring neural capacity.

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

## Example Pairing Strategy for This Project

### Recommended Starting Configuration (Phase 1)
**Test 2-3 pairs maximum** to stay within compute budget:

1. **Primary pair**: Pair Set B (Decision Tree + Gradient Boosting)
   - Rationale: Best balance of interpretability and quality
   - Use on both public and medical tracks

2. **Efficiency backup**: Pair Set A (Naive Bayes + Random Forest)
   - Rationale: Fast baseline for sanity checks
   - Prioritize on public track first

3. **Optional third pair**: Pair Set C (Linear SVM + RBF SVM)
   - Only if budget allows
   - Use to understand feature space nonlinearity

### What NOT to Do
- ❌ Don't test all possible weak-strong combinations (too expensive)
- ❌ Don't pair models with conflicting feature assumptions (e.g., Naive Bayes + neural network)
- ❌ Don't change pairs mid-experiment (breaks reproducibility)

## Final Recommendation Format
After experiments complete, document:
1. Selected weak model and one backup.
2. Selected strong model and one backup.
3. One-paragraph rationale on quality plus cost trade-off.
4. Key risk note and mitigation plan.
