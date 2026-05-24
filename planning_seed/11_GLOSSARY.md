# Glossary

Single-source reference for all technical terminology. **Bold terms** are primary definitions. *Italic terms* are synonyms. → indicates cross-references.

---

## Project Terms

### Base Learner (*Component Classifier*)
The classifier plugged into Learn++ as the trainable unit at each iteration. Can be simple or complex.

> Earlier project versions used "weak classifier" and "strong classifier." This was reframed because those terms conflated model capacity with model performance.

→ Simple Base Learner, Complex Base Learner

### Batch (*Incremental Dataset*)
A sequential subset of training data. Denoted D₁, D₂, ..., Dₖ.

Example: D₁ = baseline MRI scans from Site A; D₂ = scans from Site B.

→ Incremental Learning, Learn++

### Complex Base Learner (*High-Capacity Classifier*)
A model with higher complexity, capacity, and expected quality. Typically requires more computation.

Examples: Random Forest, Gradient Boosting, RBF SVM, Medium MLP.

Previously called "strong classifier." "Complex" refers to MODEL CAPACITY, not necessarily superior performance.

→ Simple Base Learner

### CompositeScore (*Selection Score*, *Ranking Metric*)
Weighted combination of quality and cost metrics used to rank candidates after experiments complete.

Formula: `0.40·MacroF1 + 0.15·BalancedAccuracy + 0.15·(1-TrainTimeNorm) + 0.15·(1-InferenceTimeNorm) + 0.15·(1-MemoryNorm)`

This is NOT a training equation — it's a comparison tool.

→ MacroF1, Balanced Accuracy

### Composite Hypothesis (*Ensemble Prediction*)
Combined prediction from multiple base learners in Learn++. Denoted H_t(x) at iteration t, or H_final(x) across all batches.

→ Voting Weight

### Longlist
Complete set of candidate classifiers before filtering (6 simple + 5 complex = 11 models).

→ Shortlist

### Pairing (*Simple-Complex Pair*)
A selected combination of one simple and one complex base learner tested together. Reduces experiment count from 30 combinations to 3–4 pairs.

→ 05_CLASSIFIER_CATALOG.md

### Shortlist
Refined subset of candidates selected for experiments (3 simple + 3 complex = 6 models).

→ Longlist

### Simple Base Learner (*Low-Capacity Classifier*)
A model with intentionally limited complexity, designed for ensemble use. Fast and low-cost.

Examples: Decision Tree (depth 2–3), Naive Bayes, Logistic Regression, Linear SVM.

Previously called "weak classifier." "Simple" refers to MODEL SIMPLICITY, not poor performance.

→ Complex Base Learner

---

## Learn++ Terms

### Beta (β_t)
Normalized error for a base hypothesis at iteration t. $\beta_t = \epsilon_t / (1 - \epsilon_t)$. Controls voting weight via $\log(1/\beta_t)$.

### Distribution (D_t)
Normalized sample weights at iteration t. $D_t(i) = w_t(i) / \sum w_t(r)$. Emphasizes difficult samples.

### Epsilon (ε_t)
Weighted error of a base hypothesis. Must satisfy $\epsilon_t < 0.5$. If violated after MAX_RETRIES, skip iteration.

### Hypothesis
A single trained base classifier in the ensemble. Denoted h_t.

### Incremental Learning (*Continual Learning*)
Training paradigm where the model learns from sequential batches without retraining on all previous data.

### Sample Weights (w_t)
Unnormalized importance scores. Correctly classified samples get reduced weight; misclassified samples keep theirs.

### Voting Weight
Influence of a base hypothesis: $\log(1/\beta_t)$. Better hypotheses get higher weight.

---

## Evaluation Terms

### Balanced Accuracy
Mean of per-class recalls. Robust to class imbalance. Weight in CompositeScore: 0.15.

### MacroF1
Harmonic mean of precision and recall, averaged across classes. Weight in CompositeScore: 0.40.

### Precision
Among samples predicted as class C, what fraction actually are class C? TP / (TP + FP).

### Recall (*Sensitivity*)
Among samples actually in class C, what fraction did we correctly identify? TP / (TP + FN). Critical for medical imaging.

---

## Data Terms

### BIDS (*Brain Imaging Data Structure*)
Standardized folder organization for neuroimaging data. Hierarchical (sub-XX/ses-YY/modality/), JSON metadata, NIfTI volumes.

### Class Imbalance
Unequal sample distribution across classes. Mitigated by using MacroF1 and Balanced Accuracy.

### NIfTI (*Neuroimaging Informatics Technology Initiative*)
Standard file format for medical imaging volumes (.nii or .nii.gz). Contains 3D/4D voxel arrays + spatial metadata.

### Radiomics
Quantitative feature extraction from medical images (texture, shape, intensity patterns).

---

## Model Terms

### Decision Tree
Recursive binary splits. Simple role (depth 1–3). Key params: max_depth, criterion.

### Gradient Boosting
Sequential ensemble correcting previous errors. Complex role. Variants: XGBoost, LightGBM, CatBoost.

### Logistic Regression
Linear model with sigmoid activation. Simple role (with strong regularization).

### MLP (*Multi-Layer Perceptron*)
Feedforward neural network. Small MLP = simple role. Medium MLP = complex role.

### Naive Bayes
Probabilistic classifier assuming feature independence. Gaussian variant used here. Simple role.

### Random Forest
Ensemble of bootstrapped decision trees with random feature subsets. Complex role.

### SVM (*Support Vector Machine*)
Margin-maximizing classifier. Linear SVM = simple role. RBF SVM = complex role.

---

## Common Confusions

| Confusion | Clarification |
|-----------|--------------|
| "Simple" means bad | No — "simple" refers to model capacity, not performance. Simple base learners are effective in Learn++ |
| "Complex" means always better | No — complex models cost more and may overfit. The comparison determines if the cost is justified |
| CompositeScore trains the model | No — CompositeScore ranks models AFTER training. It's a selection tool, not a loss function |
| Learn++ = boosting | Related but distinct. Learn++ extends boosting to incremental settings where new data batches arrive sequentially |
