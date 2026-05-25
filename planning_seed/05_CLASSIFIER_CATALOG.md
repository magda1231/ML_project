# Classifier Catalog

This file documents all classifier candidates considered and the rationale for the final selection. The project compares **one simple and one complex base learner** head-to-head.

> **Terminology**: "Simple" = low-capacity base learner. "Complex" = high-capacity base learner. These replace the earlier "weak"/"strong" labels which conflated model capacity with model performance. See [11_GLOSSARY.md](11_GLOSSARY.md).

---

## Selected Pair

| Role | Model | Why Selected |
|------|-------|--------------|
| **From paper** | MLP (Multi-Layer Perceptron) | The base classifier from the original Learn++ paper (Polikar et al., 2001); used for OCR; satisfies requirement to use one algorithm from the paper |
| **Recommended** | Decision Tree (depth-limited) | Used in later Learn++ variants (Learn++.NSE, 2011); strong performance on medical/structured data; interpretable; well-suited to ROI-based features |

**Why this pair**: The assignment requires one classifier from the original paper (MLP). Decision Tree is the recommended choice for MRI data based on research and its proven use in later Learn++ publications. This tests two fundamentally different learning paradigms (neural network vs tree-based) within the same incremental framework.

**Clarification on Learn++ history**:
- **Original Learn++ (Polikar et al., 2001)**: Used MLP / Single-Layer Perceptron as base classifier, applied to OCR.
- **Later Learn++ variants (Learn++.NSE, Elwell & Polikar, 2011)**: Adopted Decision Trees as base learners.
- **Gradient Boosting**: Never used in original Learn++ papers. It is itself an ensemble of decision trees (boosted), which would create a nested-ensemble architecture if placed inside Learn++.

---

## Full Candidate Pool (Considered)

The following candidates were evaluated before selecting the final pair.

| Candidate | Role | Quality Potential | Compute Cost | Incremental Stability |
|-----------|------|-------------------|-------------|----------------------|
| Decision Tree (depth 1–3) | Simple | Medium | Low | Medium |
| Gaussian Naive Bayes | Simple | Low–Medium | Very low | Medium |
| Logistic Regression (regularized) | Simple | Medium | Low | High |
| Linear SVM / SGD hinge | Simple | Medium–High | Low | Medium–High |
| LDA | Simple | Medium | Low | Medium |
| Small MLP | Simple | Medium | Medium | Medium |
| Random Forest | Complex | High | Medium | High |
| RBF SVM | Complex | High | Medium–High | Medium |
| Gradient Boosting family | Complex | High | Medium–High | Medium–High |
| Medium MLP | Complex | Medium–High | Medium | Medium |
| Feature-encoder + linear head | Complex | High | High | Medium–High |

---

## Simple Base Learner Profiles

### Decision Tree (Depth 1–3)
- **Why it fits Learn++**: Very high diversity under data reweighting and resampling. The canonical base learner for boosting-style ensembles.
- **Cost**: Low training, low inference, low memory.
- **Strengths**: Captures simple nonlinear splits quickly. Highly interpretable.
- **Risks**: Unstable on noisy labels; can overreact to small batch artifacts.
- **Medical track note**: Useful on engineered features but limited on raw high-dimensional inputs.
- **Keep signal**: Consistent improvement in early incremental batches.
- **Drop signal**: High variance across runs and frequent minority-class collapse.

### Gaussian Naive Bayes
- **Why it fits Learn++**: Extremely fast baseline with clear probabilistic assumptions.
- **Cost**: Very low across all dimensions.
- **Strengths**: Robust first-pass benchmark. Surprisingly effective on compact feature sets.
- **Risks**: Feature independence assumption rarely holds; can cap quality.
- **Medical track note**: Works on compact summary features (radiomics).
- **Keep signal**: Stable baseline with strong cost advantage.
- **Drop signal**: Persistent underperformance even after feature cleanup.

### Logistic Regression (Strong Regularization)
- **Why it fits Learn++**: Linear baseline with controllable complexity.
- **Cost**: Low training and inference.
- **Strengths**: Predictable behavior, good calibration, interpretable coefficients.
- **Risks**: Cannot model nonlinear class boundaries.
- **Medical track note**: Effective when feature engineering captures relevant patterns.
- **Keep signal**: Good balance of interpretability and stable batch performance.
- **Drop signal**: Repeated inability to recover minority classes.

### Linear SVM / SGD Hinge
- **Why it fits Learn++**: Margin-based linear learner with robust baseline behavior.
- **Cost**: Low to medium, budget-friendly.
- **Strengths**: Often stronger than logistic regression in high-dimensional spaces.
- **Risks**: Sensitive to feature scale and class imbalance.
- **Medical track note**: Good for sparse or high-dimensional engineered representations.
- **Keep signal**: Robust macro metrics with moderate cost.
- **Drop signal**: Unstable minority recall or poor calibration.

### LDA
- **Why it fits Learn++**: Simple generative model, fast to fit.
- **Cost**: Low across all metrics.
- **Strengths**: Performs well when covariance assumptions hold approximately.
- **Risks**: Sensitive to violated assumptions, poor on complex boundaries.
- **Medical track note**: Quick signal check on structured feature sets.
- **Keep signal**: Competitive with other simple baselines at lower complexity.
- **Drop signal**: Degrades under modest data shift.

### Small MLP (1–2 hidden layers, < 100 units)
- **Why it fits Learn++**: Adds light nonlinearity while remaining low-capacity.
- **Cost**: Medium training, low–medium inference.
- **Strengths**: Captures interactions missed by linear models.
- **Risks**: Overfitting on small batches without careful regularization.
- **Medical track note**: Better with precomputed features than raw volume inputs.
- **Keep signal**: Improves class separation without large cost increase.
- **Drop signal**: Unstable learning curves across incremental batches.

---

## Complex Base Learner Profiles

### Random Forest
- **Why it's complex**: Nonlinear ensemble with robust generalization.
- **Cost**: Medium training, low–medium inference, medium memory.
- **Strengths**: Strong baseline quality, noise resilience, parallelizable training.
- **Risks**: Model size can pressure memory.
- **Medical track note**: Strong on radiomics and metadata-enriched features.
- **Keep signal**: High balanced accuracy with stable per-class recall.
- **Drop signal**: Incremental gains don't justify cost over simpler alternatives.

### RBF SVM
- **Why it's complex**: Flexible nonlinear decision boundaries.
- **Cost**: Medium to high, especially with larger datasets.
- **Strengths**: High quality after tuning. Well-understood theory.
- **Risks**: Scaling issues in training time and memory with dataset size.
- **Medical track note**: Useful when feature space is informative but not too large.
- **Keep signal**: Clear quality lift that justifies added compute.
- **Drop signal**: Repeated runtime bottlenecks under project budget.

### Gradient Boosting Family (XGBoost / LightGBM / CatBoost)
- **Why it's complex**: Top performer on tabular/engineered features.
- **Cost**: Medium to high depending on depth and estimator count.
- **Strengths**: Excellent ranking performance, flexible error reduction.
- **Risks**: Can overfit if complexity grows without control.
- **Medical track note**: Very competitive on engineered features plus metadata.
- **Keep signal**: Best composite score without severe cost violations.
- **Drop signal**: Unstable under incremental shift despite strong single-batch results.

### Medium MLP (2–3 hidden layers, 100–500 units)
- **Why it's complex**: Richer representation power than small MLP.
- **Cost**: Medium training and inference.
- **Strengths**: Handles moderate feature interactions effectively.
- **Risks**: Sensitive to regularization and data volume.
- **Medical track note**: Useful when feature pipeline captures clinically relevant patterns.
- **Keep signal**: Consistent improvement over simple linear baselines.
- **Drop signal**: Batch-to-batch instability and high tuning sensitivity.

### Feature-Encoder + Linear Head
- **Why it's complex**: Leverages rich representation learning.
- **Cost**: High training and setup, medium inference.
- **Strengths**: Strongest potential for imaging tasks with complex structure.
- **Risks**: Pipeline complexity and higher reproducibility burden.
- **Medical track note**: Strongest long-term option when feature extraction is mature.
- **Keep signal**: Meaningful quality lift on medical track with acceptable cost.
- **Drop signal**: Complexity overhead outweighs gains in Phase 1.

---

## Why Not Other Pairs?

| Alternative Pair | Why Not Selected |
|-----------------|------------------|
| Naive Bayes + Random Forest | Different model families — harder to isolate capacity as the variable |
| Linear SVM + RBF SVM | Same family (good), but SVM scaling issues on medical data add confounding factors |
| Small MLP + Medium MLP | Neural networks add tuning complexity and reproducibility burden beyond project scope |
| Logistic Regression + Feature-encoder | Too dissimilar — comparison would conflate capacity with architecture differences |

The full profiles above document all candidates in case the selected pair proves unsuitable and a fallback is needed.
