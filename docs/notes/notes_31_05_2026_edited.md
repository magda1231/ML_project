# Meeting Notes — 31 May 2026

Post-discussion action items and open questions (W. + colleague).

---

## Action Items

### 1. Run on original MNIST (digits 0–9)
Follow the original Polikar (2001) paper more closely — they used OCR/digit data. Create a **separate notebook** (copy of `01_learnpp_walkthrough.ipynb`) adapted for MNIST digits. Keep current Fashion-MNIST work as-is.

### 2. Verify PCA usage
Understand and be able to explain:
- What PCA does (dimensionality reduction via principal components)
- How it affects our results (noise removal, faster training, improved MLP accuracy)
- Whether it's a valid/appropriate optimization (is it "cheating" or scientifically sound preprocessing?)

### 3. Understand the metrics produced in synthetic validation
Be able to explain clearly (especially for presentation):
- **Macro F1**: harmonic mean of precision/recall, averaged equally across all classes
- **Balanced Accuracy**: average per-class recall (handles class imbalance)
- **Ensemble Size**: number of accumulated hypotheses (memory proxy)

### 4. Clarify Learn++ terminology
Know and be able to explain:
- **D_k**: the k-th data batch (e.g., D₁ = classes 0–3, D₂ = classes 4–6)
- **T_k**: number of base learners (hypotheses) trained per batch (currently T_k=10)

### 5. For Wojtek: re-read the paper
Read Polikar et al. (2001) thoroughly again. Focus on the algorithm details, not just the results.

### 6. Explain sklearn metrics imports
Understand what `balanced_accuracy_score` and `f1_score` compute under the hood. Be ready to explain during presentation.

### 7. Run on BraTS
Execute the same Learn++ comparison on BraTS MRI data (ROI features). Requires data access (Synapse registration).

### 8. Prepare presentation intro
Draft the opening section of the presentation (problem statement, motivation, approach).

### 9. Understand Wilcoxon Signed-Rank Test
Non-parametric statistical test for paired samples. We use it to determine if MLP vs DT performance difference is statistically significant (not just random variation across seeds).

---

## Open Questions

### Q0: Why group classes into batches instead of random mixing?

**Current design**: D₁ gets classes 0–3, D₂ gets 4–6, D₃ gets 7–9 (class introduction).

**Question**: What insight do we gain from grouping classes together vs. having each batch contain a random mix of all classes? Would running per-class experiments (one class per batch, 10 batches) reveal something different about how Learn++ handles individual classes?

**Partial answer**: The confusion matrix already shows per-class performance, but a per-class-batch design would show how Learn++ *learns* each class incrementally. This is a different question than "how well does it classify each class at the end."

### Q00: Should different seeds produce similar results?

**Question**: If we get very different results across seeds, does that mean the experiment is unstable/unreliable?

**Answer**: Yes — low variance across seeds = reproducible experiment. High variance = sensitive to random initialization. Our current results show low seed variance for DT (std=0.005 F1) and moderate for MLP (std=0.02 F1), which is acceptable.

### Q6 (expanded): Hypothesis imbalance in ensemble voting

**Concern**: After 3 batches, classes introduced in D₁ have been "seen" by 30 hypotheses, while classes from D₃ have only been seen by 10. Does the ensemble's weighted majority vote unfairly favor old classes?

**Short answer**: Yes, this IS a real limitation. The numerical advantage of old hypotheses (30 for old classes vs 10 for new classes) can drown out the new ones in voting — even though voting is weighted.

**Why weighting doesn't fully solve it**: Learn++ uses weighted voting (log(1/β_t)), so better classifiers get louder votes. In theory, a well-trained D₃ hypothesis could outweigh a mediocre D₁ one. In practice, 30 old hypotheses all confidently voting for class 0 will still overpower 10 new hypotheses voting for class 9 — the numbers matter.

**How to verify**: Compare per-class recall for classes 0–3 (seen since D₁, backed by 30 hypotheses) vs classes 7–9 (seen only from D₃, backed by 10 hypotheses). If old classes have significantly higher recall, this bias is real and measurable in our experiment.
