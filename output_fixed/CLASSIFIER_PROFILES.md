# Classifier Profiles for Learn++ Planning

## Purpose
This file gives detailed, implementation-free profiles for classifier candidates considered in this project.

## How to Use
1. Use this document to understand each candidate role in Learn++.
2. Use the keep or drop signals to refine longlist to shortlist.
3. Use the cost notes to avoid candidates that violate practical constraints.

## Candidate Overview
| Candidate | Intended Role | Quality Potential | Compute Cost | Stability Across Batches |
|---|---|---|---|---|
| Decision Tree (depth 1-3) | Weak | Medium | Low | Medium |
| Gaussian Naive Bayes | Weak | Low to Medium | Very low | Medium |
| Logistic Regression (regularized) | Weak | Medium | Low | High |
| Linear SVM or SGD hinge | Weak | Medium to High | Low | Medium to High |
| LDA | Weak | Medium | Low | Medium |
| Small MLP | Weak | Medium | Medium | Medium |
| Random Forest | Strong | High | Medium | High |
| RBF SVM | Strong | High | Medium to High | Medium |
| Gradient Boosting family | Strong | High | Medium to High | Medium to High |
| Medium MLP | Strong | Medium to High | Medium | Medium |
| Feature encoder plus linear head | Strong | High | High | Medium to High |

## Weak Candidate Profiles

### Decision Tree (Depth 1-3)
1. Why it fits Learn++: very high diversity under data reweighting and re-sampling.
2. Cost profile: low training cost, low inference cost, low memory footprint.
3. Typical strengths: captures simple nonlinear splits quickly.
4. Typical risks: unstable on noisy labels; can overreact to small batch artifacts.
5. Medical NIfTI or BIDS note: useful on engineered features but weak on raw high-dimensional inputs.
6. Keep signal: gives consistent improvement in early incremental batches.
7. Drop signal: high variance across runs and frequent minority-class collapse.

### Gaussian Naive Bayes
1. Why it fits Learn++: extremely fast baseline with clear probabilistic assumptions.
2. Cost profile: very low in all cost dimensions.
3. Typical strengths: robust first-pass benchmark for many feature spaces.
4. Typical risks: assumption mismatch can cap quality.
5. Medical NIfTI or BIDS note: can work surprisingly well on compact summary features.
6. Keep signal: stable baseline and strong cost advantage.
7. Drop signal: persistent underperformance on key classes even after feature cleanup.

### Logistic Regression (Strong Regularization)
1. Why it fits Learn++: linear baseline with controllable complexity.
2. Cost profile: low training and inference cost.
3. Typical strengths: predictable behavior and good calibration trends.
4. Typical risks: underfitting for complex class boundaries.
5. Medical NIfTI or BIDS note: effective when feature engineering captures relevant patterns.
6. Keep signal: good balance of interpretability and stable batch performance.
7. Drop signal: repeated inability to recover minority classes.

### Linear SVM or SGD Hinge
1. Why it fits Learn++: margin-based linear learner with strong baseline behavior.
2. Cost profile: low to medium, usually still budget-friendly.
3. Typical strengths: often stronger than plain logistic in high-dimensional spaces.
4. Typical risks: sensitive to feature scale and class imbalance handling.
5. Medical NIfTI or BIDS note: good choice for sparse or high-dimensional engineered representations.
6. Keep signal: robust macro metric performance with moderate cost.
7. Drop signal: unstable minority recall or poor calibration needs.

### LDA
1. Why it fits Learn++: simple generative model with low cost.
2. Cost profile: low across metrics.
3. Typical strengths: can perform well when covariance assumptions are approximately true.
4. Typical risks: assumption sensitivity and weaker performance on complex boundaries.
5. Medical NIfTI or BIDS note: useful for quick signal checks on structured feature sets.
6. Keep signal: competitive with other weak baselines at lower complexity.
7. Drop signal: strong degradation under modest data shift.

### Small MLP
1. Why it fits Learn++: adds light nonlinearity while remaining weaker than larger nets.
2. Cost profile: medium training cost, low to medium inference cost.
3. Typical strengths: can capture interactions missed by linear models.
4. Typical risks: overfitting on small batches without careful regularization.
5. Medical NIfTI or BIDS note: better with stable, precomputed features than with raw volume inputs in this phase.
6. Keep signal: improves class separation without large cost jump.
7. Drop signal: unstable learning curves across incremental batches.

## Strong Candidate Profiles

### Random Forest
1. Why it is strong: nonlinear ensemble with robust generalization on many structured feature sets.
2. Cost profile: medium training cost, low to medium inference cost, medium memory.
3. Typical strengths: strong baseline quality and resilience to noise.
4. Typical risks: model size growth can pressure memory.
5. Medical NIfTI or BIDS note: often strong on radiomics and metadata-enriched features.
6. Keep signal: high balanced accuracy with stable per-class recall.
7. Drop signal: weak incremental gains compared with cheaper alternatives.

### RBF SVM
1. Why it is strong: flexible nonlinear decision boundaries and high quality potential.
2. Cost profile: medium to high, especially with larger datasets.
3. Typical strengths: often strong top-line performance after tuning.
4. Typical risks: scaling issues in training time and memory.
5. Medical NIfTI or BIDS note: useful when feature space is informative but not too large.
6. Keep signal: clear quality lift that justifies added compute.
7. Drop signal: repeated runtime bottlenecks under project budget.

### Gradient Boosting Family
1. Why it is strong: high-quality performance on tabular and engineered feature tasks.
2. Cost profile: medium to high depending on depth and number of estimators.
3. Typical strengths: strong ranking performance and flexible error reduction.
4. Typical risks: can overfit if complexity grows without control.
5. Medical NIfTI or BIDS note: very competitive on engineered features plus metadata.
6. Keep signal: best composite score without severe cost violations.
7. Drop signal: unstable performance under incremental shift despite strong single-batch results.

### Medium MLP
1. Why it is strong: richer representation power than small MLP.
2. Cost profile: medium training and inference, medium memory.
3. Typical strengths: handles moderate feature interactions effectively.
4. Typical risks: sensitive to regularization and data volume.
5. Medical NIfTI or BIDS note: useful when feature pipeline captures clinically relevant patterns.
6. Keep signal: consistent improvement over weak linear baselines.
7. Drop signal: batch-to-batch instability and high tuning sensitivity.

### Feature Encoder Plus Linear Head
1. Why it is strong: leverages richer representation learning while keeping classifier head simple.
2. Cost profile: high training and setup cost, medium inference depending on encoder size.
3. Typical strengths: strong potential for imaging tasks with complex structure.
4. Typical risks: added pipeline complexity and higher reproducibility burden.
5. Medical NIfTI or BIDS note: strongest long-term option when feature extraction is mature.
6. Keep signal: meaningful quality lift on medical track with acceptable operational cost.
7. Drop signal: complexity overhead outweighs gains in current project phase.

## Practical Downselection Advice
1. Keep at least one ultra-cheap baseline in final comparison for cost anchoring.
2. Prefer candidates that remain stable across incremental batches, not only in static evaluation.
3. Reject models that win single-batch scores but fail minority classes in later batches.
4. Favor models with transparent failure modes for easier debugging in implementation phase.
