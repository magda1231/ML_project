# Glossary of Terms

## Purpose
Single-source reference for all technical terminology used in Learn++ project documentation.

## How to Use
- **Bold terms** are primary definitions used throughout the project
- *Italic terms* are synonyms or alternative names
- → symbol indicates cross-references to related terms

---

## Project-Specific Terms

### Batch (*Dataset*, *Incremental Dataset*)
A sequential subset of training data in incremental learning. Denoted as D₁, D₂, ..., Dₖ.

**Example**: D₁ contains baseline MRI scans from Site A; D₂ contains scans from Site B.

**Related**: → Incremental Learning, → Learn++

---

### CompositeScore (*Selection Score*, *Ranking Metric*)
A weighted combination of quality and cost metrics used to rank classifier candidates **after experiments complete**.

**Formula**:
```
CompositeScore = 0.50·MacroF1 + 0.20·BalancedAccuracy
                + 0.10·(1-TrainTimeNorm)
                + 0.10·(1-InferenceTimeNorm)
                + 0.10·(1-MemoryNorm)
```

**Important**: This is NOT a classifier training equation; it's a comparison tool.

**Related**: → MacroF1, → Balanced Accuracy

---

### Composite Hypothesis (*Ensemble Prediction*)
The combined prediction from multiple weak learners in Learn++. Denoted as Hₜ(x) at iteration t, or H_final(x) across all batches.

**How it works**: Each weak learner votes for a class, weighted by its performance. The class with highest weighted vote wins.

**Related**: → Weak Hypothesis, → Voting Weight

---

### Longlist
The complete set of candidate classifiers before filtering (6 weak + 5 strong = 11 models in this project).

**Related**: → Shortlist, → Weak Classifier, → Strong Classifier

---

### Pairing (*Weak-Strong Pair*)
A selected combination of one weak and one strong classifier tested together to enable fair comparison.

**Example**: Decision Tree (weak) + Gradient Boosting (strong).

**Why used**: Reduces experiment matrix from 30 combinations to 6-9 focused experiments.

**Related**: → CLASSIFIER_PAIRING_STRATEGY.md

---

### Shortlist
The refined subset of candidates selected for actual experiments (3 weak + 3 strong = 6 models in this project).

**Selection criteria**: Based on preliminary analysis, cost constraints, and interpretability needs.

**Related**: → Longlist

---

### Strong Classifier (*High-Capacity Classifier*)
A model with higher complexity, capacity, and expected performance compared to weak classifiers. Typically requires more computation.

**Examples**: Random Forest, Gradient Boosting, RBF SVM, Medium MLP.

**⚠️ Terminology note**: "Strong" refers to MODEL CAPACITY, not to whether it performs well. A strong classifier can still perform poorly if misapplied.

**Related**: → Weak Classifier

---

### Weak Classifier (*Low-Complexity Classifier*, *Base Learner*)
A model with intentionally limited complexity designed for ensemble use. Typically fast and simple.

**Examples**: Decision Tree (depth 2-3), Naive Bayes, Logistic Regression, Linear SVM.

**⚠️ Terminology note**: "Weak" refers to MODEL SIMPLICITY, not to poor performance. Weak classifiers are effective in Learn++ ensembles.

**Related**: → Strong Classifier, → Learn++

---

## Learn++ Algorithm Terms

### Beta (βₜ)
Normalized error term for a weak hypothesis at iteration t.

**Formula**: βₜ = εₜ / (1 - εₜ)

**Range**: 0 < βₜ < 1 (when εₜ < 0.5)

**Purpose**: Controls voting weight via log(1/βₜ). Smaller βₜ = better hypothesis = stronger vote.

**Related**: → Epsilon, → Voting Weight

---

### Distribution (Dₜ)
Normalized sample weights at iteration t, forming a probability distribution over training samples.

**Formula**: Dₜ(i) = wₜ(i) / Σwₜ(r)

**Purpose**: Emphasizes difficult samples for next weak learner.

**Related**: → Sample Weights

---

### Epsilon (εₜ)
Weighted error of a weak hypothesis at iteration t.

**Formula**: εₜ = Σ Dₜ(i) for all misclassified samples i

**Constraint**: Must satisfy εₜ < 0.5 (better than random guessing for binary case).

**What happens if εₜ ≥ 0.5**: Hypothesis is rejected and resampling occurs (up to MAX_RETRIES attempts).

**Related**: → Beta, → Weak Hypothesis

---

### Hypothesis (*Weak Hypothesis*, *Base Model*)
A single trained weak classifier in the Learn++ ensemble. Denoted as hₜ.

**Lifecycle**: Train on weighted samples → Compute εₜ → Accept if εₜ < 0.5 → Assign voting weight → Add to ensemble.

**Related**: → Composite Hypothesis

---

### Incremental Learning (*Online Learning*, *Continual Learning*)
Training paradigm where model learns from sequential data batches without retraining from scratch on all previous data.

**Learn++ approach**: Accumulate weak hypotheses from each batch; final prediction aggregates votes from all past hypotheses.

**Related**: → Batch, → Learn++

---

### Sample Weights (wₜ)
Unnormalized importance scores for training samples at iteration t.

**Update rule**:
- Correctly classified: wₜ₊₁(i) = wₜ(i) · Bₜ (reduce weight)
- Misclassified: wₜ₊₁(i) = wₜ(i) (keep weight)

**Effect**: Misclassified samples become relatively more important after normalization.

**Related**: → Distribution

---

### Voting Weight
The influence of a weak hypothesis in the ensemble vote.

**Formula**: log(1/βₜ) for hypothesis t

**Interpretation**: Better hypotheses (lower βₜ) get exponentially higher voting weights.

**Related**: → Beta, → Composite Hypothesis

---

## Evaluation Metrics

### Balanced Accuracy (*Average Recall*)
Mean of per-class recalls. Robust to class imbalance.

**Formula**: (Recall_class1 + Recall_class2 + ... + Recall_classC) / C

**When to use**: Required when classes are imbalanced.

**Weight in CompositeScore**: 0.20

**Related**: → MacroF1, → Recall

---

### MacroF1 (*Macro-averaged F1*)
Harmonic mean of precision and recall, averaged across classes.

**Formula**: Average of per-class F1 scores

**Why preferred**: Class-balanced, punishes poor minority-class performance.

**Weight in CompositeScore**: 0.50 (highest weight)

**Related**: → Balanced Accuracy

---

### Precision
Among samples predicted as class C, what fraction actually are class C?

**Formula**: TP / (TP + FP)

**Related**: → Recall, → F1

---

### Recall (*Sensitivity*, *True Positive Rate*)
Among samples actually in class C, what fraction did we correctly identify?

**Formula**: TP / (TP + FN)

**Critical for**: Medical imaging (missing a disease is costly).

**Related**: → Precision, → Balanced Accuracy

---

## Data and Experiment Terms

### BIDS (*Brain Imaging Data Structure*)
Standardized organization format for neuroimaging data.

**Key features**: Hierarchical folders (sub-XX/ses-YY/modality/), JSON metadata, NIFTI volumes.

**Why important**: Enables reproducible pipelines and metadata-driven batch design.

**Project priority**: Primary format for medical imaging track.

**Related**: → NIfTI

---

### Class Imbalance
Unequal distribution of samples across classes.

**Problem**: Standard accuracy can be misleading (99% accuracy if 99% of samples are class A).

**Mitigation in this project**: Use MacroF1 and Balanced Accuracy, avoid plain accuracy.

**Related**: → MacroF1, → Balanced Accuracy

---

### Feature Engineering
Process of creating informative features from raw data.

**For medical imaging**: Radiomics features (texture, shape, intensity statistics) or deep learning embeddings.

**Project approach**: Keep feature strategy explicit and swappable in architecture.

**Related**: → Radiomics

---

### NIfTI (*Neuroimaging Informatics Technology Initiative*)
Standard file format for medical imaging volumes (.nii or .nii.gz).

**Contains**: 3D/4D array of voxel values + header with spatial metadata.

**Project priority**: Required for medical imaging track.

**Related**: → BIDS

---

### Radiomics
Extraction of quantitative features from medical images (texture, shape, intensity patterns).

**Examples**: Haralick texture features, Gray-Level Co-occurrence Matrix (GLCM), morphological features.

**Use in this project**: Potential feature extraction strategy for weak classifiers on medical imaging.

**Related**: → Feature Engineering

---

### Train/Validation/Test Split
Division of data into non-overlapping subsets for model training, hyperparameter tuning, and final evaluation.

**Critical**: Must be fixed before experiments start with documented random seed.

**Project requirement**: Document split strategy in IMPLEMENTATION_HANDOFF.md.

**Related**: → Reproducibility

---

## Machine Learning Model Terms

### Decision Tree
Hierarchical model that makes predictions via recursive binary splits.

**Learn++ role**: Weak classifier (depth limited to 1-3).

**Key parameters**: Max depth, split criterion (Gini, Entropy).

**Pros**: Fast, interpretable.

**Cons**: High variance, unstable.

**Related**: → Random Forest, → Gradient Boosting

---

### Gradient Boosting
Ensemble method that sequentially adds weak learners to correct previous errors.

**Variants**: XGBoost, LightGBM, CatBoost.

**Learn++ role**: Strong classifier candidate.

**Key parameters**: Number of estimators, learning rate, max depth.

**Pros**: High quality, handles complex patterns.

**Cons**: Moderate to high computational cost.

---

### Logistic Regression
Linear model with sigmoid activation for binary classification (extendable to multi-class).

**Learn++ role**: Weak classifier (with strong regularization).

**Key parameters**: Regularization strength (C or λ).

**Pros**: Fast, interpretable, well-calibrated probabilities.

**Cons**: Linear decision boundary only.

---

### MLP (*Multi-Layer Perceptron*)
Feedforward neural network with one or more hidden layers.

**Learn++ roles**:
- Small MLP: Weak classifier (1-2 hidden layers, <100 units)
- Medium MLP: Strong classifier (2-3 hidden layers, 100-500 units)

**Key parameters**: Layer sizes, activation functions, dropout rate.

**Pros**: Learns nonlinear patterns.

**Cons**: Needs tuning, risk of overfitting.

---

### Naive Bayes
Probabilistic classifier assuming feature independence given class.

**Variant used**: Gaussian Naive Bayes (assumes features are Gaussian-distributed).

**Learn++ role**: Weak classifier (ultra-fast baseline).

**Pros**: Extremely fast, works surprisingly well despite strong assumptions.

**Cons**: Assumption violations can limit performance.

---

### Random Forest
Ensemble of decision trees trained on bootstrapped samples with random feature subsets.

**Learn++ role**: Strong classifier candidate.

**Key parameters**: Number of trees, max depth, max features.

**Pros**: Robust, handles noise well, parallel training.

**Cons**: Larger model size, moderate inference cost.

**Related**: → Decision Tree

---

### SVM (*Support Vector Machine*)
Margin-maximizing classifier with optional kernel transformations.

**Variants**:
- Linear SVM: Weak classifier (linear decision boundary)
- RBF SVM: Strong classifier (nonlinear boundary via Gaussian kernel)

**Key parameters**: C (regularization), gamma (kernel width for RBF).

**Pros**: Effective in high dimensions, strong theoretical foundation.

**Cons**: Scaling issues with large datasets (especially RBF).

---

## Project Management Terms

### Exit Gate
Set of criteria that must be met before proceeding to next project phase.

**Example (Phase 1 → Phase 2)**:
- Classifier choices approved and justified
- Learn++ flow documented
- Risks and assumptions updated

**Related**: → Phase

---

### Phase
Major project stage with defined objectives, deliverables, and exit gates.

**This project**:
- Phase 1: Classifier selection and documentation
- Phase 2: Medical imaging implementation
- Phase 3A-C: Expansion to drone/photo recognition, mapping, swarm control

**Related**: → PHASED_ROADMAP.md

---

### Reproducibility
Ability to replicate experimental results given same data, code, and parameters.

**Requirements**: Fixed random seeds, versioned dependencies, documented hyperparameters, tracked data versions.

**Project emphasis**: Critical for Phase 2 implementation and publication.

---

## Common Confusions to Avoid

### "Strong classifier" vs "strong performance"
- ✅ **Correct**: "Strong classifier" = high-capacity model (Random Forest, Gradient Boosting)
- ❌ **Incorrect**: "Strong classifier" = any model that performs well

### "Weak classifier" vs "weak performance"
- ✅ **Correct**: "Weak classifier" = low-complexity model (Decision Tree, Naive Bayes)
- ❌ **Incorrect**: "Weak classifier" = any model that performs poorly

### CompositeScore vs classifier equations
- ✅ **Correct**: CompositeScore ranks experiments; classifier equations describe training
- ❌ **Incorrect**: CompositeScore is part of model training

### Batch vs mini-batch
- ✅ **Correct**: In this project, "batch" = incremental dataset (D₁, D₂, D₃)
- ❌ **Incorrect**: "Batch" = mini-batch in gradient descent (unrelated here)

---

## Acronyms

- **BIDS**: Brain Imaging Data Structure
- **GLCM**: Gray-Level Co-occurrence Matrix
- **LDA**: Linear Discriminant Analysis
- **MLP**: Multi-Layer Perceptron
- **MRI**: Magnetic Resonance Imaging
- **NIfTI**: Neuroimaging Informatics Technology Initiative
- **RBF**: Radial Basis Function
- **SGD**: Stochastic Gradient Descent
- **SVM**: Support Vector Machine

---

## References to Full Documentation

- **Learn++ algorithm details**: [LEARNPP_EQUATION_FLOW_MAP.md](LEARNPP_EQUATION_FLOW_MAP.md)
- **All equation definitions**: [CLASSIFIER_EQUATIONS_DETAILED.md](CLASSIFIER_EQUATIONS_DETAILED.md)
- **Classifier profiles**: [CLASSIFIER_PROFILES.md](CLASSIFIER_PROFILES.md)
- **Pairing strategy**: [CLASSIFIER_PAIRING_STRATEGY.md](CLASSIFIER_PAIRING_STRATEGY.md)
- **Project phases**: [PHASED_ROADMAP.md](PHASED_ROADMAP.md)
